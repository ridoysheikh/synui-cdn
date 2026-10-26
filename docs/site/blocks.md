# Blocks

Every block kind, by what sort of thing it is. **Holds** = takes child blocks.
Every block also takes `style` ([styles.md](styles.md)), `data`, `each`, `if` and `on`
([views.md](views.md)). `blockCatalog()` returns all of this as data (the builder's
"Copy for AI").

## Layout

| Type | Holds | Fields | What |
| --- | :-: | --- | --- |
| `Section` | ✓ | `width`, `tag`, `image`, `video`, `wash`, `still` | A full-width band; its content sits in a centred column (layout settings apply to that column). `video`: an .mp4 / .webm playing muted and looping behind it. |
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
| `Image` | `src`, `alt`, `ratio` (square · portrait · landscape · video · wide), `fit` (cover · contain · fill · none), `focus` (the part kept in view: center, top, bottom-left…), `href` | A picture, optionally a link (the link is then the block: it takes the style and clips the picture). |
| `Icon` | `name`, `size`, `label` | Any Iconify icon — [icons.md](icons.md). |
| `Video` | `src`, `poster`, `auto`, `loop`, `controls`, `ratio`, `fit`, `focus`, `label` | YouTube, Vimeo or a file; muted autoplay loop for heroes. `fit: "cover"` fills any shape, embedded players too. |
| `Magnifier` (Zoom picture) | `src`, `alt`, `zoom` (inside · lens), `scale`, `full`, `ratio`, `fit` | Magnifies under the pointer; tap to zoom on touch; full screen. |
| `Compare` (Before / after) | `before`, `after`, `beforeLabel`, `afterLabel`, `start`, `ratio` | Two pictures with a handle between. |

## Actions

| Type | Fields | What |
| --- | --- | --- |
| `Button` | `label`, `href`, `variant` (default · outline · secondary · ghost · link), `size` (sm · default · lg · xl), `full`, `icon`, `iconAt`, `iconSize` (auto · sm · md · lg · xl), `says`, `count` | Goes to a page or runs steps (`click`). No label = a square icon button; `says` for screen readers; `count` shows a number on its corner when above 0 (`{{ global.saved \| count }}`). Outline and ghost take the colour of what they sit on (light on a dark band), with tints of it on hover and while open; the icon follows the text size unless `iconSize` says. |
| `ToggleButton` | `key`, `global`, `value`, `icon`, `iconOn`, `label`, `labelOn`, `variant`, `size`, `iconSize`, `says` | Puts `value` in the state list `key` or takes it out — a wishlist heart, compare, follow, like — or flips a yes / no (no `value`). Looks different while on; events `on`, `off`. |
| `Link` | `label`, `href` | A text link. |
| `FloatingButton` | `icon`, `iconSize`, `label`, `href`, `top`, `corner`, `after` | Fixed in a corner — WhatsApp, call, back to top; shows after scrolling `after`. |

## List and data

| Type | Holds | Fields | What |
| --- | :-: | --- | --- |
| `List` | ✓ | `source`, `api`, `params`, `items`, `as`, `limit`, `paging`, `pageSize`, `empty`, `rowsWhen` | Repeats its blocks per item — [data.md](data.md#lists). `rowsWhen` (a condition, `query.view == 'list'`): one item per row while it holds. |
| `States` | ✓ | `of`, `preview` | Shows one Case for a piece of data. |
| `Case` | ✓ | `when` | loading · empty · error · ready. |
| `Skeleton` | | `shape`, `count`, `columns`, `lines` | Shimmering stand-ins. |

## Filter

Controls that narrow, sort and page a list, keeping what's picked in the page address
(`?brand=acme,zen&sort=new&page=2`) — or, for `Choice` and `Range`, a state (`saveTo`: `address` ·
`page` · `site`). The page's data binds `{{ query.* }}` and loads again — [data.md](data.md#filtered-lists).

| Type | Fields | What |
| --- | --- | --- |
| `Search` (Search box) | `param` (`q`), `placeholder`, `goTo`, `live`, `label` | Search words; `live` searches as you type; `goTo` sends them to a results page (a header's box). Event `submit`. |
| `Choice` (Choices) | `title`, `param`, `saveTo`, `look` (list · buttons · swatches · select · stars · links), `multiple`, `options`, `items`, `labelAt`, `valueAt`, `countAt`, `hideCounts`, `all`, `more`, `searchable`, `folds` | Options to pick: checkbox filters with counts, chips, colour swatches, a sort dropdown, "4★ & up", category links, a grid / list switch, a size picker. `options` one per line, `Label = value` (an icon name as the label shows the icon); or `items` from a list (an API's facets). Event `change`. |
| `Range` | `title`, `min`, `max`, `step`, `paramMin`, `paramMax`, `saveTo`, `money`, `folds` | From–to with a two-handled slider and boxes — a price range. Event `change`. |
| `ActiveFilters` (Chosen filters) | `params` (`brand, color = Colour`), `clear` | What's picked, as chips that remove one each, and "Clear all". Nothing when nothing is picked. |
| `Pager` (Page numbers) | `total`, `size`, `pages`, `param` (`page`), `look` (numbers · simple), `scrollTo` | Page numbers for results the page loads a page at a time. Event `change`. |

## Form

| Type | Fields | What |
| --- | --- | --- |
| `Form` | `form`, `columns` | A form file — [forms.md](forms.md). |
| `Stepper` (Number stepper) | `key`, `global`, `min`, `max`, `step`, `label` | A number with − / +, kept in a state: how many, guests, nights. Event `change`. |

## Overlay

| Type | Holds | Fields | What |
| --- | :-: | --- | --- |
| `Dialog` | ✓ | `title`, `description`, `size`, `place`, `header`, `close`, `closeIcon`, `scrim`, `state`, `closeOnLink`, `keepOpen` | A window over the page — [overlays.md](overlays.md). |
| `Sheet` | ✓ | `title`, `description`, `side`, `size`, `inline`, + the same | A panel from an edge — cart, filters, phone menu. `inline` (tablet · desktop): on screens that wide it sits in place instead (a filter sidebar), sliding in on smaller ones. |
| `Dropdown` | ✓ | `label`, `variant`, `size`, `align`, `icon`, `iconSize`, `chevron`, `state` | A button opening a short menu. Events `open`, `close`. |
| `MenuItem` | | `label`, `href`, `danger`, `icon` | One line of a dropdown (`click`). |
| `Tabs` / `Tab` | ✓ | `Tab.label` | Each Tab is one panel. |
| `Accordion` | ✓ | `title`, `open` | A title that opens — FAQs, size guides. |
| `CloseButton` | | `label`, `variant`, `size` | Closes the dialog or sheet it sits in. |

## Navigation

| Type | Fields | What |
| --- | --- | --- |
| `Logo` | `text`, `src` | Store logo or name, linking home. |
| `NavMenu` (Menu) | `menu`, `vertical`, `look` (links · pills · underline · dropdown · mega), `columns`, `pictures` | A menu from `manifest.nav`. Entries with `children` open a dropdown, or a full-width **mega menu** (each child a column — `image`, `description`, `badge`, its own `children` as links). Vertical menus fold children open in place (phone menus). Links take the menu's text colour (quieter at rest, full on hover and for the page you're on); the menu's `when` states go to each link. |
| `CartButton` | `href`, `label`, `variant`, `size`, `icon`, `iconSize` | Cart icon (a bag unless `icon` says) with the item count. |
| `Breadcrumbs` | `items`, `home`, `separator` | Home › … › this page, from `items` (`{ label, href }`) or the address; tells search engines too when `seo.siteUrl` is set. |

## Motion

| Type | Fields | What |
| --- | --- | --- |
| `CountUp` (Counting number) | `value`, `from`, `duration`, `prefix`, `suffix`, `decimals`, `plain`, `tag` | Counts up when it scrolls into view — "1,200+". |
| `AnimatedText` (Moving text) | `text`, `effect` (words · letters · typewriter · rotate · shimmer), `words`, `loop`, `speed`, `tag` | Rises word by word or letter by letter, types itself, swaps words after the text, or shimmers. |
| `ScrollProgress` (Reading progress) | `place`, `thickness` | A bar along the top or bottom filling as the page is read; its colour is the text colour (`tone`, `color`). |

Any block can also move: appear, move with the scroll, follow the pointer — [styles.md](styles.md#motion).

## Commerce

| Type | Fields | What |
| --- | --- | --- |
| `ProductCard` | `product`, `href`, `showAdd` | Picture, name, price, add to cart (`add`). |
| `Price` | `amount`, `compareAt`, `size` | Price, regular price crossed out when discounted. |
| `AddToCart` | `product`, `label`, `qty` | Puts the product (`{{ product }}`, `{{ item }}`) in the cart — `qty` of them (`{{ state.qty }}` from a Number stepper) (`click`). |
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
as a row), marquees stop, counting numbers and moving text show their end, devices asking
for less motion get none.
