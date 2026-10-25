# Custom code

Steps cover most needs. When they don't, there is code — all of it in the builder's **Code**
panel: the site's CSS file ([theme.md](theme.md)), every page's HTML, snippets, and the scripts.

## Page and site scripts

`pages/x.js` sits beside `pages/x.json` (or `script` names another file); `manifest.scripts`
lists site-wide ones. They are ES modules; exported functions are callable by name.

```js
// pages/product.js
export async function related({ params, api }) {          // a data loader: "data": { "related": "related" }
  const { data: p } = await api.call("products.get", { params: { slug: params.slug } })
  const { data } = await api.call("products.list", { params: { collection: p.collections?.[0], limit: 4 } })
  return data.items
}

export async function addBoth({ cart, toast, item }) {      // a step: { "do": "run", "fn": "addBoth" }
  await cart.add(item)
  await cart.add(item.pair)
  toast.success("Both added")
}
```

**Action `ctx`** — the step's scope plus:

| Name | What |
| --- | --- |
| `params`, `query`, `data`, `site`, `item`, `scope`, `session`, `global` | What bindings see |
| `state`, `set(key, value)`, `merge(patch)` | The page's state |
| `setGlobal(key, value)` | A site-wide key |
| `api` | The site's API registry: `await api.call(name, { params, body })` → `{ data, status }` |
| `cart` | The built-in cart — [data.md](data.md#the-cart) |
| `navigate(to)`, `toast`, `reload()` | Go, tell, load the page's data again |
| `open(id)`, `close(id?)` | Overlays |

**Loader `ctx`**: `params`, `query`, `site`, `session`, `api`, `cart`.

Scripts run in the page as blob modules (a Worker sandbox is not built yet), so the
site must live on its own domain. Checks flag a step or form naming a function the
script doesn't export.

## Custom HTML block

`Html` renders `html` in a sandboxed iframe (`height`, `title`) — embeds, widgets.

## Every page's HTML

Code that is part of each page's HTML — written by the host's server, not added in the browser — lives in the
page HTML file (`document.json`): `head`, `bodyStart`, `bodyEnd` and `root` code for every page or chosen
paths, beside the title, SEO tags and structured data, with live values from your APIs. See
[page-html.md](page-html.md).

## Snippets

`manifest.inject` puts HTML snippets — `<script>`, `<meta>`, `<link>`, `<style>`,
`<noscript>` — into every page or chosen ones:

```json
"inject": [{ "id": "ga", "name": "Google Analytics", "where": "head", "enabled": true,
             "pages": ["/", "/products/:slug"], "code": "<script async src=…></script>" }]
```

Snippets are added by the site in the browser, so they run as the visitor moves between pages; for code that
must be in the HTML itself, use the page HTML file above. `where`: `head` · `body-start` · `body-end`. They run on the live site only — never in
the builder, never with `SynUI({ injections: false })`. The window event
`synui:navigate` (`detail.path`) counts page views of the single-page site.
