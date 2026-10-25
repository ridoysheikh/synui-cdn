# Blocks

Every block kind, by what sort of thing it is. **Holds** = takes child blocks.
Every block also takes `style` ([styles.md](styles.md)), `data`, `each`, `if` and `on`
([views.md](views.md)). `blockCatalog()` returns all of this as data (the builder's
"Copy for AI").

## Layout

| Type | Holds | Fields | What |
| --- | :-: | --- | --- |
| `Section` | ✓ | `width`, `tag`, `image`, `wash`, `still` | A full-width band; its content sits in a centred column (layout settings apply to that column). |
| `Stack` | ✓ | — | Blocks in a row or a column. |
| `Grid` | ✓ | — | Equal columns, changing per screen size. |
| `Box` | ✓ | — | A card-like panel. |
| `Spacer` | | `size` | Empty space. |
| `Divider` | | — | A thin line. |
| `Outlet` (Page slot) | ✓ | — | Where the page shows in a layout, or a sub-page in its parent. |
| `Component` | ✓ | `component` + its inputs | A reusable piece — [components.md](components.md). |
| `Slot` | | — | Inside a component: where each use's blocks show. |
| `Carousel` | ✓ | `perView`, `perViewTablet`, `perViewPhone`, `peek`, `effect` (slide · fade), `arrows`, `prevIcon`, `nextIcon`, `dots`, `autoplay`, `loop` | Slides that swipe; each child is a slide (a child with `each` makes one per item). Autoplay stops on hover. Event `change`. |
| `Marquee` | ✓ | `speed`, `direction`, `space`, `pause`, `fade` | An endless running line — offer strips, logos. Pauses on hover. |

## Text

| Type | Fields | What |
| --- | --- | --- |
| `Heading` | `text`, `level` (1–4) | A title. |
| `Text` | `text`, `size` | A paragraph. |
| `Badge` | `text`, `variant` | A small label, like "New". |
| `Countdown` | `mode` (date · per visitor), `until`, `minutes`, `look`, `days`, `short`, `ended`, `hide` | Time left; says, hides or runs steps (`end`) when over. |

## Media

| Type | Fields | What |
| --- | --- | --- |
| `Image` | `src`, `alt`, `ratio`, `fit`, `href` | A picture, optionally a link. |
| `Icon` | `name`, `size`, `label` | Any Iconify icon — [icons.md](icons.md). |
| `Video` | `src`, `poster`, `auto`, `loop`, `controls`, `ratio`, `label` | YouTube, Vimeo or a file; muted autoplay loop for heroes. |
| `Magnifier` (Zoom picture) | `src`, `alt`, `zoom` (inside · lens), `scale`, `full`, `ratio`, `fit` | Magnifies under the pointer; tap to zoom on touch; full screen. |
| `Compare` (Before / after) | `before`, `after`, `beforeLabel`, `afterLabel`, `start`, `ratio` | Two pictures with a handle between. |

## Actions

| Type | Fields | What |
| --- | --- | --- |
| `Button` | `label`, `href`, `variant`, `size`, `full`, `icon`, `iconAt`, `says` | Goes to a page or runs steps (`click`). No label = icon button; `says` for screen readers. |
| `Link` | `label`, `href` | A text link. |
| `FloatingButton` | `icon`, `label`, `href`, `top`, `corner`, `after` | Fixed in a corner — WhatsApp, call, back to top; shows after scrolling `after`. |

## List and data

| Type | Holds | Fields | What |
| --- | :-: | --- | --- |
| `List` | ✓ | `source`, `api`, `params`, `items`, `as`, `limit`, `paging`, `pageSize`, `empty` | Repeats its blocks per item — [data.md](data.md#lists). |
| `States` | ✓ | `of`, `preview` | Shows one Case for a piece of data. |
| `Case` | ✓ | `when` | loading · empty · error · ready. |
| `Skeleton` | | `shape`, `count`, `columns`, `lines` | Shimmering stand-ins. |

## Form

| Type | Fields | What |
| --- | --- | --- |
| `Form` | `form`, `columns` | A form file — [forms.md](forms.md). |

## Overlay

| Type | Holds | Fields | What |
| --- | :-: | --- | --- |
| `Dialog` | ✓ | `title`, `description`, `size`, `place`, `header`, `close`, `closeIcon`, `scrim`, `state`, `closeOnLink`, `keepOpen` | A window over the page — [overlays.md](overlays.md). |
| `Sheet` | ✓ | `title`, `description`, `side`, `size`, + the same | A panel from an edge — cart, filters, phone menu. |
| `Dropdown` | ✓ | `label`, `variant`, `align`, `icon`, `chevron`, `state` | A button opening a short menu. Events `open`, `close`. |
| `MenuItem` | | `label`, `href`, `danger`, `icon` | One line of a dropdown (`click`). |
| `Tabs` / `Tab` | ✓ | `Tab.label` | Each Tab is one panel. |
| `Accordion` | ✓ | `title`, `open` | A title that opens — FAQs, size guides. |
| `CloseButton` | | `label` | Closes the dialog or sheet it sits in. |

## Navigation

| Type | Fields | What |
| --- | --- | --- |
| `Logo` | `text`, `src` | Store logo or name, linking home. |
| `NavMenu` (Menu) | `menu`, `vertical`, `look` (links · pills · underline · dropdown · mega), `columns`, `pictures` | A menu from `manifest.nav`. Entries with `children` open a dropdown, or a full-width **mega menu** (each child a column — `image`, `description`, `badge`, its own `children` as links). Vertical menus fold children open in place (phone menus). |
| `CartButton` | `href`, `label` | Cart icon with the item count. |

## Commerce

| Type | Fields | What |
| --- | --- | --- |
| `ProductCard` | `product`, `href`, `showAdd` | Picture, name, price, add to cart (`add`). |
| `Price` | `amount`, `compareAt`, `size` | Price, regular price crossed out when discounted. |
| `AddToCart` | `product`, `label` | Puts the product (`{{ product }}`, `{{ item }}`) in the cart (`click`). |
| `CartLines` | `empty` | Cart items with quantity controls. |
| `CartSummary` | `checkoutHref`, `note` | Subtotal and checkout button. |
| `Gallery` (Picture gallery) | `image`, `images`, `alt`, `thumbs`, `zoom`, `full`, `ratio`, `fit` | Big picture with zoom and full screen, thumbnails, swipe (`change`). |
| `Rating` (Star rating) | `value`, `count`, `out`, `size`, `showValue` | Stars filled to a rating. |
| `GoalBar` | `value`, `goal`, `text`, `done`, `money` | "{left} more for free delivery". |

## Embed

| Type | Fields | What |
| --- | --- | --- |
| `Html` (Custom HTML) | `html`, `height`, `title` | Your own HTML, in a sandboxed iframe. |

In the builder no block autoplays or runs away: carousels sit still (a fading one shows
as a row), marquees stop, devices asking for less motion get none.
