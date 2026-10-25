# Renderer — `SynUI(config)`

`@synui/renderer` — the public package every site installs. A site is an **object**: make it from a
config, then render it. The npm package and the CDN script ([cdn.md](cdn.md)) are the same code.

```ts
import { SynUI } from "@synui/renderer"

SynUI({ target: document.getElementById("app"), baseURL: "https://cdn.example.com/sites/acme/v3/" }).render()   // into the page

const site = SynUI({ baseURL: "https://cdn.example.com/sites/acme/v3/" })
site.render()           // no target: a React element, for a React tree
site.unmount()          // take it out of everything it was rendered into
```

The config is read once. Make the object once (module scope, `useMemo`, `useState`) and keep it.

## Config — `SynUIConfig`

Three params:

| Field | Type | What |
| --- | --- | --- |
| `target` | `Element \| Iterable<Element>` | The element it renders into, or several (`document.querySelectorAll(".promo")` — every one). Never a string. Left out: `render()` is a React element. |
| `baseURL` | `string` | The site's own folder — its actual path (`…/sites/acme/v3/`). Every file is **public**, read from here with a plain GET, one at a time. |
| `manifest` | `string \| Manifest` | The manifest's name or path in `baseURL` (default `manifest.json`), a full URL — or the manifest itself, already in hand. |

Everything else is optional, in `options` (`SynUIOptions`):

| Option | Type | Default | What |
| --- | --- | --- | --- |
| `vars` | `Record<string, unknown>` | — | Values for `{{ site.* }}` on top of the manifest's `vars` — what only the host knows: an API's base URL. |
| `files` | `Record<string, string>` | — | Site files already in hand (a server read them for the page's HTML): path → text, not fetched again. |
| `history` | `"browser" \| "memory"` | `"browser"` | `memory` keeps its own address (a preview inside another app). |
| `basename` | `string` | — | The site mounted under a path (`/shop`). |
| `path` | `string` | `"/"` | First page with `history: "memory"`. |
| `injections` | `boolean` | `true` | Put `manifest.inject` code into the page. Off while prerendering. |
| `onRouter` | `(router) => void` | — | The React Router instance, when built and again when routes change. |
| `onError` | `(error) => void` | — | The site couldn't load (manifest or theme missing, bad JSON). |

## `render()`

| Call | Returns | What |
| --- | --- | --- |
| `render()` with a `target` | the object | Renders into the target's element(s). |
| `render()` without one | `ReactElement` | The site, for a React tree. |
| `render(target)` | the object | Into these element(s) instead of the config's. |

A target with no element (a `null` from `getElementById`, a string) throws. Rendering into an element twice does
nothing the second time. `site.config` is what it was made
with; `site.version` the renderer's version.

## Where everything comes from

The **manifest names every file** the site has — theme, CSS, scripts, layouts, components, forms, the API
registry, icons — and every route names its page ([../site/files.md](../site/files.md)). So `baseURL` and the
manifest are all the renderer needs:

| Need | From |
| --- | --- |
| Pages, layouts, components, forms | the files the manifest names |
| Data | the site's API registry — page `data`, form `submit`, button steps ([../site/api-registry.md](../site/api-registry.md)) |
| `{{ site.name }}`, `site.locale`, `site.currency`, `site.timezone`, `site.logo`, `site.nav` | the manifest |
| `{{ site.anything }}` | the manifest's `vars`, then `options.vars` |
| The cart | built in: kept in the browser ([../site/data.md](../site/data.md#the-cart)) |

**Every site file is public.** The renderer sends no headers and no sign-in for them; a host may block
other origins with CORS, but that is the host's choice. Secrets never go in site files — a call that needs
one goes to your API, which holds it.

## What loads when

1. `manifest.json`, the theme, the CSS, the registry, the icons and site scripts.
2. On each visit: the page's JSON, its script, its forms and components, then its
   `data` ([../site/data.md](../site/data.md)). Layouts load once and stay mounted.
3. Linked pages load ahead on hover (`manifest.prefetch`).

The `<title>`, description and social image come from the route and page SEO;
redirects run before the page loads.

## Sources — for tools

`SynUI` reads `baseURL` with an `HttpSource`; tools that need the files themselves use the sources directly
(`@synui/renderer/core`):

| Export | Use |
| --- | --- |
| `HttpSource(baseURL, files?, manifest?)` | A public folder. Caches each file; a failed read is retried next time. |
| `MemorySource(files?)` | An editable copy: `write`, `remove`, `get`, `has`, `list`, `snapshot`, `version`; fires `subscribe` on change (the builder's canvas). |
| `loadAllFiles(source, extra?)` | Every file the manifest reaches, `{ path: text }` — to copy, publish or check a site. |

## Every page's HTML

`manifest.document` holds site-wide code that belongs to the document itself — `head` (end of
`<head>`) and `bodyEnd` (just before `</body>`). The renderer never adds it: **your server writes it
into the HTML it sends for every page**, so it is there before any script runs and search engines,
link previews and site-verification checks see it. `@synui/renderer/core` has the helpers (no DOM —
Node, edge workers, build scripts):

```ts
import { writeDocument, documentCode } from "@synui/renderer/core"

res.send(writeDocument(indexHtml, manifest))      // head before </head>, bodyEnd before </body>
const { head, bodyEnd } = documentCode(manifest)  // the two snippets, when you build the HTML yourself
```

`writeDocument` wraps each snippet in `<!-- synui:head -->` / `<!-- synui:body-end -->` markers and
replaces what is between them on a second run, so writing a cached page again doesn't double it.
The test app's server does this for the preview (`test-preview/site-document.ts`).

## Security

- Custom HTML blocks render in a sandboxed iframe.
- `manifest.inject` snippets run only when `options.injections` is on — never in the builder.
- `manifest.document` is the site's code in every page's HTML: like injections, it runs with the
  site's full access, which is why a site lives on its own domain.
- Page scripts are ES modules run in the page (not yet a Worker sandbox): serve the
  site from its **own domain**, where no session cookies live.
- API keys in a published registry are public: only browser-safe keys belong there.
