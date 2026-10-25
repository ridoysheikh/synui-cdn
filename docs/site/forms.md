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
| `name`, `label`, `type` | `text` · `email` · `phone` · `number` · `textarea` · `select` · `checkbox` |
| `placeholder`, `help` | Hints. |
| `required`, `min`, `max`, `pattern` | Checks, run before submit. |
| `options` | A select's choices: `[{ label, value }]`. |
| `optionsFrom` | A select's choices from an API: `{ api, params?, label, value }` naming the item fields. |
| `default` | Its value before anyone types. |
| `span` | `1` or `2` grid columns on wide screens. |

## Submit

- `steps` run with `{{ values }}` — usually one `call` ([steps.md](steps.md)), or `action`:
  a function exported by a script.
- `success` shows after it worked; `redirect` goes to a route.
- `load` fills the fields from an API first (each field takes the answer's value of the same name).

## Status

`{{ forms.name.submitting }}`, `{{ forms.name.sent }}`, `{{ forms.name.error }}`;
`loading.forms.name` / `failed.forms.name` while `load` runs. Fields shimmer while loading.

From an endpoint: the builder (and `formFromEndpoint`) makes the fields from its body schema.
