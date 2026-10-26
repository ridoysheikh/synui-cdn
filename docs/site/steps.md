# Steps

One action language for everything that happens: a button's click, a menu item, a
form's submit, a block's events, an action. A list of steps runs in order;
values may bind (`{{ }}`), and a call's answer is kept for the steps after it.

```json
"on": { "click": [
  { "do": "call", "api": "auth.login", "body": { "phone": "{{ values.phone }}" }, "as": "login",
    "onError": [{ "do": "toast", "text": "{{ error.message }}", "tone": "error" }] },
  { "do": "login", "token": "{{ login.token }}", "user": "{{ login.user }}" },
  { "do": "close", "target": "signin" },
  { "do": "navigate", "to": "/account" }
] }
```

| `do` | Fields | Does |
| --- | --- | --- |
| `call` | `api`, `params?`, `body?`, `headers?`, `as?`, `onError?` | Calls a registry endpoint; the answer is kept as `as` (default `result`). On failure runs `onError` with `{{ error.message / status / data }}` and **stops**; without `onError` the error shows as a toast. |
| `set` | `key`, `value?`, `global?`, `op?`, `max?` | Changes a state value — `key` may be a path inside it (`cart.items.0.qty`); `op`: `set` · `add` · `push` · `pull` · `toggle` (the item in or out of a list) · `clear`; `max` keeps only the newest of a list ([states.md](states.md)). |
| `toggle` | `key`, `global?` | Flips a yes / no (a path works here too). |
| `open` | `target` | Opens a dialog, sheet or dropdown by id. |
| `close` | `target?` | Closes it (no target: the one opened last). |
| `navigate` | `to` | Goes to an address; closes every overlay first. A full `https://` address (a payment page, another site) loads in the browser — in the builder's canvas it only says where it would go. |
| `toast` | `text`, `tone?` | A message: `success` · `error` · `info`. |
| `if` | `when`, `then`, `else?` | Branches on a [condition](views.md#conditions). |
| `flow` | `flow`, `input?` | **Run an action** (`{{ input.x }}` inside). A failed call inside stops the caller too. |
| `login` | `token`, `user?` | Signs the customer in: `{{ session.token }}` / `{{ session.user }}`, kept per site in the browser; page data and site-wide keys loaded `from` an API load again. |
| `logout` | — | Clears the session. |
| `cart` | `op`, `product?`, `qty?` | `add` · `remove` · `clear` on the built-in cart; `product` binds the item (`{{ product }}`) — [data.md](data.md#the-cart). |
| `refresh` | `target?` | Loads a List (by id) or a site-wide key (`global.key`) again; no target reloads the page's data. |
| `copy` | `text` | Copies to the clipboard. |
| `scroll` | `target` | Scrolls smoothly to a block by id. |
| `query` | `key?`, `value?`, `op?`, `replace?` | Changes the page address's search: `set` a key (empty removes it), `toggle` one value in or out of a key holding many (`?brand=acme,zen`), `clear` a key — or every key, given none. Anything but `page` goes back to the first page; Back undoes it unless `replace`. The page's data binding `{{ query.* }}` loads again — [data.md](data.md#filtered-lists). |
| `run` | `fn` | Calls a function exported by the page or site script — [custom-code.md](custom-code.md). |

Actions may run actions, up to 8 deep.

## Actions

Named step lists, reusable from any page — the site's actions, edited in States → Actions
([states.md](states.md#actions)). They are kept in the registry as `flows`:

```json
"flows": {
  "checkout": {
    "summary": "Place the order and empty the cart",
    "input": { "type": "object", "properties": { "values": { "type": "object" } } },
    "steps": [
      { "do": "call", "api": "orders.create", "body": "{{ input.values }}", "as": "order" },
      { "do": "cart", "op": "clear" },
      { "do": "navigate", "to": "/thanks?order={{ order.number }}" }
    ]
  }
}
```

## Where steps run

| Place | Event |
| --- | --- |
| Button, Menu item, Floating button, Add to cart | `click` |
| Toggle button | `on`, `off` |
| Search box | `submit` |
| Choices, Range, Page numbers, Number stepper | `change` |
| Product card | `add` |
| Dialog, Sheet | `open`, `close`, `click`, `outside`, `link` ([overlays.md](overlays.md)) |
| Dropdown | `open`, `close` |
| Carousel, Gallery | `change` |
| Countdown | `end` |
| Form | `submit.steps` ([forms.md](forms.md)) |

A string in place of steps (`"on": { "click": "addAll" }`) still names an exported function.
