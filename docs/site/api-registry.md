# API registry

`api/registry.json`: every backend the site talks to, written as data. The builder
reads it to suggest bindings and make forms; the runtime reads it to call.

```json
{
  "servers": {
    "shop": { "baseUrl": "{{ site.api }}/public", "auth": "customer" }
  },
  "auth": {
    "customer": { "type": "bearer", "token": "{{ session.token }}" }
  },
  "endpoints": {
    "products.list": {
      "summary": "Products, newest first", "server": "shop", "method": "GET", "path": "/products",
      "params": [{ "name": "collection", "in": "query", "schema": { "type": "string" } }],
      "pagination": { "style": "page", "page": "page", "size": "size", "items": "items", "total": "total", "defaultSize": 12 },
      "response": { "type": "object", "properties": { "items": { "type": "array", "items": { "ref": "Product" } } } },
      "example": { "items": [], "total": 0 },
      "tag": "Catalog"
    }
  },
  "flows": {},
  "schemas": { "Product": { "type": "object", "properties": { "name": { "type": "string" } } } }
}
```

## Servers (API addresses)

`baseUrl` — a **full URL** (`https://api.example.com/v1`), never a path like `/api`; it may bind
(`{{ site.api }}/v1`), and what it binds to must be a full URL too — `headers?`, `auth?` (default scheme for its
endpoints), `description?`. A path is refused: Checks flags it, and a call to it fails with a plain message.
In the builder: Site settings → API addresses & sign-in.

## Auth schemes

| `type` | Fields | Sends |
| --- | --- | --- |
| `none` | — | Nothing |
| `bearer` | `token` | `Authorization: Bearer …` (usually `{{ session.token }}`) |
| `apiKey` | `in: header \| query`, `name`, `value` | A key in a header or the query |
| `basic` | `username`, `password` | Basic auth |
| `cookie` | — | The API's own cookie (`credentials: include`) |

**Everything in a published registry is public.** Only browser-safe keys belong
here; secrets stay on your server.

## Endpoints

| Field | What |
| --- | --- |
| `summary`, `description`, `tag` | Plain words; `tag` groups them in the builder. |
| `server`, `method`, `path` | `"/products/{slug}"` — `{name}` is a path param. |
| `params` | `{ name, in: path \| query \| header, schema?, required?, description? }` |
| `headers`, `auth` | Extra headers; a scheme overriding the server's. |
| `body`, `response` | Schemas (a small JSON-Schema subset; `ref` names a shared one). `body.example` is the request body example. |
| `example` | The response body example: the builder canvas uses it when the API can't be reached. |
| `responseHeaders` | Example headers it answers with (for the reader). |
| `pagination` | `style: page \| offset \| cursor`, the param names, and where `items`, `total`, `next` are in the answer. **Required on every GET that returns a list** — see below. |

### Every GET list is paged

A strict rule: a GET that returns a list must declare `pagination`, so no list is ever loaded whole — it is
read a page at a time (page numbers) or lazily ("Load more", a cursor).

- **A list** is a GET whose answer is an array, or an object holding one under `items`, `data`, `results`,
  `rows` or `list`. A path that ends in one record's id (`/orders/{number}`) is a record, not a list — its
  `items` are that record's own lines.
- `cursor` paging also says where the next cursor is (`next`).
- **Checks** shows an unpaged list as an error. **The runtime refuses to call one** (the call fails with that
  sentence), and a paged endpoint called with no page of its own gets the first: `defaultSize` items, else 20.
- `listGetProblem(name, endpoint, registry)` and `returnsList(endpoint, registry)` (`@synui/renderer/core`) are
  the rule on its own.

Schemas: `type`, `properties`, `required`, `items`, `enum`, `format` (`email`, `phone`,
`url`, `date`, `date-time`, `money`, `image`, `textarea`, `password`), min / max,
`pattern`, `default`, `example`, `title`, `description`.

## Using an endpoint

- Page, component or block data: `{ "api": "products.list", "params": { … } }` — [data.md](data.md)
- A List block's items — [data.md](data.md#lists)
- A `call` step — [steps.md](steps.md)
- A form's submit or `load`, a select's `optionsFrom` — [forms.md](forms.md)
- A site-wide key's `from` — [states.md](states.md)

`flows` are the site's actions — [states.md](states.md#actions).

## In the builder

**The app's APIs come from the config.** An app passes its own API list — the same for every site it builds
(a shop platform's storefront endpoints) — as `SynUIBuilder({ apis })`, written in this file's shape:

```ts
SynUIBuilder({
  target: document.getElementById("editor"),
  apis: {
    servers: { shop: { baseUrl: "{{ site.api }}/public", auth: "customer" } },
    endpoints: {
      "products.list": { summary: "Products", server: "shop", method: "GET", path: "/products",
        headers: { "X-Client": "web" }, response: { ref: "ProductPage" } },
      "orders.create": { summary: "Place an order", server: "shop", method: "POST", path: "/orders",
        body: { ref: "OrderInput" }, response: { ref: "Order" } },
    },
    flows: { checkout: { summary: "Check out", steps: [{ do: "call", api: "orders.create", body: "{{ form }}" }] } },
    schemas: { ProductPage: { … }, OrderInput: { … }, Order: { … } },
  },
})
```

They show in the API panel and are merged into the site's `api/registry.json` (same names replaced, the
site's own others kept), so the built site carries them and the renderer calls them.

The API panel is one flat, searchable list of endpoints. An endpoint is written as examples:

| Part | What |
| --- | --- |
| Request | What it does, the method and path, the address (server) and sign-in. |
| Values it takes | `{name}` in the path (found by itself), query values, headers. |
| Request headers | On top of the address's. |
| Request body | An example in JSON (not for GET) — forms send this shape. |
| Response headers | An example. |
| Response body | An example in JSON — lists and bindings read its fields. |
| Paging, Try it | Folded paging settings; a real request, whose answer can become the response example. |

The fields (schemas) are learned from the examples. Writing a new example keeps what was said
about the fields still there (titles, formats, required, choices) — `mergeSchema(old, inferSchema(example))`.
Addresses and sign-in are in Site settings; actions in States.
`formFromEndpoint(registry, api)` (`@synui/builder`) makes a form from an endpoint's body
schema; `schemaPaths`, `schemaAt`, `inferSchema`, `mergeSchema`, `bodyFromSchema` come from
`@synui/renderer/core`.
