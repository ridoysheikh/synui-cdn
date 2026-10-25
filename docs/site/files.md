# Site files

A site is a folder of small **public** files indexed by `manifest.json`. The manifest names every file
the site has, so a renderer given the folder's address needs nothing else. Nothing is packed into
one blob, so a 100-page site loads one page at a time, and people, the builder and
an AI can all read and edit the same files.

```
manifest.json       names every file · site info · routes · layouts · components · forms · nav · seo · state
styles/global.css   the site's CSS: every variable it draws with (:root), and any other CSS
theme.json          motion
layouts/main.json   header / footer around a Page slot (Outlet) block
components/x.json   a reusable piece (header, card): inputs + view
pages/x.json        a page: view tree, data, initial state, seo
pages/x.js          that page's loaders and actions (optional ES module)
forms/x.json        fields, checks and what happens on submit
scripts/site.js     actions shared by every page (optional)
api/registry.json   servers · sign-in schemes · endpoints · actions (flows) · shared schemas
icons.json          drawings of every icon the site uses
```

**What loads when:** the manifest, theme, CSS file, registry, `icons.json` and site scripts
up front; each page (JSON, script, forms, components, data) when visited. One file
per dynamic route (`/products/:slug`) serves every URL.

## `manifest.json`

| Field | What |
| --- | --- |
| `version` | Always `1`. |
| `name`, `locale` | Site name; language and region (`en-BD`) for dates and numbers. |
| `currency`, `timezone`, `logo` | Money for `\| money` and the price blocks (`BDT`); the time zone dates show in; the logo's URL. |
| `vars` | Anything else pages and the registry read as `{{ site.<name> }}` — an API's base URL, a phone number. The host's `vars` win ([../host/renderer.md](../host/renderer.md)). |
| `theme` | Theme file path (`theme.json`). |
| `css` | The CSS file (`styles/global.css`), loaded as it is on every page — [theme.md](theme.md). |
| `layout`, `layouts` | Default layout for every route; every layout file, used or not. |
| `routes` | Pages and sub-pages — [routes.md](routes.md). |
| `nav` | Named menus: `{ main: [{ label, href, children? }] }`, shown by Menu blocks. |
| `seo` | `titleTemplate` (`"%s · Nakshi"`), `description`, `image`, `siteUrl`. |
| `redirects` | `[{ from, to, status? }]` (301 by default). |
| `state`, `persist`, `stateKeys` | Site-wide state — [states.md](states.md). |
| `components` | Component names (files in `components/`) — [components.md](components.md). |
| `forms` | Form names (files in `forms/`) — [forms.md](forms.md). The builder keeps it up to date. |
| `scripts` | Site-wide JS modules — [custom-code.md](custom-code.md). |
| `api` | Registry path (default `api/registry.json`) — the APIs the site calls at run time. |
| `icons` | Icon drawings (default `icons.json`). |
| `inject` | Snippets the site adds in the browser, on every page or chosen ones — [custom-code.md](custom-code.md#snippets). |
| `document` | `head` / `bodyEnd` code in every page's HTML, written by the server — [custom-code.md](custom-code.md#every-pages-html). |
| `prefetch` | `hover` (default) · `visible` · `off` — [data.md](data.md#loading-ahead). |
| `files` | Published snapshots only: path → content hash. |

## A page file

```json
{
  "data": { "product": { "api": "products.get", "params": { "slug": "{{ params.slug }}" } } },
  "state": { "qty": 1 },
  "seo": { "title": "{{ product.name }}" },
  "view": [
    { "id": "title", "type": "Heading", "props": { "text": "{{ product.name }}", "level": 1 } }
  ]
}
```

| Field | What |
| --- | --- |
| `data` | Name → API call or script loader — [data.md](data.md). |
| `state` | The page's own values, `{{ state.x }}` — [states.md](states.md). |
| `script` | Another script path, or `false` for none (default: same name, `.js`). |
| `seo` | `title`, `description`, `image` — may bind. |
| `view` | The blocks — [views.md](views.md). |

A layout file has the same shape; its view holds an `Outlet` where the page shows.

## `theme.json`

See [theme.md](theme.md).
