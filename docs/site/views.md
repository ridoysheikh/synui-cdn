# Views, bindings and conditions

A view is a tree of nodes. Every page, layout, component and section is one.

```json
{
  "id": "card", "type": "ProductCard",
  "props": { "product": "{{ item }}", "href": "/products/{{ item.slug }}" },
  "style": { "padding": "md", "radius": "lg" },
  "class": "ring-1 ring-border",
  "each": "products", "as": "item",
  "if": "item.inStock",
  "on": { "add": [{ "do": "toast", "text": "Added {{ item.name }}" }] },
  "children": []
}
```

| Field | What |
| --- | --- |
| `id` | Unique in its file; the builder and `open` / `close` steps address nodes by it. |
| `type` | A block type — [blocks.md](blocks.md). |
| `props` | The block's fields; any value may bind. |
| `style` | Style settings from fixed scales — [styles.md](styles.md). |
| `class` | Extra Tailwind classes (compiled only if in the CSS build). |
| `data` | Data this block loads for itself and what's inside — [data.md](data.md). |
| `each` / `as` | Repeat for every item of a list in scope (item named `item` unless `as`). |
| `if` | Show only when the condition holds. |
| `on` | Event → steps ([steps.md](steps.md)), or the name of an exported script function. |
| `tags` | What kind of piece it is (`header`, `hero`), for the builder's Swap. Not rendered. |
| `children` | Child nodes, for blocks that hold blocks. |

## Bindings

`{{ path | filter: arg }}` — a path lookup and a fixed set of filters. No `eval`.

- A string that is **one whole binding keeps its type**: `"{{ products }}"` is a list,
  `"{{ product.price }}"` a number. Mixed with text it becomes text.
- Literals: `'text'`, `"text"`, numbers, `true` / `false`.

| Filter | Example | Gives |
| --- | --- | --- |
| `money` | `{{ product.price \| money }}` | The price in the site's currency and locale — `BDT 1,250` for `en-BD` (or `money: 'USD'`) |
| `date` | `{{ order.at \| date: 'long' }}` | Date in the site's locale and timezone (`short` · `medium` · `long` · `full`) |
| `default` | `{{ user.name \| default: 'Guest' }}` | The fallback when empty |
| `then` | `{{ item.inStock \| then: 'In stock' \| default: 'Sold out' }}` | The text when the value is set (not empty, false or 0), else nothing — with `default`, an either-or |
| `count` | `{{ cart.lines \| count }}` | Length of a list |
| `first` / `last` | `{{ product.images \| first }}` | First / last item |
| `take` | `{{ global.recent \| take: 4 }}` | The first n items |
| `reverse` | `{{ global.recent \| reverse }}` | The list backwards (newest first) |
| `sum` | `{{ cart.lines \| sum: 'qty' }}` | Numbers added up, or one field of each item |
| `join` | `{{ tags \| join: ' · ' }}` | A list as text |
| `truncate` | `{{ text \| truncate: 60 }}` | Cut with `…` |
| `upper` / `lower` | | Case |
| `url` | `/search?q={{ q \| url }}` | URL-encoded |
| `json` | | JSON text |

## What's in scope

| Name | Where | What |
| --- | --- | --- |
| `site` | everywhere | `{ name, locale, currency, timezone, logo, url, nav }` (`url`: the site's address, `seo.siteUrl`) and every `vars` value — [files.md](files.md#manifestjson) |
| `cart` | everywhere | `{ lines, count, subtotal }` — [data.md](data.md#the-cart) |
| `route` | everywhere | `{ path }` — the current address |
| `session` | everywhere | `{ token, user }` after a `login` step |
| `global` | everywhere | Site-wide state |
| `state` | a page | The page's state |
| `params`, `query` | a page | Route params; the address's search (`?brand=acme,zen` → `query.brand`), changed by the `query` step and the Filter blocks |
| *data names* | a page / block / component | Loaded data, plus `loading.x`, `failed.x` |
| `props` | inside a component | What the use passed in |
| `item` (or `as`) | inside `each` / List | The current item |
| `values` | form submit steps | The form's values |
| `forms.name` | everywhere | `submitting`, `sent`, `error` |

## Conditions

`if` (and `if` steps' `when`) take:
`"path"` · `"!path"` · `"path == 'x'"` · `"path != 3"` · `"path > 0"` (`>=`, `<`, `<=`) ·
`"list has item.id"` / `"list lacks item.id"` (a list holds the item, or one with its id; a
comma-separated address value holds a part: `"query.brand has 'acme'"`).
An empty list counts as false. Braces are optional: `"{{ cart.count }}"` works too.
