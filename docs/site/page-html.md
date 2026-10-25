# Page HTML — SEO, structured data and code for each page

`document.json` (named by `manifest.document`) is what the **host's server writes into each page's HTML**
before sending it: the title, search and link-preview tags, structured data (schema.org JSON-LD), and any code
— for every page, or chosen paths. It is there before any script runs, so search engines, link previews
(WhatsApp, Facebook, X, Slack), site-verification checks and crawlers that never run JavaScript all read it.
Values can come live from your APIs.

```json
// manifest.json
"seo": { "titleTemplate": "%s · {{ global.shop.name }}", "siteUrl": "https://shop.example.com" },
"stateKeys": { "shop": { "from": { "api": "site.get", "keep": 3600 } } },
"document": "document.json"
```

```json
// document.json
{
  "meta": { "og:site_name": "{{ global.shop.name }}", "twitter:site": "@shop", "theme-color": "#131921" },
  "head": "<link rel=\"icon\" href=\"{{ global.shop.logo }}\">\n<script async src=\"https://www.googletagmanager.com/gtag/js?id=G-XXXX\"></script>",
  "bodyStart": "<noscript><iframe src=\"https://www.googletagmanager.com/ns.html?id=GTM-XXXX\" height=\"0\" width=\"0\" style=\"display:none\"></iframe></noscript>",
  "bodyEnd": "<noscript>Turn on JavaScript to shop here.</noscript>",
  "paths": [
    { "path": "/", "jsonld": { "@context": "https://schema.org", "@type": "Organization", "name": "{{ global.shop.name }}", "logo": "{{ global.shop.logo }}", "url": "{{ site.url }}" } },
    { "path": "/products/:slug",
      "meta": { "og:type": "product", "product:price:amount": "{{ product.price }}" },
      "jsonld": { "@context": "https://schema.org", "@type": "Product", "name": "{{ product.name }}", "image": "{{ product.images }}",
        "offers": { "@type": "Offer", "price": "{{ product.price }}", "priceCurrency": "{{ site.currency }}",
          "availability": "{{ product.inStock | then: 'https://schema.org/InStock' | default: 'https://schema.org/OutOfStock' }}" } },
      "root": "<h1>{{ product.name }}</h1><p>{{ product.description }}</p>" },
    { "path": "/products/:slug", "if": "!product", "status": 404, "title": "Product not found" },
    { "path": "/search", "meta": { "robots": "noindex, follow" }, "canonical": "{{ site.url }}/search" },
    { "path": ["/cart", "/checkout", "/account/*"], "noindex": true, "canonical": false }
  ]
}
```

## How a page's HTML is made

The **top level is every page**. Each entry of **`paths`** whose `path` matches the address adds to it, in order:
a later entry wins a title, a description or a meta tag; code, meta and structured data add up. A `path` is a
route pattern (`/products/:slug`), an exact address (`/about`), a prefix ending in `*` (`/account/*`) — or a
list of them.

| Key | Where | What |
| --- | --- | --- |
| `title` | both | The title; the site's `seo.titleTemplate` wraps it. Unset: the route's `title`, the page's `seo.title`, the site's name |
| `description`, `image` | both | Unset: the route's, the page's `seo`, then the site's `seo`. A path-only `image` gets the site address in front |
| `canonical` | both | The page's one true address; `false` writes none. Unset: the site address + the path, without the query |
| `noindex` | both | Keep search engines out (`<meta name="robots" content="noindex">`) |
| `lang` | both | `<html lang>`; unset: the site's `locale` |
| `meta` | both | `name` or `property` → content. `og:*`, `article:*`, `product:*`… are written as `property`, the rest as `name`. A list writes one tag each (several `og:image`s); `null` drops one set before |
| `jsonld` | both | Structured data, one object or a list, each written as `<script type="application/ld+json">`. Values a binding didn't find are left out, and an object left with nothing is dropped |
| `head` | both | Any HTML at the end of `<head>`: meta, link, script, style, noscript |
| `bodyStart` | both | Any HTML right after `<body>` (a tag manager's `<noscript>` frame) |
| `bodyEnd` | both | Any HTML just before `</body>`: chat widgets, deferred scripts |
| `root` | both | HTML inside the site's element (`#root`) until the site draws over it — text for crawlers that don't run scripts. The last entry's wins |
| `path` | entry | What the entry matches (above) |
| `if` | entry | Only when this holds, once the data is in: `"!product"`, `"product.stock == 0"` — the [conditions](views.md) blocks use |
| `status` | entry | The answer's status: `404`, `410`… |
| `data` | entry | Data this path needs beside the page's own: name → API call (`{ "api", "params", "keep" }`). Server only |

Written for you on every page, then changed by `meta`: `description`, `og:title`, `og:description`, `og:image`,
`og:url` (the canonical), `og:type` (`website`), `og:site_name`, `twitter:card` (`summary_large_image` with an
image) and, with `noindex`, `robots`.

**Status.** 200; **404** for an address no route shows or the not-found route (`*`); **301 / 302** for the
site's `redirects`; or an entry's `status`. An error page (400 and up) is always `noindex`, with no canonical
and no structured data.

## Values — the same bindings as pages

Everything binds like a page: `{{ site.* }}` (with `site.url`, the site's address), `{{ params.* }}`,
`{{ query.* }}`, `{{ global.* }}` and the page's data by name. **Nothing is written twice:**

- **The page's own data.** `{{ product.name }}` on `/products/:slug` reads the `product` the page file loads —
  the server makes the same call. A route title of `{{ product.name }}` works in the browser and in the HTML.
- **Live site details** — a name, logo, phone, social links from your API — are a site-wide state loaded from
  it (`stateKeys.shop.from`, [states.md](states.md)): `{{ global.shop.logo }}` in the HTML and on the page alike.
- **`data` on an entry** for what only the HTML needs (a review summary for `aggregateRating`). A source can
  read another's answer (`{{ product.id }}`); it loads after it.

Only what the bindings read is loaded: a product page's related lists, the signed-in customer and anything
else unread cost nothing. `keep` (or a state key's `ttl`) keeps an answer on the server that many seconds — a
shop's details, for example. An answer that fails or is slow (3 s) is left out, and the page still goes out.

Bindings in `head`, `bodyStart`, `bodyEnd` and `root` are **escaped as HTML**, so a value from an API can't add
tags; in `jsonld` they keep their type (`"{{ product.images }}"` is a list) and `<` is escaped. The `then`
filter writes either-or values: `{{ product.inStock | then: 'in stock' | default: 'out of stock' }}`.

## In the browser

The renderer reads the same file. The first page keeps the tags the server wrote (marked `data-syn-seo`); from
the next page on, the site replaces them with its own — the same title, description, image, canonical, meta and
structured data, worked out from what the page loaded (an entry's `data` is the server's only). `head`,
`bodyStart`, `bodyEnd` and `root` run only from the server; for code that should run on chosen pages as the
visitor moves around, use [snippets](custom-code.md#snippets).

Hosts: [../host/renderer.md](../host/renderer.md#every-pages-html) (`pageDocument` + `writeDocument`). In the
builder: **Code → Every page's HTML** edits the file and previews what the server writes for an address.
Checks flag a file that doesn't parse, body tags in head code, and calls to APIs the registry lacks.

The older inline `"document": { "head", "bodyEnd" }` in the manifest still works (every page, no bindings);
the builder moves it into the file in one click.
