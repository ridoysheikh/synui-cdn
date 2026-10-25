# Icons

Any [Iconify](https://icon-sets.iconify.design/) icon, named `set:name`:
`lucide:truck`, `tabler:heart`, `logos:visa`, `circle-flags:bd`, `fluent-emoji:gift`.

Used by the `Icon` block, buttons (`icon`, `iconAt`), dropdowns, menu items, floating
buttons, carousel arrows, the close control, and component inputs of kind `icon`.

## `icons.json`

A picked icon's drawing is copied into the site's `icons.json`, so the site never asks
Iconify:

```json
{ "lucide:truck": { "body": "<path …/>", "width": 24, "height": 24 } }
```

- An icon missing there still loads from the Iconify API, and Checks warns about it.
- Library items bring the drawings they use; the catalog script fetches them for its shops.

## Drawing

Icons are drawn as a CSS mask in the text colour (colour sets — logos, flags, emoji —
as a background picture), never inserted as markup. Older files' names without a set
(`truck`) still draw.

## The picker

Common icons first; any set from a dropdown (UI sets, brands, colour logos, flags,
emoji); search across them. Picking saves the drawing.
