# Exports

Two packages, ES modules with types.

| Import | What |
| --- | --- |
| `@synui/renderer` | `SynUI(config)`, the block system, and everything in `/core`. |
| `@synui/renderer/core` | The site format, bindings, scripts and the cart — no React. |
| `@synui/renderer/ui` | The UI components sites use (shadcn / Radix). |
| `@synui/renderer/internals` | What `@synui/builder` needs beyond the public API. Not for sites; may change in any minor version. |
| `@synui/renderer/styles.css` · `/fonts.css` | The site's compiled stylesheet; the fonts. |
| `@synui/builder` | `SynUIBuilder(config)` and the headless tools (checks, libraries, forms from endpoints, `blockCatalog`). **Private.** |
| `@synui/builder/styles.css` | The site's stylesheet plus the builder's own classes. |

The CDN script (`synui.js`) puts the same `SynUI` on `window` ([cdn.md](cdn.md)).

## `@synui/renderer/core` (also from `@synui/renderer`)

| Export | What | Doc |
| --- | --- | --- |
| Types: `Manifest`, `SiteInfo`, `Route`, `PageFile`, `ComponentFile`, `ComponentInput`, `ViewNode`, `StyleProps`, `ScreenStyle`, `FormFile`, `FormField`, `Theme`, `StateKey`, `Injection`, `DocumentFile`, `CodeItem`, `CodeType`, `DocumentCode`, `DocumentEntry`, `SiteDocument`, `NavItem`, `SiteSeo`, `Redirect` | The site files | [site/files.md](../site/files.md) |
| Types: `Registry`, `Server`, `AuthScheme`, `Endpoint`, `Param`, `Pagination`, `Schema`, `Flow`, `Step`, `StepKind`, `DataSource` | The API registry and steps | [site/api-registry.md](../site/api-registry.md) |
| `REGISTRY_PATH`, `STEP_LABEL`, `pathParams`, `endpointParams`, `schemaPaths`, `schemaAt`, `deref`, `inferSchema`, `mergeSchema`, `bodyFromSchema` | Registry helpers | [site/api-registry.md](../site/api-registry.md) |
| `siteInfo(manifest, vars?)` | What `{{ site.* }}` reads | [site/files.md](../site/files.md) |
| `SiteSource`, `HttpSource`, `MemorySource`, `loadAllFiles`, `scriptPathFor`, `componentPath`, `formPath` | Reading site files | [renderer.md](renderer.md#sources--for-tools) |
| `pageDocument`, `PageDocumentConfig`, `PageDocument` | One address's page HTML on the server: status, redirect, title, SEO tags, structured data, code | [renderer.md](renderer.md#every-pages-html) |
| `writeDocument`, `documentCode`, `DocumentParts` | Write those parts into the HTML you send (or the older inline `manifest.document`) | [renderer.md](renderer.md#every-pages-html) |
| `codeItems`, `itemMatch`, `runsIn`, `itemHtml`, `itemPlace`, `pageSeo`, `seoTags`, `metaAttr`, `jsonldText`, `outputItems`, `runFunction`, `functionContext`, `reads`, `PageSeo`, `FunctionOutput` | The site's code items: all of them (older forms read in), which hold where, their HTML, the SEO worked out for a page, functions | [site/page-html.md](../site/page-html.md) |
| `allRoutes`, `findRoute`, `mapRoute`, `addRoute`, `layoutOf`, `standsAlone`, `matchPattern`, `routeFor`, `FlatRoute` | Routes (`routeFor`: the route an address shows, with its params) | [site/routes.md](../site/routes.md) |
| `walk`, `findNode`, `findParent`, `mapNodes`, `updateNode`, `removeNode`, `insertNode`, `shiftNode`, `cloneNode`, `collectIds`, `newId` | Editing view trees | — |
| `resolve`, `test`, `get`, `filters`, `formatMoney`, `Scope` | Bindings and conditions | [site/views.md](../site/views.md) |
| `loadScript`, `fn`, `ScriptModule` | Loading page scripts | [site/custom-code.md](../site/custom-code.md) |
| `browserCart`, `CartApi`, `CartItem`, `Cart`, `CartLine`, `Product`, `Collection`, `Money` | The built-in cart and the shapes the commerce blocks read | [site/data.md](../site/data.md#the-cart) |

## `@synui/renderer`

| Export | What | Doc |
| --- | --- | --- |
| `SynUI`, `SynUIConfig`, `SynUIOptions`, `SynUIApp`, `SynUITarget`, `mountInto`, `version`, `SiteRouter` | A site as an object | [renderer.md](renderer.md) |
| `defineBlock`, `getBlock`, `allBlocks`, `BlockDef`, `BlockRenderProps`, `FieldSpec`, `FieldKind` | Blocks | [extending.md](extending.md) |
| `BLOCK_KINDS`, `KIND_DESCRIPTION`, `BlockKind` | Block kinds | [extending.md](extending.md) |
| `styleClasses`, `STYLE_OPTIONS` | Style settings → classes; every option | [site/styles.md](../site/styles.md) |
| `useRuntime`, `usePage`, `NodeList`, `NodeView` | For custom blocks | [extending.md](extending.md) |

Importing it registers every built-in block.

## `@synui/renderer/ui`

`Badge`, `Button`, `Card`, `Checkbox`, `Collapsible`, `DropdownMenu`, `IconButton`, `Input`,
`Label`, `Pagination`, `Select`, `Separator`, `SonnToaster` (sonner), `Textarea` (and their
parts), `PortalContainerContext` / `usePortalContainer` (overlays open inside the builder's
canvas iframe), plus `cn`. shadcn components on Radix.

## `@synui/builder`

| Export | What | Doc |
| --- | --- | --- |
| `SynUIBuilder`, `SynUIBuilderConfig`, `SynUIBuilderApp` | The builder as an object | [builder.md](builder.md) |
| `SynUIBuilderOptions` | The builder's `options` | [builder.md](builder.md) |
| `MediaAPI`, `MediaFile`, `MediaQuery`, `MediaPage`, `MediaUpload`, `MediaChange`, `MediaContext` | The `mediaAPI` config and its functions' shapes | [media.md](media.md) |
| `SiteLoader`, `mergeApis` | The `loader` config (the site to edit); merging the `apis` list into a registry | [builder.md](builder.md) |
| `TemplateLibrary`, `TemplatePack` | One `templates` entry (`url` / `load` / `json`); a library as one JSON | [../site/libraries.md](../site/libraries.md#in-the-builder) |
| `lintSite`, `Suggestion` | Checks | [site/checks.md](../site/checks.md) |
| `openLibrary`, `bundleOf`, `codeIn`, `LIBRARY_KINDS`, `KIND_NAMES`, `LibraryFile`, `LibraryItem`, `LibraryItemKind`, `LibraryRef`, `LoadedLibrary`, `LibrarySource`, `Bundle`, `FlowFile` | Libraries | [site/libraries.md](../site/libraries.md) |
| `formFromEndpoint` | A form file from an endpoint's body schema | [site/forms.md](../site/forms.md) |
| `blockCatalog()` | Every block, field, style option and step as data (for AI) | [site/blocks.md](../site/blocks.md) |

The builder's own UI (panels, dialogs, popovers, the model behind them) lives inside `@synui/builder` and is
not exported.
