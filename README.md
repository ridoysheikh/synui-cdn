# SynUI renderer — CDN &nbsp;`v3.1.0`

The built files of `@synui/renderer`: render a SynUI site in any HTML page — no npm, no bundler. This repo holds
only the released files (`dist/`) and the docs; each version is a tag.

| File | What |
| --- | --- |
| `dist/synui.js` | One classic script (React and the renderer inside); puts `SynUI` on `window` |
| `dist/synui.css` | The styles; the fonts (`*.woff2`) sit beside it |

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/ridoysheikh/synui-cdn@3.1.0/dist/synui.css">

<div id="app"></div>

<script src="https://cdn.jsdelivr.net/gh/ridoysheikh/synui-cdn@3.1.0/dist/synui.js"></script>
<script>
  SynUI({ target: document.getElementById("app"), baseURL: "https://s3.example.com/bucket/site-id/" }).render()
</script>
```

`baseURL` is the site's own public folder (its `manifest.json` names every file). Or, with no script of your
own, a boot block — the same config as JSON, rendered into `#root`:

```html
<div id="root"></div>
<script type="application/json" id="syn-boot">{"baseURL": "https://s3.example.com/bucket/site-id/"}</script>
<script src="https://cdn.jsdelivr.net/gh/ridoysheikh/synui-cdn@3.1.0/dist/synui.js"></script>
```

Always pin the version in the URL (`@3.1.0`): a released version never changes, so it can be cached
forever. Move a page to a new renderer by changing the version in its two URLs.

Docs: [docs/INDEX.md](docs/INDEX.md) — start with [docs/host/cdn.md](docs/host/cdn.md) and
[docs/host/renderer.md](docs/host/renderer.md).
