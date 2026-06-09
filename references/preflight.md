# Preflight

This preflight is required before building any new screen in the target file.

Writing to canvas needs edit access and a Full seat. If the `use_figma` calls below fail entirely (rather than returning a missing asset), suspect no write access / Dev seat / MCP not connected, and stop with that diagnosis per `fallback-policy.md`.

## Step 1. Check Library Variable Access

Expected collections are listed in `references/config.md` → **Library Collections**.

1. `figma.teamLibrary.getAvailableLibraryVariableCollectionsAsync()`
2. Confirm that at least the collections listed in `config.md` are visible.
3. Get variables for the relevant collection:
   `figma.teamLibrary.getVariablesInLibraryCollectionAsync(collection.key)`
4. Import at least one control variable:
   `figma.variables.importVariableByKeyAsync(key)`

## Step 2. Check Text Styles

1. Open the example screen.
2. Read `textStyleId` on its text nodes.
3. Get the style object and record `name`, `key`, `remote`.

An empty `getLocalTextStylesAsync()` does not mean styles are absent, and a style `name` may repeat across different `key` values — see [figma-mcp-native-paths.md](figma-mcp-native-paths.md) for why the key found on the specific example screen is the source of truth, not the name.

## Step 3. Check Control Component

Import at least one component or component set by the key in `references/config.md` → **Control Component** (see [figma-mcp-native-paths.md](figma-mcp-native-paths.md) → Components). The import must succeed, not just be listed.

## Step 4. Check Control Styles and Variables on a Real Screen

Open the example screen from `references/config.md` → **Example Screen**, then confirm each style and variable from **Control Text Styles** and **Control Variables** actually resolves on it: read `textStyleId` / `boundVariables` on its nodes and match the recorded keys.

## Step 5. Check Font — Only if Direct Text Overrides Are Needed

If text will be changed inside imported instances or new text nodes:

1. Check `figma.listAvailableFontsAsync()`
2. Try `loadFontAsync()` only with a family/style actually found
3. If the project font fails to load, do not break the composition:
   - either use the instance without override,
   - or stop per `fallback-policy.md`

## Minimum Criteria to Start a Build

Screen composition may only begin if all of the following are confirmed:

- the target file sees the required library collections;
- the live variables path is confirmed via `teamLibrary`;
- at least one control component can be imported;
- at least one real example screen has been read;
- at least one control variable is found;
- at least one control text style is found or confirmed via the example screen.
