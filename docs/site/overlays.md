# Dialogs, sheets and menus

Dialogs and sheets are designed like any block — Radix underneath, not a fixed look.

```json
{ "id": "menuSheet", "type": "Sheet",
  "props": { "title": "Menu", "side": "left", "size": "sm", "header": "title", "closeIcon": "lucide:x",
             "scrim": "blur", "state": "menuOpen", "closeOnLink": true },
  "style": { "padding": "lg", "gap": "md", "surface": "card" },
  "on": { "link": [{ "do": "toast", "text": "Going to {{ payload.text }}" }] },
  "children": [{ "id": "nav", "type": "NavMenu", "props": { "menu": "main", "vertical": true } }] }
```

## Design

- **Style tab** (style set `overlay`): the panel's padding, gap, alignment, surface,
  text colour, border, corners, shadow.
- **Fields**:

  | Field | Values |
  | --- | --- |
  | `size` | `sm` · `md` · `lg` · `xl` · `full` (dialog width, sheet width or height) |
  | `place` (dialog) | `center` · `top` · `bottom` |
  | `side` (sheet) | `right` · `left` · `bottom` · `top` |
  | `header` | `title` · `line` (title and a line) · `none` |
  | `close`, `closeIcon` | `icon` · `none`; any icon name |
  | `scrim` | `dim` · `dark` · `blur` · `light` · `none` |
  | `title`, `description` | The title row's text (also read by screen readers) |

- On the canvas one shows open while it or a block inside is selected (the page behind
  stays clickable), as a small marker when closed; "Try it" runs it for real.

## Opening and closing

- `open` / `close` steps by id ([steps.md](steps.md)); `CloseButton` inside; Esc; the close icon.
- A click outside closes it, unless **`keepOpen`** ("Ignore outside") — Esc and the close
  control still work.
- **`closeOnLink`** ("Links close it"): a link or button inside closes it after doing its
  own job — the phone menu, a cart's Checkout.
- A `navigate` step closes every overlay.

## Tied to a site-wide key

`state` ("Open while") names a key in `manifest.state` (the builder's + makes `<id>Open`,
false at the start). The key is true exactly while it's open:

- `{{ global.menuOpen }}` works in any condition;
- a `set` step on the key opens or closes it;
- `open` / `close` steps aimed at its id write the key.

## Events

| Event | When | `payload` |
| --- | --- | --- |
| `open` / `close` | It opened / closed | — |
| `click` | A click anywhere inside | — |
| `outside` | A click outside (also with `keepOpen`) | — |
| `link` | A link or button among its blocks | `{ text, href }` |

Dropdowns take `state` too, with `open` / `close` events; their items are `MenuItem`s.
