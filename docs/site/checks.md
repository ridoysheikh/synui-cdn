# Checks

`lintSite(files)` (`@synui/builder`) reads every site file and returns suggestions — the
builder's Checks panel. Run it before publishing.

```ts
import { lintSite } from "@synui/builder"
const problems = lintSite(files).filter((s) => s.level === "error")
```

```ts
type Suggestion = {
  id: string
  level: "error" | "warning" | "tip"
  area: "seo" | "links" | "forms" | "logic" | "content" | "performance"
  message: string                 // a plain sentence saying what's wrong and what to do
  file?: string; route?: string; nodeId?: string
  fix?: { label: string; writes: Record<string, string> }   // one-click fix: files to write
}
```

## What it checks

| Area | Examples |
| --- | --- |
| logic | A step calls an API or flow not in the registry, opens an id no overlay has, runs a function the script doesn't export; a value read from `global` that nothing sets; a States block without data; a Case or Slot outside its parent; an endpoint on an undefined server or auth scheme |
| links | A link or `navigate` to an address with no route; a page nothing links to; a missing not-found route; a path declared twice; a route whose page file is missing |
| forms | A form that doesn't exist, does nothing when sent, says nothing after, or submits to a missing function |
| content | No level-1 heading, images without alt text, an icon not saved in `icons.json`, a component that isn't defined, sub-pages without a Page slot, a dynamic page that loads no data |
| seo | Missing or too long titles (60) and descriptions (160), duplicate titles, no share image, no `siteUrl` |
| performance | A page file over 24 KB |

The test app's `test-preview/__tests__/sites.test.ts` runs it on the demo site and every
catalog shop and fails on any error.
