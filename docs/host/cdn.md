# The renderer on a CDN

The same renderer as the npm package, as two files any HTML page can load — no npm, no bundler, no build
on your side. They are published to the public repo **[ridoysheikh/synui-cdn](https://github.com/ridoysheikh/synui-cdn)**
(only the built files and these docs — the source stays private) and served by jsDelivr:

```
https://cdn.jsdelivr.net/gh/ridoysheikh/synui-cdn@<version>/dist/synui.js
https://cdn.jsdelivr.net/gh/ridoysheikh/synui-cdn@<version>/dist/synui.css
```

- `synui.js` — one classic script (React and the renderer inside); it puts `SynUI` on `window`
- `synui.css` — the styles; the fonts (`*.woff2`) sit beside it

## Use it in any page

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/ridoysheikh/synui-cdn@2.0.0/dist/synui.css">

<div id="app"></div>

<script src="https://cdn.jsdelivr.net/gh/ridoysheikh/synui-cdn@2.0.0/dist/synui.js"></script>
<script>
  SynUI({ target: document.getElementById("app"), baseURL: "https://cdn.example.com/sites/acme/v3/" }).render()
</script>
```

`SynUI(config)` is the npm package's function, with the same three params ([renderer.md](renderer.md#config--synuiconfig)):
`target` — the element (or a list of them; never a string); `baseURL` — the
site's folder; `manifest` — its name or path when it isn't `manifest.json` there. Anything else goes in
`options`. `SynUI.version` is the renderer's version.

Or with no script of your own — a boot block, read when the page has loaded. It is the same config as JSON,
without `target` (JSON can't hold an element): it always renders into `#root`.

```html
<div id="root"></div>
<script type="application/json" id="syn-boot">{"baseURL": "https://cdn.example.com/sites/acme/v3/", "manifest": "site.json", "options": {"vars": {"api": "https://api.example.com"}}}</script>
<script src="https://cdn.jsdelivr.net/gh/ridoysheikh/synui-cdn@2.0.0/dist/synui.js"></script>
```

A server that already read the manifest and this page's file for the HTML (SEO, `writeDocument`) passes them
along as `"options": {"files": {"manifest.json": "…", "pages/home.json": "…"}}` — they aren't fetched again.

## Prerendering

When the site has settled and nothing has loaded for 300 ms, `window.__synReady` turns `true` — a headless
browser waits for it, then saves the target's HTML. Put that HTML inside the target on the next request and
the page shows before the script runs. Capture with `"options": {"injections": false}`, so injected code stays off.

## Releasing to the CDN

`npm run release` publishes the current version to [ridoysheikh/synui-cdn](https://github.com/ridoysheikh/synui-cdn)
(`gh` signed in on the machine):

```bash
npm run release              # build, then push README.md + docs/ + dist/ to synui-cdn as one commit, tagged <version>
npm run release -- --dry-run # build and prepare it in a temp folder; push nothing
```

- The CDN repo holds **only** `README.md` (from `scripts/cdn-readme.md`), `docs/` (the renderer's — the
  builder's pages left out) and `dist/` (`synui.js`, `synui.css`, fonts). Nothing else ever goes there.
- Each version is one tag, **never replaced**: the script refuses a version already there — bump it.
- Always pin the version in the URL (`@2.0.0`): the files never change and are cached forever. A page moves to
  a new renderer by changing the version in its two URLs.
- The repo is made once: `gh repo create ridoysheikh/synui-cdn --public`.

The script is a classic script, so it loads from any origin; jsDelivr sends the fonts with
`Access-Control-Allow-Origin`, so they load on any domain. The site's own files are read with plain GETs from `baseURL` — public, like the script.

Locally, `npm run cdn` serves the built files on `http://localhost:5190/` (after `npm run build:renderer`).
