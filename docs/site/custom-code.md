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

## Code in the pages — snippets, CSS, JS, tags

Code the site puts into its pages — pasted snippets (analytics, pixels, widgets), CSS, JS, script files, meta
tags, structured data, text for crawlers, and functions that work values out — is one list of typed **code
items** in the page HTML file (`document.json`). Each is for every page or chosen paths, maybe only when a
condition holds, with API data of its own; each runs in the page's HTML (written by the host's server) or in the
browser — once, or on every page view. See [page-html.md](page-html.md).

The older `manifest.inject` snippets still work, read as `html` items (every page: `once`; chosen pages:
`visit`); the builder moves them into the file the first time you change one.
