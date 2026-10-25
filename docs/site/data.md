# Data and loading states

## Where data comes from

A page (`data`), a component (`data`, params may read `{{ props.x }}`) or any block
(`node.data`) loads named sources; each lands in scope under its name.

```json
"data": {
  "product": { "api": "products.get", "params": { "slug": "{{ params.slug }}" } },
  "reviews": { "api": "reviews.list", "params": { "product": "{{ params.slug }}" }, "wait": false, "keep": 60 },
  "related": "relatedProducts"
}
```

A string names a loader exported by the page script; it gets one `ctx`
(`params`, `query`, `site`, `session`, `api` — the registry, `api.call(name, { params, body })` — and `cart`).
Every source reads the site's own API registry: there is no other data channel.

| Option | What |
| --- | --- |
| `api`, `params` | A registry endpoint and its params — [api-registry.md](api-registry.md). |
| `wait: false` | Page data: show the page at once, fill this in when it arrives. |
| `keep: seconds` | Reuse the answer instead of asking again. |
| `ahead: false` | Page data: never load it before the click (changes fast or costs a lot). |

While loading or after failing: `{{ loading.name }}`, `{{ failed.name }}`.
`{{ loading.page }}` is true while a page loads its data again (a filter changed the URL, a reload).

## Loading ahead

`manifest.prefetch`: `hover` (default; also on focus) · `visible` (as soon as a link
shows) · `off`. A linked page's file and data load ahead; a thin bar shows while a
page that waits is loading.

## Lists

The `List` block repeats its blocks for every item from an API (`source: "api"`,
`api`, `params`) or from a list on the page (`source: "binding"`, `items: "{{ products }}"`). `limit`,
`paging` (`none` · `pages` · `more` — the first page only, page numbers, or "Load more", using the endpoint's `pagination`), `pageSize`, and an `empty` message.
An API list is always asked for one page at a time; its endpoint must be paged ([api-registry.md](api-registry.md#every-get-list-is-paged)).

## Filtered lists

Search, filters, sort, a grid / list switch and page numbers — a marketplace's listing page, a
job board, a directory, a blog archive — live in the **page address** (`/search?q=tea&brand=acme,zen&min=500&sort=price-asc&page=2`),
so a filtered page can be shared, bookmarked and gone Back to:

1. The page's data passes the address to the API — and loads again whenever it changes:
   ```json
   "data": { "results": { "api": "items.search", "params": {
     "q": "{{ query.q }}", "brand": "{{ query.brand }}", "min": "{{ query.min }}", "max": "{{ query.max }}",
     "sort": "{{ query.sort }}", "page": "{{ query.page }}", "limit": 24 } } }
   ```
   Empty values aren't sent. `{{ loading.page }}` is true while it loads again (a States block of `page`).
2. The Filter blocks change the address: `Search` (`q`), `Choice` (checkboxes with counts from the
   answer's facets — `"items": "{{ results.facets.brand }}"`; a sort dropdown; a grid / list switch),
   `Range` (`min`, `max`), `ActiveFilters` (chips), `Pager` (`page`, `"total": "{{ results.total }}"`).
   Any button can do the same with a `query` step ([steps.md](steps.md)).
3. A List shows `{{ results.items }}`; `"rowsWhen": "query.view == 'list'"` makes it one per row, and
   its card can switch design with `"if": "query.view == 'list'"`.
4. The filters sit down the left in a **Sheet** with `"inline": "desktop"`: in place on wide screens, a
   panel that slides in on phones (a "Filters" button with `"hide": ["desktop"]` opens it).

A filter changes go back to page 1 (the `page` key). Values a key holds several of are
comma-separated (`acme,zen`); the API reads them as it likes.

## Loading states

```json
{ "id": "s", "type": "States", "props": { "of": "reviews" }, "children": [
  { "id": "c1", "type": "Case", "props": { "when": "loading" }, "children": [
    { "id": "sk", "type": "Skeleton", "props": { "shape": "card", "count": 3 } } ] },
  { "id": "c2", "type": "Case", "props": { "when": "empty" }, "children": [ ... ] },
  { "id": "c3", "type": "Case", "props": { "when": "error" }, "children": [ ... ] },
  { "id": "c4", "type": "Case", "props": { "when": "ready" }, "children": [ ... ] }
] }
```

`States.of` follows a data name, a site-wide key (`global.x`), `page` or `forms.x`.
`Case.when`: `loading` · `empty` · `error` · `ready`. `Skeleton` draws shimmering stand-ins
(`shape`: `lines` · `heading` · `image` · `card` · `row` · `circle` · `button`; `count`, `columns`, `lines`).
The builder can pin a case on the canvas (`preview`) to design it.

## The cart

Built in, for any site that sells: `{{ cart.lines }}`, `{{ cart.count }}`, `{{ cart.subtotal }}`. It is kept in
the browser (`localStorage`, per site, shared by its tabs) and filled with what the page has in hand — an item
added carries its own `id`, `name`, `image`, `price` (`slug`, `inStock` too), so nothing is looked up. Your
checkout prices it again.

- The `cart` step adds `{{ product }}` / `{{ item }}`; an id alone adds one more of a line already there, or
  finds the page's product with that id ([steps.md](steps.md)).
- `ProductCard`, `AddToCart`, `CartLines`, `CartSummary` and `CartButton` use it ([blocks.md](blocks.md)).
- Saved-for-later lists, compare and the like are site-wide state lists, not carts — [states.md](states.md#saved-lists-wishlist-compare-follow).
- In scripts: `cart.add(item, qty?)`, `cart.update(lineId, qty)`, `cart.remove(lineId)`, `cart.clear()`.

