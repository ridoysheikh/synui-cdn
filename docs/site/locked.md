# Locked parts

A library can ship parts a site may **restyle but not rewire** — a shop's checkout, its sign-in pages, a customer's
account, the forms and actions behind them. What they do (which API they call, what they check, when they show)
follows the host's rules; how they look is the site's.

```json
{ "id": "checkout", "type": "Section", "locked": { "by": "Acme", "note": "Checkout follows the shop's settings.", "sum": "3k1z…" },
  "children": [ … ] }
```

`locked` (`{ by, note?, sum? }`) goes on:

| Where | What it covers |
| --- | --- |
| a block (`ViewNode`) | the block and everything inside it |
| a page or layout file (`PageFile`) | its `data`, `state`, `script` and every block |
| a component file | its `inputs`, `data` and every block |
| a form file | its fields' names, types, checks and choices, `load`, and the submit's `steps`, `action`, `redirect` — not labels, placeholders, help or the button's words |
| a flow in `api/registry.json` | its `input` and `steps` |

## What can change, what can't

Inside a locked part **free**: `style` (every screen, every state, `style.vars`), `class`, plain text, pictures and
icons, and look settings (a block's select, switch and number fields — button look, columns, field size).
**Kept**: bindings (`{{ }}` anywhere), `data`, `if`, `each` / `as`, `on` (events and their steps), APIs, forms, links,
fields of kind binding · api · form · state · params · data · code · component · href · nav, fields a block marks
`logic: true` (a List's source, a filter's range, site-wide state), props a block doesn't declare, and which blocks
sit where (no adding, removing or moving inside it). The lock itself can't be taken off.

## Where it's enforced

- **The builder** refuses any write that changes a locked part's logic — typing in Code included — and says why;
  the inspector shows "Locked by …" and keeps those settings read-only (Delete is off).
- **`sum`** is the checksum of what the part does. `sealLocks(files)` (`@synui/builder`) stamps it when a library is
  built; the site checks (`lintSite`) report a part whose logic no longer matches as an error.
- **Replacing it** with another sealed, intact part (another design's checkout from the library) is allowed; a
  whole file may still be removed (the host decides whether it allows that).
- A host that must be sure checks on its server too: compare a locked part with its library original.

## In a library

Mark the item `"locked": "Acme"` in `library.json` (shown as a lock on its card), put `locked` in its files, and
run `sealLocks` over them when the library is built.
