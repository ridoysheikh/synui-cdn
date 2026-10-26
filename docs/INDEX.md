# SynUI Documentation Index

Every topic is one file. Read the row you need, not the whole set — the `~tok`
column is the approximate cost of each file.

Two packages, both used as objects made from a config: **`@synui/renderer`** — `SynUI(config)`, public,
every site installs it (or loads it from a CDN) — and **`@synui/builder`** — `SynUIBuilder(config)`, private,
only the app that edits sites. Three groups of docs: **Host** (putting them in an app), **Site** (the files a
site is made of — for people, the builder and AI alike) and **Develop** (changing them).

Version: **3.2.0** — see [../CHANGELOG.md](../CHANGELOG.md).

---

## Host — using the packages in an app

`@synui/renderer` (`/core`, `/ui`, `/styles.css`, `/fonts.css`) · `@synui/builder` (`/styles.css`) · the CDN script (`synui.js`, `synui.css`)

| File | Covers | ~tok |
| --- | --- | ---: |
| [host/getting-started.md](host/getting-started.md) | The two packages and who installs which, the stylesheets, Next.js, showing a site, opening the builder | 1260 |
| [host/renderer.md](host/renderer.md) | `SynUI(config)`: the three params (`target` — an element or a list, never a string; `baseURL`; `manifest`) and `options`, `render()` / `unmount()`, where everything comes from (public files, `{{ site.* }}`), what loads when, sources for tools, each page's HTML on the server (`pageDocument`, `writeDocument`: status, SEO, structured data), security | 2300 |
| [host/builder.md](host/builder.md) | `SynUIBuilder(config)` (private, peer of the renderer): the site being edited (`loader` — `baseURL` + `manifest`, optional), the app's modules: `apis` (its API list, merged into the site's registry), `templates` (url / loader API / `load` / `json`), `mediaAPI` (a full URL); `target` an element, `onSave`, `onPublish`, `options` — the object's members, saving, publishing, what the person sees — ships in `@synui/builder`'s docs only | 1500 |
| [host/media.md](host/media.md) | The `mediaAPI` config (build time, all media public): a CRUD `url` with `dir` and `headers`, your own functions and their types, the in-page default, the panels it adds — ships in `@synui/builder`'s docs only | 1390 |
| [host/cdn.md](host/cdn.md) | The renderer as one script: `SynUI({ target, baseURL }).render()` in any HTML page, the `#syn-boot` block (always into `#root`), prerendering (`__synReady`), `npm run release` | 680 |
| [host/extending.md](host/extending.md) | `defineBlock`, `BlockDef`, `FieldSpec`, `BlockRenderProps`, hooks, rules a block follows | 930 |
| [host/exports.md](host/exports.md) | Every export of both packages, entry by entry, with its doc | 1270 |

## Site — the site format

| File | Covers | ~tok |
| --- | --- | ---: |
| [site/files.md](site/files.md) | The public folder, what loads when, every `manifest.json` field (site info, `vars`, every file it names), a page file | 1040 |
| [site/views.md](site/views.md) | View nodes, `{{ bindings }}` and filters (`take`, `reverse`, `sum`…), what's in scope (`query` — the page address), conditions (`has` / `lacks`) | 1010 |
| [site/routes.md](site/routes.md) | Routes, sub-routes inside the parent or standing alone, layouts, nav, redirects, SEO | 620 |
| [site/states.md](site/states.md) | Page and site-wide state, `set` ops (`toggle` an item in a list, `max`), saved lists (wishlist, compare, recently viewed), `persist`, `stateKeys` (`from`, `ttl`) | 1140 |
| [site/data.md](site/data.md) | Data sources (`wait`, `keep`, `ahead`), loading ahead, lists, filtered lists in the page address (search, filters, sort, pages, a filter sidebar), loading states, the built-in cart | 1370 |
| [site/api-registry.md](site/api-registry.md) | Servers, auth schemes, endpoints, pagination, schemas, examples | 820 |
| [site/steps.md](site/steps.md) | Every step (`query` — the page address), actions, which blocks fire which events | 1040 |
| [site/forms.md](site/forms.md) | Form files: fields (`password` too), checks, submit, `load`, `optionsFrom`, status | 470 |
| [site/components.md](site/components.md) | Components, inputs, `Slot`, per-use data, tags for Swap | 380 |
| [site/blocks.md](site/blocks.md) | Every block, by kind (Layout, Text, Media, Actions, List, Filter, Form, Overlay, Navigation, Motion, Commerce, Embed), with its fields | 2470 |
| [site/overlays.md](site/overlays.md) | Dialog / Sheet design, opening and closing, `keepOpen`, `closeOnLink`, site-wide keys, events | 640 |
| [site/icons.md](site/icons.md) | Iconify names, `icons.json`, how icons draw, the picker | 270 |
| [site/styles.md](site/styles.md) | Every style setting by group — see-through colours, gradients, shapes and fades, blend, filters, scroll snapping — pinning, screen sizes, motion (appear, scroll-linked, pointer, press, loops, morph), `class`, the safelist | 1780 |
| [site/theme.md](site/theme.md) | The CSS file (`manifest.css`) and its variables, `theme.json` (`motion`, `scroll: smooth`, `pages: morph`), the Theme pickers, `style.vars`, older sites, fonts | 770 |
| [site/custom-code.md](site/custom-code.md) | Page and site scripts (`ctx`), the Html block, where code in the pages lives | 650 |
| [site/page-html.md](site/page-html.md) | `document.json`: the site's code items — `seo`, `meta`, `jsonld`, `html`, `css`, `js`, `script`, `root`, `function` — for every page or chosen paths, with `if` and `data`; run in the page HTML (the host's server), once, or on every page view; precedence, status, bindings, functions (`runFunctions`), the browser, the builder (Code, Page tab), older forms | 2300 |
| [site/libraries.md](site/libraries.md) | `library.json`, item kinds, Templates, the builder's `templates` (a folder, a loader API answering with a JSON pack, `load()`, or the pack attached; build time only, never in the site), the demo catalog in this repo (30 shops: the four large stores from `catalog/large` and the MegaHaat marketplace among them) | 1380 |
| [site/checks.md](site/checks.md) | `lintSite`, `Suggestion`, everything it checks | 440 |

## Develop — changing the kit

| File | Covers | ~tok |
| --- | --- | ---: |
| [develop/workspace.md](develop/workspace.md) | (Not in the renderer's tarball.) Folder layout, the package boundaries, commands, conventions, releasing | 1480 |

---

## Common entry points

| I want to… | Read |
| --- | --- |
| Show a site in my app | [host/getting-started.md](host/getting-started.md) |
| Show a site on a plain HTML page | [host/cdn.md](host/cdn.md) |
| Load data from my API | [site/api-registry.md](site/api-registry.md), [site/data.md](site/data.md) |
| Save what the builder changes | [host/builder.md](host/builder.md#saving) |
| Let people upload and place pictures and files | [host/media.md](host/media.md) |
| Offer ready-made designs | [site/libraries.md](site/libraries.md#in-the-builder) |
| Write a site by hand (or have an AI write one) | [site/files.md](site/files.md), then [site/views.md](site/views.md) and [site/blocks.md](site/blocks.md) |
| Call my API from a button | [site/api-registry.md](site/api-registry.md), [site/steps.md](site/steps.md) |
| Keep a value across pages | [site/states.md](site/states.md) |
| Open a sheet from anywhere | [site/overlays.md](site/overlays.md#tied-to-a-site-wide-key) |
| Show skeletons while data loads | [site/data.md](site/data.md#loading-states) |
| Change a block's layout on phones | [site/styles.md](site/styles.md#screen-sizes) |
| Build a listing page with filters down the left | [site/data.md](site/data.md#filtered-lists) |
| Add a wishlist, compare or "recently viewed" | [site/states.md](site/states.md#saved-lists-wishlist-compare-follow) |
| Make blocks move (on scroll, on hover, between pages) | [site/styles.md](site/styles.md#motion), [site/theme.md](site/theme.md) |
| Give a sub-page its own layout | [site/routes.md](site/routes.md#sub-routes) |
| Add a block kind of my own | [host/extending.md](host/extending.md) |
| Publish a design library | [site/libraries.md](site/libraries.md) |
| Check a site before publishing | [site/checks.md](site/checks.md) |
| Cut a release | [develop/workspace.md](develop/workspace.md#releasing) |
