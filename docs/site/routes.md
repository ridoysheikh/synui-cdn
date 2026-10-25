# Routes, layouts and SEO

```json
"layout": "layouts/main.json",
"routes": [
  { "path": "/", "page": "pages/home.json", "title": "Home" },
  { "path": "/products/:slug", "page": "pages/product.json", "title": "{{ product.name }}" },
  { "path": "/checkout", "page": "pages/checkout.json", "layout": null },
  { "path": "/account", "page": "pages/account.json", "children": [
    { "path": "/account/orders", "page": "pages/account-orders.json" },
    { "path": "/account/print", "page": "pages/print.json", "layout": "layouts/blank.json" }
  ] },
  { "path": "*", "page": "pages/404.json", "noindex": true }
]
```

| Field | What |
| --- | --- |
| `path` | React Router pattern: `/`, `/products`, `/products/:slug`, `*`. Children write theirs in full. |
| `page` | The page file. Its script sits beside it as `.js`. |
| `layout` | A layout file; `null` = no layout; unset = the default (or, for a child, see below). |
| `title`, `description`, `image` | SEO; may bind to the page's data. `seo.titleTemplate` wraps the title. The host's server writes the same into the page's HTML, with more per path (meta, structured data, status) — [page-html.md](page-html.md). |
| `noindex`, `sitemap` | Keep out of search; `{ priority, changefreq }`. |
| `children` | Sub-routes. |

## Sub-routes

- **Without `layout`**: a sub-page shows **inside its parent** — in the parent page's
  **Page slot** (Outlet) block — in the parent's layout. It sees the parent's params
  and data. Blocks put inside the slot show while no sub-page is open.
- **With `layout`** (a file, or `null`): the sub-page **stands alone** — a page of its
  own in that layout; the parent isn't shown.

`layoutOf(manifest, path)` and `standsAlone(route, depth)` (`@synui/renderer/core`) give the
same answers the renderer uses. In the builder, a sub-page's Page settings has
**Shows**: inside the parent · on its own in a layout · on its own with no layout.

## Layouts

A layout is a view file with an `Outlet` block where the page goes. It stays mounted
across navigations, so a header's state and a sheet's open state survive.

## Navigation and redirects

- `manifest.nav` holds named menus, shown by Menu blocks (`props.menu`).
- Links (`href`) to routes navigate in place; hovering loads the page ahead.
- `redirects: [{ from, to, status? }]` answer before any page loads.
- On the live site every navigation fires `window` event `synui:navigate` (`detail.path`), for analytics snippets.

## Helpers

`allRoutes(manifest)` (flat, with `parent` and `depth`), `findRoute`, `mapRoute`,
`addRoute` — all from `@synui/renderer/core`.
