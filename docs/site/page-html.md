# Page HTML — code, SEO and structured data, as typed items

`document.json` (named by `manifest.document`) holds every piece of code and SEO the site puts into its pages,
as **one list of `items`, each of one type** — a title, meta tags, structured data, a pasted snippet, CSS, JS, a
script file, text for crawlers, or a function that works values out. Each is for every page or the paths it
names, maybe only when a condition holds, maybe with API data of its own; each **runs** in the page's HTML
(written by the host's server, so search engines and link previews see it) or in the browser.

```json
// manifest.json
"seo": { "titleTemplate": "%s · {{ global.shop.name }}", "siteUrl": "https://shop.example.com" },
"stateKeys": { "shop": { "from": { "api": "site.get", "keep": 3600 } } },
"document": "document.json"
```

```json
// document.json
{
  "items": [
    { "id": "tags", "name": "Shop tags", "type": "meta", "meta": { "og:site_name": "{{ global.shop.name }}", "theme-color": "#131921" } },
    { "id": "gtag", "name": "Google Analytics", "type": "script", "src": "https://www.googletagmanager.com/gtag/js?id=G-XXXX", "attrs": { "async": true }, "run": "once" },
    { "id": "views", "name": "Count page views", "type": "js", "run": "visit", "where": "bodyEnd", "code": "gtag('event', 'page_view', { page_path: location.pathname })" },
    { "id": "print", "name": "Print", "type": "css", "code": "@media print { header { display: none } }" },
    { "id": "private", "name": "Private pages", "type": "seo", "paths": ["/cart", "/checkout", "/account/*"], "noindex": true, "canonical": false },
    { "id": "product", "name": "Product", "type": "jsonld", "paths": ["/products/:slug"],
      "jsonld": { "@context": "https://schema.org", "@type": "Product", "name": "{{ product.name }}", "image": "{{ product.images }}",
        "offers": { "@type": "Offer", "price": "{{ product.price }}", "priceCurrency": "{{ site.currency }}",
          "availability": "{{ product.inStock | then: 'https://schema.org/InStock' | default: 'https://schema.org/OutOfStock' }}" } } },
    { "id": "gone", "name": "Missing product", "type": "seo", "paths": ["/products/:slug"], "if": "!product", "status": 404, "title": "Product not found" },
    { "id": "deal", "name": "Deal in the preview", "type": "function", "paths": ["/products/:slug"], "if": "product.compareAt",
      "code": "export default ({ data, site }) => ({ description: `Now ${site.currency} ${data.product.price}, was ${data.product.compareAt}` })" }
  ]
}
```

## Types

| `type` | What | Its keys |
| --- | --- | --- |
| `seo` | Title, description, share image, canonical, hide from search, language, status | `title`, `description`, `image`, `canonical` (`false`: none), `noindex`, `lang`, `status` (404, 410…) |
| `meta` | Meta tags. `og:*`, `article:*`, `product:*`… are written as `property`, the rest as `name` | `meta`: name → content; a list writes one tag each; `null` drops one set before |
| `jsonld` | Structured data (schema.org), each as `<script type="application/ld+json">` | `jsonld`: one object or a list. Values a binding didn't find are left out |
| `html` | Any snippet, pasted as it is — analytics, pixels, widgets, `<link>`, `<noscript>` | `code`, `where` |
| `css` | Styles, written as a `<style>` in `<head>` | `code` |
| `js` | Code written as a `<script>`. A binding in it is a JS value: `{{ product.name }}` → `"Kettle"` (quoted) | `code`, `where` |
| `script` | A script file by its address | `src`, `attrs` (`async`, `defer`, `type: "module"`, `crossorigin`…), `where` |
| `root` | HTML inside the site's element (`#root`) until the site draws — text crawlers read without running scripts | `code` |
| `function` | Code that calls APIs and works values out, then returns what to add | `code`: an ES module, below |

**Every item** has `id`, `name`, `type`, and:

| Key | What |
| --- | --- |
| `paths` | Route patterns (`/products/:slug`), addresses (`/about`) or prefixes (`/account/*`) it's for; none: every page |
| `if` | Only when this holds, once the data is in: `"!product"`, `"product.stock == 0"` — the [conditions](views.md) blocks use |
| `data` | Data it needs beside the page's own: name → API call (`{ "api", "params", "keep" }`) |
| `enabled` | `false`: kept, not used |
| `run` | `html`, `css`, `js`, `script`, `function`: **`html`** (default) — in the page's HTML, written by your server, runs as the page loads, search engines see it; **`once`** — added by the site in the browser the first time a page it's for opens, then kept (a library, a widget); **`visit`** — added each time a page it's for opens and removed when it closes (a page-view count). `seo`, `meta`, `jsonld` and `root` are always `html` |
| `where` | `html`, `js`, `script`: `head` (default) · `bodyStart` · `bodyEnd` |

Items apply **in order**: a later one wins a title, a description or a meta tag; code, meta and structured data
add up. For the title, description, image and noindex: an item **with paths** wins over the route's own
(`title` in the manifest's route, or the page's `seo`), and the route wins over an item **for every page** (a
site default). `seo.titleTemplate` wraps the title. Unset canonical: the site address + the path, no query.

Written on every page, then changed by `meta`: `description`, `og:title`, `og:description`, `og:image`,
`og:url` (the canonical), `og:type` (`website`), `og:site_name`, `twitter:card` and, with `noindex`, `robots`.

**Status.** 200; **404** for an address no route shows or the not-found route (`*`); **301 / 302** for the site's
`redirects`; or an `seo` item's `status`. An error page (400 and up) is always `noindex`, with no canonical and
no structured data.

## Values — the same bindings as pages

Everything binds like a page: `{{ site.* }}` (with `site.url`, the site's address), `{{ params.* }}`,
`{{ query.* }}`, `{{ global.* }}` and the page's data by name. **Nothing is written twice:**

- **The page's own data.** `{{ product.name }}` on `/products/:slug` reads the `product` the page loads — the
  server makes the same call.
- **Live site details** — a name, logo, phone, social links from your API — are a site-wide state loaded from it
  (`stateKeys.shop.from`, [states.md](states.md)): `{{ global.shop.logo }}` in the HTML and on the page alike.
- **`data` on an item** for what only it needs. A source can read another's answer (`{{ product.id }}`); it
  loads after it.

On the server only what the bindings read is loaded; `keep` (or a state key's `ttl`) keeps an answer that many
seconds. An answer that fails or is slow (3 s) is left out, and the page still goes out. Bound values are made
safe for where they land: escaped in HTML, plain in CSS, JS values in JS; HTML a function returns is its own.

## Functions

```js
// Gets the page's values; returns any of: title, description, image, canonical, noindex, status, meta, jsonld,
// head, bodyStart, bodyEnd, root — added like items of their kinds.
export default async ({ site, params, query, path, global, data, api, fetch }) => {
  const { data: stock } = await api.call("stock.get", { params: { id: data.product.id } })
  return { meta: { "product:availability": stock.left ? "in stock" : "out of stock" } }
}
```

`data` holds the page's data and the item's own, by name; `api.call` is the site's registry; `fetch` the
platform's. In the page HTML, **a function runs only where the host lets it** (`pageDocument`'s
`runFunctions` — it runs with the server's access, so only for trusted sites); elsewhere it's skipped and named
in `problems`. The browser runs page-HTML functions too, for the SEO of the pages that follow, and runs `once` /
`visit` functions, adding what they return.

## In the browser

The renderer reads the same file. The first page keeps the tags the server wrote (marked `data-syn-seo`); from
the next page on, the site writes the same SEO itself — title, description, image, canonical, meta and
structured data, from what the page loaded (an item's own `data` is the server's only). `html`, `css`, `js`,
`script` and `root` items that run in the page HTML run once, as it loads; `once` and `visit` items are the
browser's own (they load their `data` first). The window event `synui:navigate` (`detail.path`) also fires on
each page change.

## In the builder

- **Code** (left): **Every page & path rules** — the items for every page and the ones that name paths; **On
  one page** — each page's own (open that page to edit them); the CSS file and site scripts; **Page scripts**,
  every page's; and **What your server writes** for any address.
- **Page tab** (right), **Code & SEO**: the open page's own items (their paths fixed to it), what else runs
  there (click to edit it in Code), and the page script.

Each item has a **Type** picked from one list, then For, Runs, Goes, its type's fields, Only when and Data it
reads. Checks flag a file that doesn't parse, body tags in head HTML, unknown APIs, a script with no address,
paths that don't start with `/`, and functions that need the host's permission.

**Older forms** still work, read as items: the 2.1.0 file (the same keys at its top level and in `paths`
entries), the manifest's inline `document` (`head`, `bodyEnd`) and `inject` snippets (every page: `once`;
chosen pages: `visit`). The builder writes them back as one file of items the first time you change one.
