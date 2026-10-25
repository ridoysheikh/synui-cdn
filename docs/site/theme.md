# The CSS file and the theme

A site's look lives in **one plain CSS file**, `manifest.css` (`styles/global.css`). The site
loads it as it is, on every page, after its own styles — so anything in it wins. Its `:root`
holds every variable the site draws with:

```css
:root {
  --background: hsl(210 20% 98%);
  --primary: #0f766e;            /* changed */
  --radius: 0.75rem;
  --font: Georgia, serif;
  --brand-gold: #c8a24a;         /* added — your own */
  --section-pad: 4rem;
}

.hero h1 { letter-spacing: -0.02em; }   /* any CSS */
```

A new site's file starts with **all the site's defaults** written out (page, brand, surfaces,
lines and fields, status, charts, greys, `--radius`, `--font`). Change a value, delete a line
(the site's built-in default shows again), or add your own variable and use it anywhere as
`var(--name)`. "Variables" always means these CSS variables; app values are
[states](states.md).

`theme.json` keeps only `motion`: `none` · `subtle` · `normal` (default) · `lively` —
[styles.md](styles.md#motion).

## In the builder

| Where | What |
| --- | --- |
| Code → `styles/global.css` | The file as text. |
| Theme | Pickers for the main colours (with contrast against the text on them), corners, font and motion; your own colour variables too. **Each picker writes its variable in the same file** — nothing is kept anywhere else. |
| Style controls | Spacing, corners, shadow, text size, width and height offer your own variables of the fitting kind beside their fixed steps: `"paddingY": "--section-pad"` → `padding-block: var(--section-pad)`. |
| Colour pickers | Your own colour variables, under "Yours": `"bg": "brand-gold"`. |
| A block's Style → Advanced → Variables here | `style.vars`: variables for one block and everything inside — another `primary` for one band: `"vars": { "primary": "#b91c1c" }`. |

## Older sites

A site from before the CSS file (colours, `radius`, `font`, `vars` and `css` in `theme.json`)
still renders as it did. The builder gives it the file when it opens: the defaults with the
theme's values written in, then its CSS — an unsaved change until it's saved. Taking a shop
template or another site's look brings its CSS file the same way.

## Fonts

Inter, Noto Sans Bengali and JetBrains Mono ship inside the kit's CSS, so a site never
needs a font CDN. Bangla text always uses Noto Sans Bengali, whatever `--font` says.
