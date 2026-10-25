# States

The site's state store, like Zustand or Redux: **state** — one tree of values of any shape — that
every block reads, and **actions** — named steps that change it. (Styling variables are
something else — see [theme.md](theme.md).)

| | Site-wide | A page's |
| --- | --- | --- |
| Declared in | `manifest.state` | the page's `state` |
| Read as | `{{ global.cart.items }}` | `{{ state.filters.size }}` |
| Lives | across pages (and visits, if remembered) | while the page is open |
| Builder | States panel (left) | Page data & state (right) |

## Values of any shape

```json
"state": {
  "cart": { "items": [], "note": "" },
  "prefs": { "lang": "bn", "seen": ["eid-2026"] },
  "promoClosed": false
}
```

Text, numbers, yes / no, empty (`null`), lists and objects, nested to any depth. In the builder
each key is a tree: every field has a name, a type and a value; lists and objects open into
their fields. Pick **JSON** as a field's type to write it by hand, or switch the whole state to
**JSON** at the top of the panel.

## Changing a value

```json
{ "do": "set", "key": "cart.items", "global": true, "op": "push", "value": "{{ item.id }}" }
{ "do": "set", "key": "cart.items.0.qty", "global": true, "op": "add", "value": 1 }
{ "do": "toggle", "key": "filters.open" }
```

`key` is a path: the key, then fields and list positions inside it. Each level on the way is
copied, never changed in place; a missing level is made (a list for a number, else an object).

| `op` | Does |
| --- | --- |
| `set` (default) | Replace the value |
| `add` | Add a number (`-1` to subtract) |
| `push` / `pull` | Add to / remove from a list (no duplicates) |
| `clear` | Back to its starting value |

`toggle` flips a yes / no. The "Change a value" step offers every key and every field inside an
object. Page scripts get `set`, `merge` and `setGlobal` ([custom-code.md](custom-code.md)).

## Actions

Named lists of steps — `addToWishlist`, `setLanguage`, `signIn` — run from any button, form or
event with **Run an action** (`{ "do": "flow", "flow": "signIn", "input": { … } }`). They take
values (`{{ input.x }}` inside), change the state, call APIs and run other actions. They are
kept in the registry as `flows` ([steps.md](steps.md#actions)) and edited in States → Actions.

## Site-wide key options

```json
"persist": ["cart", "promoClosed"],
"stateKeys": {
  "user": { "from": { "api": "account.me" }, "ttl": 300, "description": "The signed-in customer" },
  "promoClosed": { "ttl": 86400 }
}
```

Options are per top-level key (each key's **Options** in the panel):

- `persist` — the browser remembers the key between visits.
- `stateKeys.key.from` — the key holds an API's answer: loaded when the site opens,
  again on sign-in / sign-out, and on a `refresh` step with `target: "global.key"`.
  `{{ loading.global.key }}` / `{{ failed.global.key }}` meanwhile.
- `stateKeys.key.ttl` — seconds a value lives; a loaded key then loads again, any other
  goes back to its start (remembered ones too).

## Overlays on a key

A Dialog, Sheet or Dropdown with `state` set is open exactly while that key is true —
see [overlays.md](overlays.md#tied-to-a-site-wide-key).

## In the builder

Each key shows its value on the canvas right now when it differs (change it to try a case
without saving), the blocks using it, and keys that blocks read but nothing sets ("Used, not set
up", with a one-click Set up). Renaming a key rewrites its uses, deep paths included.
