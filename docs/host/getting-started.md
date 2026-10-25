# Getting Started

Two packages:

| Package | Goes into | What |
| --- | --- | --- |
| `@synui/renderer` | every site (public) | `SynUI(config)` — renders a site from its public files. Also a CDN script ([cdn.md](cdn.md)). |
| `@synui/builder` | your app only (private) | `SynUIBuilder(config)` — the drag-and-drop editor, plus the checks and library tools. |

**The builder never ships to a site.** A site installs only `@synui/renderer`, which contains no
builder code (a build check enforces it). The builder uses the renderer as a peer dependency, so the app
that hosts the builder installs both, of the same version.

See [renderer.md](renderer.md) for every `SynUI` field, [builder.md](builder.md) for `SynUIBuilder`.

## Install

```bash
# a site
npm install ./synui-renderer-2.1.0.tgz

# the app with the builder (the builder needs the renderer of the same version)
npm install ./synui-renderer-2.1.0.tgz ./synui-builder-2.1.0.tgz
```

**React 19** is required (`peerDependencies`: `react >=19`, `react-dom >=19`).
Radix, React Router, sonner and the other runtime libraries install as dependencies
(kept external, so your app shares one React and one router).

Import the stylesheets at your app entry:

```ts
import "@synui/renderer/styles.css"   // a site: Tailwind v4 build, tokens, every style-setting class
import "@synui/renderer/fonts.css"    // the fonts, as their own files (Bengali loads only on pages with Bangla)
import "@synui/builder/styles.css"    // the builder's app instead: the site's stylesheet plus the builder's
```

The CSS is compiled: your app does not need Tailwind, and a site's style settings
work without a rebuild (see [../site/styles.md](../site/styles.md#the-safelist)).

### Next.js

Both use the DOM (iframes, portals, `history`), so they run on the client.
Mark the importing file `'use client'`, or load it with `dynamic(..., { ssr: false })`.

---

## Show a site

Three params: the element it renders into, the site's folder, and the manifest when it isn't `manifest.json` there.

```ts
import { SynUI } from "@synui/renderer"
import "@synui/renderer/styles.css"

SynUI({ target: document.getElementById("app"), baseURL: "https://cdn.example.com/sites/acme/v3/" }).render()          // one element
SynUI({ target: document.querySelectorAll(".promo"), baseURL: "https://cdn.example.com/sites/promo/", manifest: "promo.json" }).render()   // every .promo
```

In React, leave out `target` — `render()` is then an element:

```tsx
const site = SynUI({ baseURL: "https://cdn.example.com/sites/acme/v3/" })
export const Site = () => site.render()
```

- `baseURL` is the site's own public folder. Its manifest names every file; only the manifest, the theme,
  the CSS and the registry load up front — each page loads when visited.
- Data comes from the APIs in the site's own `api/registry.json` ([../site/api-registry.md](../site/api-registry.md)),
  called by pages, forms and buttons. A value only the host knows — which API base to use — goes in
  `options.vars`: `SynUI({ baseURL, options: { vars: { api: "https://api.example.com" } } })` makes `{{ site.api }}` that address.

The site owns the URL (`history: "browser"`). Mount it under a path with `options.basename`, or keep its own
address with `options.history: "memory"`.

---

## Open the builder

```tsx
import { SynUIBuilder } from "@synui/builder"
import "@synui/builder/styles.css"

SynUIBuilder({
  target: document.getElementById("editor"),
  loader: { baseURL: "https://cdn.example.com/drafts/acme/" },     // optional: the site to edit; left out, a new site
  apis: { servers, endpoints, flows, schemas },                    // the app's API list, into the site's registry
  templates: [{ name: "Designs", url: "https://cdn.example.com/designs/" }],
  mediaAPI: { url: "https://media.example.com/files", dir: "acme/", headers: () => ({ Authorization: `Bearer ${token()}` }) },
  onSave: async (changes) => uploadAll(changes),                  // path → new text, or null for a deleted file
  options: { back: { href: "/settings", label: "Settings" } },
}).render()
```

The builder fills the viewport (`h-svh`): give it a page of its own. Its canvas is the real renderer inside
an iframe, so what you design is what ships. Leave out `mediaAPI` and files are kept in the page for the session.

---

## The site files

A site is a folder of small public JSON (and optional JS) files — never one blob:

```
manifest.json       names every file; routes, layouts, nav, SEO, state, site info
styles/global.css   the site's CSS and its variables
theme.json          motion
layouts/main.json   header and footer around a Page slot
pages/home.json     a page's blocks, data and state
forms/contact.json  a form
api/registry.json   the APIs the site calls
icons.json          drawings of the icons it uses
```

Start from a template (Templates in the builder) or write them by hand:
[../site/files.md](../site/files.md).

## Where next

| I want to… | Read |
| --- | --- |
| Load data from my API | [../site/api-registry.md](../site/api-registry.md), [../site/data.md](../site/data.md) |
| Save what the builder changes | [builder.md](builder.md#saving) |
| Let people upload and place pictures | [media.md](media.md) |
| Put a site on a plain HTML page | [cdn.md](cdn.md) |
| Add a block of my own | [extending.md](extending.md) |
| Check a site for mistakes before publishing | [../site/checks.md](../site/checks.md) |
