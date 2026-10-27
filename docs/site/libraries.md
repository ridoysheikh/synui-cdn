# Libraries and the catalog

Shared designs published at a URL (an S3 folder, a CDN): whole shops, pages, layouts,
sections, components, forms and flows. Taking one **copies** it into the site.

## `library.json`

```json
{
  "synui": "library", "version": 1, "name": "Eid kit", "author": "Nakshi",
  "items": [
    { "id": "eid-hero", "kind": "section", "name": "Eid hero", "file": "sections/eid-hero.json", "tags": ["hero"] },
    { "id": "bazaar", "kind": "site", "name": "Bazaar", "manifest": "sites/bazaar/manifest.json" },
    { "id": "landing", "kind": "page", "name": "Landing", "file": "pages/landing.json", "path": "/landing" }
  ]
}
```

| Item field | What |
| --- | --- |
| `id`, `kind`, `name` | `kind`: `site` (shop template) · `pages` (page set) · `page` · `layout` · `section` · `component` · `form` · `flow`. **An item without a kind is skipped.** |
| `file` / `manifest` | The item's JSON, or a site's manifest, relative to the library. |
| `description`, `tags` | `tags` say what it can stand in for (a `header`) — the builder's Swap. |
| `category` | Its one category (`hero`, `checkout`, `heading`) — the browser's Category filter and Swap's "same kind" (a hero is swapped for heroes). Default: its first tag that isn't a kind word. |
| `locked` | Who keeps what it does — [locked.md](locked.md). |
| `thumbnail` | A picture instead of a live preview. |
| `path`, `title` | A page's suggested address and title. |

A flow file is a registry flow plus `needs` — the servers and endpoints it calls, added
when missing. A section or component file may carry `needs.vars` — CSS variables it draws with and a value for each,
written to the site's CSS file only where the site doesn't set them.

### Page sets

A page set (`kind: "pages"`, default file `sets/<id>/set.json`) is pages that work together — a checkout, sign-in, a
customer's account — added in one click. Its files sit beside `set.json` at the paths they take in a site
(`sets/checkout-split/pages/cart.json` → `pages/cart.json`):

```json
{ "name": "Split checkout", "start": "/checkout",
  "pages": [{ "path": "/cart", "file": "pages/cart.json", "title": "Cart" },
            { "path": "/checkout", "file": "pages/checkout.json", "layout": "layouts/checkout.json" }],
  "flows": { "placeOrder": { … } },
  "needs": { "servers": {}, "endpoints": {}, "state": { "shop": {} }, "persist": [], "stateKeys": {},
             "vars": { "lg-line": "var(--border)" }, "scripts": ["scripts/payment.js"] } }
```

Adding it writes, in one undo step: every page at its address (a page already there is replaced, its sub-pages kept —
the builder lists what it replaces and asks twice), their layouts, components, forms and scripts under their own
names, the flows (replacing same-named ones) and the endpoints and servers the site lacks, state the site doesn't
have, and CSS variables it doesn't set. The browser previews it on a copy of the site, page by page. A library may bring `icons.json`; items bring the drawings they use.

Also accepted as a library URL: a site folder (its components and pages become items)
or a single component / page file.

## In the builder

`SynUIBuilder({ templates })` — where the Templates panel loads and searches designs. Each entry is a `name`
and one of three sources:

```ts
templates: [
  { name: "Designs", url: "https://cdn.example.com/designs/" },                  // a folder with library.json
  { name: "Pro", url: "https://api.example.com/templates",                       // a loader API: one JSON pack
    headers: () => ({ Authorization: `Bearer ${getToken()}` }) },
  { name: "Mine", load: () => fetchMyTemplates() },                             // your own loader: the pack
  { name: "Kit", json: kitPack },                                                // the pack, attached
]
```

| Field | Meaning |
| --- | --- |
| `name` | Shown as the source in the gallery's filter |
| `url` | A folder with `library.json` (or `library.json` itself), read file by file — **or** a loader API whose answer is a pack (JSON holding a `"library.json"` key) |
| `headers` | Optional, on every request to `url` (a token, an API key) — an object, or a function called each time |
| `load` | Your own loader: `() => Promise<pack>` (any API, any SDK) |
| `json` | The pack itself, attached |

**A pack** is a whole library as one JSON — path → file: `library.json` and every file its items name.
A JSON file may be the object itself or its text; a page script or CSS is its text; a thumbnail is a URL (or a
`data:` URL).

```json
{
  "library.json": { "synui": "library", "version": 1, "name": "Kit", "items": [
    { "id": "hero", "kind": "section", "name": "Hero", "file": "sections/hero.json", "thumbnail": "thumbs/hero.png" } ] },
  "sections/hero.json": { "name": "Hero", "view": [ … ] },
  "thumbs/hero.png": "https://cdn.example.com/thumbs/hero.png"
}
```

SynUI only reads a library; where it's hosted, and who may read it, is the application's business. With
`headers`, every file — a whole site template too — is read with them, and so are a folder's thumbnails (a
plain `<img>` can't send headers). A 401 / 403 shows on that library as "check its headers".

- **Templates** shows every item of the `templates` libraries and of libraries pasted in the Library
  panel, with live thumbnails, filtered by type and source.
- Every item opens in a preview (the real renderer, in this site's theme) before it's added.
- Its components and forms come with it, renamed on clashes. Items copy to Components,
  except site templates, which replace the site.
- **Templates are build time only.** Nothing about a library goes into the site's files:
  an added item is the site's own copy. Pasted libraries last for the session.

`openLibrary(url)` and `bundleOf(library, item)` (`@synui/builder`) do the same for tooling.

## The design catalog in this repo

`npm run catalog` (`catalog/build.mjs`, from `catalog/base/`) generates `catalog/kroybd/`:
complete shops with checkout (`shops.mjs`; Bazaar, Atelier, Fresh Market and more), four **large stores** from one
engine (`catalog/large/`: `skins.mjs` — HaatBazar, Kenakata, Nilam and Sodai, each in the manner of a different big
marketplace: header, home sections, card and product-page layout, palette and words — and `site.mjs`, 29 pages each on
the shared registry in `catalog/base`: filters with facet counts, brands, reviews, offers, related products, sign-in by
SMS code, orders, saved lists, compare and a page HTML file), **MegaHaat** — a 28-page
marketplace in the manner of the big online stores (`catalog/marketplace/`: dark search-first header with
departments and account menu; home of card tiles and product strips; search with a filter sidebar in the page
address; dense product pages with a buy box, offers, bought-together, specs and paged reviews; deals,
departments, best sellers, cart, checkout, account, orders, saved lists, compare, help, gift cards,
membership, selling and brand stores — with its own API registry and demo data, and a page HTML file of code items: the
shop's live details on every page, a page-view counter, a function for deal previews, Product / CollectionPage / Brand / Organization structured data, 404s for missing
products, results pages kept out of search) — plus pages, layouts,
sections, parts, forms and flows, all on the current blocks — moving announcement, hero
slider, product carousels with loading cases, reviews with stars, zoom spotlight, video,
before / after, sale countdown, payment-logo marquee, phone menu sheet on `menuOpen`, checkouts that go on to the
store's payment page when it asks for money first (`placeOrder` / `payOrder` flows, `checkout.payments` for "Pay with"),
per-screen layouts. It fetches every icon they use into `icons.json`. Re-run it after
adding blocks. The test app offers it at `/libs/kroybd/` (public) and the Eid kit at `/libs/signed-in/`
(behind a bearer token), and a small kit attached as JSON (`test-preview/attached-kit.ts`) — all in the `templates` of `test-preview/builder.tsx`.

An item can be **locked** (`"locked": "<who>"` on the item, `locked` in its files): a site restyles it but can't change what it does — [locked.md](locked.md).
