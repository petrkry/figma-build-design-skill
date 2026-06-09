# Figma MCP Native Paths

This document records not theoretical but practically verified paths for working with a DS through Figma MCP. It is the canonical reference for the call sequences below; other skill files point here instead of restating them.

## What Has Proven Reliable

### Variables

Most reliable path:

1. `figma.teamLibrary.getAvailableLibraryVariableCollectionsAsync()`
2. `figma.teamLibrary.getVariablesInLibraryCollectionAsync(collection.key)`
3. `figma.variables.importVariableByKeyAsync(key)`
4. Binding via `setBoundVariable` or `setBoundVariableForPaint`

This is more reliable than drawing conclusions from local variable collections.

### Text Styles

Most reliable path:

1. Open the example screen.
2. Read `textStyleId` on real text nodes.
3. Get the style object and its `name`, `key`, `remote`.
4. Use this path as the source of truth.

Important:

- An empty `getLocalTextStylesAsync()` in the target file does not mean styles are absent.
- Real style usage is best observed on already-built screens.
- Style `name` values may repeat while `key` values differ.
- Therefore, the source of truth for style reuse is the style key actually found on the relevant example screen, not the name alone.

### Components

Most reliable path:

1. Inspect existing screens and instances.
2. Read `mainComponent` and `parent COMPONENT_SET`.
3. Import by key.
4. Reuse an existing instance if the import path is inconvenient but the instance is confirmed.

## Use With Caution

### `search_design_system`

Use as a supplementary signal, not the sole source of truth.

Reasons:

- Results may be incomplete for a given session.
- Variables/styles may be accessible via the live file path even when search returns nothing useful.
- Component discovery through existing screens is often faster and more reliable.

### `getAvailableLibraryStylesAsync`

This path does not work via `use_figma` in some environments. Verify in your environment before relying on it.

Practical rule:

- Do not build your workflow around library style listing as the only path.
- For text styles, use example screen inspection and bound style IDs.

## Current Control Assets

Current collections, components, styles, and variables are stored in `references/config.md`. They are not duplicated here.

After setup (see `references/setup.md`), `config.md` contains confirmed live data for the example screen, text styles, variables, and control component.

## Font Gotcha

The visual presence of a font in existing instances does not guarantee that the plugin can safely perform a text override.

Rule:

1. If no text override is needed, do not block composition with a font check.
2. If a text override is needed, run `listAvailableFontsAsync()` first, and check `hasMissingFont` on the target text node before editing it.
3. Run `loadFontAsync()` only with a family/style that was actually found. `loadFontAsync` cannot fetch fonts from the internet, and in the MCP runtime `listAvailableFontsAsync()` may not expose locally-installed fonts (often only cloud fonts), so a licensed project font can be unloadable via MCP even though it renders in the file.
4. If the needed font cannot load, never silently swap to an arbitrary font. Either keep the existing text (no override), substitute only with a DS-sanctioned font and record it as a deviation, or stop. The durable fix is org-level shared fonts.

## Practical Decision Tree

1. Need variables:
   go through `teamLibrary`.
2. Need text styles:
   go through example screens and `textStyleId`.
3. Need components:
   inspect existing screens and instances first, then import by key.
4. Need a whole screen/page:
   examples are required before composition starts.
