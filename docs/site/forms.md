# Forms

A form file holds the fields, their checks and what happens on submit. A `Form`
block shows it (`props.form`: a name in `forms/`, `columns`).

```json
{
  "load": { "api": "account.me" },
  "fields": [
    { "name": "name", "label": "Name", "type": "text", "required": true },
    { "name": "phone", "label": "Phone", "type": "phone", "required": true },
    { "name": "district", "label": "District", "type": "select",
      "optionsFrom": { "api": "places.districts", "label": "name", "value": "id" } },
    { "name": "note", "label": "Note", "type": "textarea", "span": 2 }
  ],
  "submit": {
    "steps": [{ "do": "call", "api": "account.update", "body": "{{ values }}" }],
    "label": "Save",
    "success": "Saved.",
    "redirect": "/account"
  }
}
```

## Fields

| Field | What |
| --- | --- |
| `name`, `label`, `type` | `text` · `email` · `phone` · `number` · `password` (hides what is typed) · `textarea` · `select` · `checkbox` |
| `placeholder`, `help` | Hints. |
| `required`, `min`, `max`, `pattern` | Checks, run before submit. |
| `options` | A select's choices: `[{ label, value }]`. |
| `optionsFrom` | A select's choices from an API: `{ api, params?, label, value }` naming the item fields. |
| `default` | Its value before anyone types. |
| `span` | `1` or `2` grid columns on wide screens. |

## How it looks

The `Form` block's own settings (`props`): `fieldSize` (`sm` · `default` · `lg` — as tall as a site button of the
same size), `fieldLook` (`bordered` default · `underline` · `flat` (filled) · `faded` (filled and outlined) ·
`ghost` (nothing until typed in)), `labels` (`above` default · `inside` — the label becomes the field's hint and is
kept for screen readers), and the button: `submitLook` and `submitSize` (the Button block's looks and sizes),
`submitFull`, `submitAlign` (`start` · `center` · `end`).

Colours, edges, corners and the focus ring are **field variables** — the Theme's *Form fields* for every form
(the site's CSS file), the block's Style → *Fields* for one form (`style.vars`):

| Variable | Default | What |
| --- | --- | --- |
| `--field` | `transparent` | Background. See-through at rest, on hover, in focus and when the browser autofills. |
| `--field-hover`, `--field-focus` | follow `--field` | Background on hover / in focus. |
| `--field-fill` | 6% of the text colour | Background of the `flat` and `faded` looks. |
| `--field-text`, `--field-label` | the text colour around | Typed text; labels. |
| `--field-placeholder` | that, fainter | Hint text, a select's chevron. |
| `--field-border`, `--field-border-hover` | slate | The edge. |
| `--field-border-focus` | `var(--primary)` | The edge in focus. |
| `--field-border-width` | `1px` | |
| `--field-radius` | `--radius` less 2px | Corners — the same as a site button's. |
| `--field-ring` | a glow of `--field-border-focus` | The ring round a field in focus. |
| `--field-ring-width` | `3px` | |
| `--field-check`, `--field-check-mark` | brand, text on brand | A ticked checkbox. |

The ones that follow have no line in the CSS file until you set them, so a band or a form that sets only
`--field` or `--field-border-focus` gets matching hover, focus and ring colours, and a field on a dark band takes
that band's text colour.

## Submit

- `steps` run with `{{ values }}` — usually one `call` ([steps.md](steps.md)), or `action`:
  a function exported by a script.
- `success` shows after it worked; `redirect` goes to a route.
- `load` fills the fields from an API first (each field takes the answer's value of the same name).

## Status

`{{ forms.name.submitting }}`, `{{ forms.name.sent }}`, `{{ forms.name.error }}`;
`loading.forms.name` / `failed.forms.name` while `load` runs. Fields shimmer while loading.

From an endpoint: the builder (and `formFromEndpoint`) makes the fields from its body schema.
