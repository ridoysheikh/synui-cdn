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

`manifest.document` is code that is part of every page's HTML — written by the server that sends the
page, not added by the site in the browser — so it is there before anything loads:

```json
"document": {
  "head": "<meta name=\"google-site-verification\" content=\"…\">\n<link rel=\"preconnect\" href=\"https://fonts.gstatic.com\">",
  "bodyEnd": "<noscript>Turn on JavaScript to shop here.</noscript>"
}
```

| Key | Goes | For |
| --- | --- | --- |
| `head` | At the end of `<head>` | Meta and verification tags, preconnects, fonts, icons, analytics that must load first |
| `bodyEnd` | Just before `</body>` | Chat widgets, `<noscript>` fallbacks, deferred scripts |

Always every page; never in the builder. Search engines, link previews and site checks see it
(they often don't run the site's scripts). Checks warn when `head` holds a body tag (`<div>`,
`<img>`…), which the browser would move out of `<head>` with everything after it. Hosts: see
[../host/renderer.md](../host/renderer.md#every-pages-html).

## Snippets

`manifest.inject` puts HTML snippets — `<script>`, `<meta>`, `<link>`, `<style>`,
`<noscript>` — into every page or chosen ones:

```json
"inject": [{ "id": "ga", "name": "Google Analytics", "where": "head", "enabled": true,
             "pages": ["/", "/products/:slug"], "code": "<script async src=…></script>" }]
```

Snippets are added by the site in the browser, so they can run on chosen pages only; for code that
must be in the HTML itself, use `document` above. `where`: `head` · `body-start` · `body-end`. They run on the live site only — never in
the builder, never with `SynUI({ injections: false })`. The window event
`synui:navigate` (`detail.path`) counts page views of the single-page site.
