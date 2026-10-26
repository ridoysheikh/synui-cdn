# Style settings

A block's `style` picks from fixed scales, so a site never needs a Tailwind rebuild.
Each block kind offers only the settings that fit it (its style set), and the Style tab
shows them in groups.

```json
"style": {
  "display": "flex", "direction": "row", "align": "center", "justify": "between", "gap": "md",
  "padding": "lg", "surface": "card", "radius": "xl", "shadow": "md",
  "phone": { "direction": "column", "padding": "sm" }
}
```

Spacing steps (`Scale`): `none` · `xs` · `sm` · `md` · `lg` · `xl`. Spacing, corners,
shadow, width, min height and text size also take a CSS variable: `"--section-pad"`.

## Groups

| Group | Settings |
| --- | --- |
| **Layout** (what's inside) | `display` (flex · grid · block · inline-flex · inline), `direction` (row · column · row-mobile-column · row-reverse · column-reverse), `wrap`, `align`, `justify`, `alignContent`, `gap`, `gapX`, `gapY`, `columns` (1–6), `rows` (1–6), `flow` (row · column · dense) |
| **In its parent** | `alignSelf`, `grow` (fill · grow · keep · auto · none), `order` (first · last · 1–6), `span` (1–6 · full), `rowSpan`, `marginTop`, `marginBottom`, `marginX` (`auto` centres), `hide` (phone · tablet · desktop) |
| **Size** | `width` (full · content · narrow · wide · auto · fit · half · third · two-thirds · quarter), `maxWidth`, `height`, `minHeight`, `aspect` (square · video · photo · portrait · wide), `overflow` (visible · hidden · auto · scroll-x · scroll-y · clip · `clip-x` — cut at the sides only, sticky still works), `noScrollbar`, `snap` (x · y · none — scrolling stops at each block), `snapAlign` (start · center · end, on the blocks inside) |
| **Space inside** | `padding`, `paddingX`, `paddingY` |
| **Position** | `position` (static · relative · absolute · fixed · sticky), `anchor` (9 points), `offset`, `layer` (back · base · raised · front · top), `sticky` |
| **Text** | `fontSize` (xs … 6xl), `weight`, `textAlign`, `font` (sans · serif · mono), `lineHeight`, `letterSpacing`, `textTransform`, `italic`, `decoration`, `textWrap` (wrap · balance · pretty · nowrap · truncate), `clamp` (1–6 lines) |
| **Look** | `surface` (background · card · muted · primary · secondary · `transparent` · `glass` — see-through and frosted), `tone`, `bg`, `color`, `opacity`, `radius`, `shadow`, `border`, `borderWidth`, `borderSides`, `borderStyle`, `borderColor` |
| **Effects** | `clip` (circle · ellipse · triangle · diamond · hexagon · star · arch · slant-top · slant-bottom · wave · ticket), `mask` (fade-top · fade-bottom · fade-left · fade-right · fade-x · fade-y · vignette), `blend` (multiply · screen · overlay · darken · lighten · difference · luminosity), `blur` (sm … xl), `backdrop` (frosted glass), `grayscale`, `invert`, `brightness`, `contrast`, `saturate`, `rotate`, `zoom`, `cursor`, `passThrough` (clicks go through) |
| **Motion** | `enter`, `delay`, `speed`, `ease`, `stagger`, `replay`, `scroll`, `scrollBy`, `hover`, `press`, `loop`, `morph` — see below |
| **States** | `when`: its look while hovered, pressed, focused, open, current or stuck — see below |
| **Variables** | `vars`: CSS variables for this block and what's inside — [theme.md](theme.md) |

A `Section` applies the Layout group to its inner centred column, not the full-width band.

**Pinning a block**: `position: "absolute"` with an `anchor` pins it inside its parent
(a badge on a picture's corner); the builder makes the parent `relative` for you.

**Colours** (`bg`, `color`, `borderColor`): a theme colour or variable name
(`"primary"`, `"brand-gold"`), `#hex`, `rgb()` / `hsl()`, or `transparent`. They win over `surface` / `tone`.

- **See-through**: `"primary/20"`, `"#000000/50"` — that colour, 20% / 50% solid.
- **Gradients**: `bg` takes `linear-gradient(…)`, `radial-gradient(…)`, `conic-gradient(…)`; a gradient
  `color` fills the text itself. `"loop": "gradient"` makes a gradient drift.
- **A picture on a coloured card**: `"blend": "multiply"` drops its white background.

**Shapes and fades** (`clip`, `mask`) work on any block: a round avatar, a hexagon photo, a
video fading out at the bottom, a row of cards fading at its edges.

## Screen sizes

`style` is the default, on every screen. Each size changes only what it sets, for that
range alone:

| Key | Range |
| --- | --- |
| `desktop` | 1024px and wider |
| `tablet` | 768–1023px |
| `phone` | under 768px |

Sizes can change layout, place in the parent, size, overflow and snapping, padding, margins,
text size / weight / spacing / lines, opacity, corners, shadow, position, **colours** (`bg`, `color`,
`borderColor` — a light header that turns dark on phones) and **border width** (`borderWidth`, on the
default's sides). Effects, motion and states are the same on every screen. Older `columns: { base, md, lg }`
are read as a default plus sizes.

In the builder the Style tab's **Default · Desktop · Tablet · Phone** switch picks what
the controls change (and moves the canvas to that size); each control shows what it
follows ("As default: M"), and each size counts its changes, with a Reset.

## States

`when` gives a block a look of its own in a state, over the rest of its style:

```json
"style": {
  "surface": "card", "border": true,
  "when": {
    "hover": { "bg": "primary", "color": "primary-foreground", "shadow": "md" },
    "press": { "zoom": "95" },
    "stuck": { "shadow": "lg", "bg": "background/90" }
  }
}
```

| State | While |
| --- | --- |
| `hover` | the pointer is over it (only on devices with a pointer) |
| `press` | it's held down — a tap or a click |
| `focus` | it's reached with the keyboard, or someone types in it (a search box) |
| `open` | the menu, dropdown or panel it opens is open |
| `current` | it links to the page that's showing, or is the chosen tab / option, or a toggle that's on |
| `stuck` | a sticky block (`sticky`, or `position: "sticky"`) has reached its edge after the page scrolled |

Each state can set `bg`, `color`, `borderColor` (colours as above, gradients aside), `shadow` (none · sm · md ·
lg · xl · 2xl), `opacity`, `zoom` (90 · 95 · 105 · 110) and `decoration` (underline · none). A state wins over the
block's own look and the kit's (a button's own hover colour); colours ease in. A block made of items — `NavMenu`,
`Tabs` — gives its states to each item: `hover` is the link under the pointer, `current` the page you're on.

In the builder the Style tab's **State** picker (under the screen sizes) chooses which look the controls change; the
canvas shows that look on the selected block while you edit it. Picking a theme background for a state also sets
its text colour (brand → brand's text) when none is set.

`hover` / `press` in Motion are ready-made movements (lift, grow, tilt…); `when` is the colours. Both work together.

## Motion

| Setting | Values |
| --- | --- |
| `enter` | fade · fade-up · fade-down · slide-left · slide-right · zoom · blur · pop · flip · rotate · reveal-up · reveal-left — on scrolling into view (reveals wipe it in) |
| `delay` | 0 · 100 · 200 · 300 · 500 · 800 ms |
| `speed` | fast · normal · slow |
| `ease` | smooth (default) · spring (overshoots and settles) · bounce · linear · snappy |
| `stagger` | its blocks appear one after another |
| `replay` | appears again each time it scrolls back into view |
| `scroll` | moves with the page's scroll while on screen: parallax · parallax-reverse · zoom-in · zoom-out · fade-in · fade-out · rotate · slide-left · slide-right · reveal · fill-x (a bar filling across) |
| `scrollBy` | sm · md · lg — how much `scroll` moves it |
| `hover` | lift · grow · zoom-image · glow · dim · tilt (3D, toward the pointer) · magnetic (pulled toward the pointer) · shine (a sheen sweeps across) · brighten |
| `press` | shrink · push — while it's pressed |
| `loop` | float · pulse · bounce · spin · wiggle · heartbeat · glow · gradient |
| `morph` | a name (may bind: `"pic-{{ item.id }}"`): with `theme.pages: "morph"`, the block with the same name on the next page is where it glides to — a card's picture becoming the big picture |

Appearing, scroll movement and the pointer each use their own CSS property (`transform`;
`translate` / `scale` / `rotate`; `transform`), so `enter` and `scroll` combine. `theme.motion`
(none · subtle · normal · lively) scales it all; devices asking for less motion get none.
Pages ease in on navigation (`theme.pages`), and `theme.scroll: "smooth"` makes the wheel
glide — [theme.md](theme.md).

## `class`

Free Tailwind classes for what the settings don't cover. They only style if they are
in the CSS build: the published kit compiles its own classes, not yours.

## The safelist

Every class the settings can produce is generated from `renderer/style.ts` into
`synui-renderer/style-safelist.css` (`npm run safelist`; also run by `dev`, `typecheck` and
`build`) and compiled into `@synui/renderer/styles.css`. Run it after changing a style map.
