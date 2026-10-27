# Extending — your own blocks

`defineBlock` registers a block kind. Once registered it renders in sites, appears in
the builder's Add panel with its fields, style controls and events, and is described
in `blockCatalog()`.

```tsx
import { defineBlock } from "@synui/renderer"

defineBlock<{ text?: string; tone?: string }>({
  type: "Notice",
  label: "Notice",
  kind: "Text",
  description: "A one-line notice, like “Free delivery in Dhaka today”.",
  style: "text",
  events: ["click"],
  fields: [
    { name: "text", label: "Says", kind: "text" },
    { name: "tone", label: "Tone", kind: "select", options: ["info", "sale"], optionLabels: { info: "Info", sale: "Sale" } },
  ],
  create: () => ({ props: { text: "Free delivery today", tone: "info" } }),
  render: ({ props, className, attrs, on }) => (
    <p className={className} {...attrs} onClick={() => on.click?.()}>{props.text}</p>
  ),
})
```

Register blocks before the first render (import the file at your entry) — in the site
**and** in the app hosting the builder, so the canvas knows them too. Ship custom blocks as
their own module importing only `@synui/renderer`.

## `BlockDef`

| Field | What |
| --- | --- |
| `type` | Unique name, written in site files (`"type": "Notice"`). |
| `label`, `description` | Plain words for people and AI: what it shows or does. |
| `kind` | One of `BLOCK_KINDS`: Layout, Text, Media, Actions, List, Data, Form, Overlay, Navigation, Commerce, Embed. |
| `fields` | `FieldSpec[]` — the Content tab. |
| `style` | The style set it offers: `section`, `stack`, `grid`, `box`, `list`, `panel`, `form`, `carousel`, `marquee`, `text`, `media`, `action`, `nav`, `card`, `price`, `icon`, `overlay`, `spacer`, `divider`, `none`. Only those controls show. |
| `container` | Holds child blocks (rendered as `children`). |
| `inner` | Its children sit in an inner element: layout settings go there (`innerClass`). |
| `events`, `eventLabels` | Events it fires, and their plain names ("When it opens"). |
| `data` | Can load its own data (default yes). |
| `create` | Starting props / style / children when added. |
| `render` | The component. |

## `FieldSpec`

`{ name, label, kind, options?, optionLabels?, placeholder?, help?, when?, advanced?, logic? }`

`kind`: `text`, `textarea`, `number`, `select`, `boolean`, `image`, `href`, `icon`,
`form`, `nav`, `binding`, `code`, `api`, `params`, `component`, `data`, `state`.
`when: { field, is?, set? }` shows a field only when another one has one of those values (or is set).
`advanced: true` keeps a rarely changed field under the Content tab's **More options** until it's set.
`logic: true` marks a select, switch or number that changes what the block does (a data source, a range, site-wide
state), so it stays as it is inside a [locked part](../site/locked.md); look settings leave it out.

## `BlockRenderProps`

| Prop | What |
| --- | --- |
| `props` | The node's props, bindings already resolved. |
| `className` | Classes from its style settings and `class` — put on the root. |
| `attrs` | Spread on the root: editing hooks (`data-syn-node`) and its own colours. |
| `innerClass` | For `inner` blocks: the layout classes for the inner element. |
| `on` | `event → (payload?) => void`, running the node's steps. |
| `children` | Rendered child blocks (containers). |
| `scope` | Everything bindings can read (`product`, `item`, `global`…). |
| `node` | The raw node. |

Hooks: `useRuntime()` (site, `api`, `cart`, `editing`, state sites) and `usePage()` (the
page's forms, components and `run(steps)`). `NodeList` / `NodeView` render child nodes
with another scope (how List and Carousel repeat).

## Rules a block follows

- No markup from data: text is text, icons are CSS masks, custom HTML goes through the `Html` block.
- Respect `editing` — no autoplay, no navigation, a visible placeholder when empty.
- Respect `prefers-reduced-motion`.
- Classes from style settings must exist in the CSS: use the style system, or add your
  classes to the host's Tailwind build.
