# Components

A reusable piece (header, footer, product card) in one file. Edit it once and every
use follows.

```json
// components/promo-card.json   (listed in manifest.components as "promo-card")
{
  "name": "Promo card",
  "description": "A picture with a title and a button",
  "tags": ["card"],
  "inputs": [
    { "name": "title", "label": "Title", "kind": "text", "default": "Eid sale" },
    { "name": "image", "label": "Picture", "kind": "image" },
    { "name": "icon", "label": "Icon", "kind": "icon", "default": "lucide:gift" }
  ],
  "data": { "count": { "api": "products.count", "params": { "tag": "{{ props.title }}" } } },
  "view": [
    { "id": "t", "type": "Heading", "props": { "text": "{{ props.title }}" } },
    { "id": "body", "type": "Slot" }
  ]
}
```

A `Component` block uses it:

```json
{ "id": "eid", "type": "Component", "props": { "component": "promo-card", "title": "Eid sale" },
  "children": [{ "id": "x", "type": "Text", "props": { "text": "Up to 40% off" } }] }
```

- Inside, `{{ props.x }}` reads what the use passed (or the input's `default`).
- A `Slot` block shows the blocks put inside that use.
- `inputs[].kind`: `text` · `textarea` · `image` · `href` · `boolean` · `number` · `icon`.
- `data` loads per use; `{{ loading.name }}` / `{{ failed.name }}` as for pages.
- `tags` say what it can stand in for: the builder's **Swap** on a `header` block offers it.
- A use with no style of its own adds no box (`display: contents`): the component's blocks sit straight in the
  parent — a sticky header in a component sticks for the whole page, a component's card is a grid's cell. Give
  the use a style (padding, a width…) and it becomes a box again.

Sections (bands saved for reuse) are view snippets copied in, not linked; components stay linked.
