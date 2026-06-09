# Setup

Initial configuration of the skill. Run once; after that, the skill operates normally.

## Trigger Condition

This file is read when `references/config.md` is missing, or when its **Library Collections** section still lists the placeholder `todo-collection-name`. Once setup is complete, neither condition applies.

## Recovering an Edited Template

If `config.md` is missing but `config.example.md` no longer contains the `todo-collection-name` sentinel, the user filled the template directly instead of a copy. Recover before delivering the response template:

1. Preserve their work — copy `config.example.md` to `config.md` (their entries carry over).
2. Restore the pristine template — `git checkout references/config.example.md` (skip if this is not a git checkout; just tell the user the template was edited).
3. Continue against `config.md`; the next preflight surfaces any keys still missing.

## Response Template

Deliver this as your first message. Adapt the language to the user's language.

---

**Figma Build Design — first run**
The skill is not configured yet (`references/config.md` is missing). A one-time setup is needed for your design system.

**What I need from you**

1. **URL of any assembled screen** in your Figma project (working file or DS file).
   From one screen I can automatically extract library collection names, text style keys, component keys, and control variables.

2. **(Optional) DS links** — the more sections you cover, the more precisely the skill navigates your system during builds:
   - Foundation (colors, typography, spacing, radii, icons)
   - Base components (buttons, inputs, checkboxes, and similar primitives)
   - Complex components and patterns (headers, cards, navigation, etc.)
   - Additional example layouts

   > Each DS link becomes a direct navigation target. The fuller this map, the fewer missed assets and the more accurately the right component is chosen for each task. Filling `config.md` manually after auto-setup is the highest-leverage step for getting the most out of the skill.

**What happens next**
1. Copy the template to `references/config.md`
2. Read your example screen via Figma MCP
3. Fill the config with real names and keys
4. Confirm the skill is ready — you can then describe a screen to build right away

**Requirements**
- Figma MCP must be connected (Figma Desktop open with the relevant file)
- Edit access to the file is required (Dev seats are read-only)

---

Send the example screen URL, for example:
`https://www.figma.com/design/ABC123/My-App?node-id=1-234`

---

## How to Handle the Responses

Before calling `use_figma`, load the **figma-use** skill. `use_figma` requires an active Figma MCP connection.

If the call fails (Figma MCP not connected or no file access):
1. Inform the user of the connection issue.
2. Ask them to provide manually, as much as they have: library collection names; the example screen node ID and name; the name + key of at least one component; and, if known, one text style key and one variable key.
3. Fill in `config.md` directly with this data, skipping the automated read.
4. Note: text style and variable keys are usually not known without MCP. Fill what you have; the build still cannot start until a live preflight confirms the full minimum (see `preflight.md`). Once MCP is back, the first preflight surfaces the missing keys — record them in `config.md` then.

### Step 0. Create the live config

Copy `references/config.example.md` to `references/config.md`. All edits below are made in the live `config.md`, never in the template. `config.md` is git-ignored, so your DS keys stay out of version control.

### Step 1. Read the example screen

Via `use_figma` in the file from the provided URL:

1. Switch to the page containing the example screen (`setCurrentPageAsync`).
2. On several text nodes, read `textStyleId` → get style object → record `name`, `key`, `remote`.
3. On several frame and component nodes, read `boundVariables` → get variable object → record `name`, `key`, `variableCollectionId`.
4. Call `figma.teamLibrary.getAvailableLibraryVariableCollectionsAsync()` → record the names and keys of all visible collections.
5. On component instances, read `mainComponent` → record `name`, `key`, `id` for at least one.

### Step 2. Update config.md

Edit `references/config.md`, replacing all placeholder values with the data collected:

- `todo-collection-name` → actual library collection names (one or more).
- `todo-component-name` + `00000:00000` → actual `name`, `node`, `key` of a found component.
- `todo-screen-name` + placeholder file key/node → actual `name`, `file key`, `node` of the example screen.
- `todo-text-style-1`, `todo-text-style-2` → actual `name` and `key` of found text styles (add more lines if useful).
- `todo-variable-1`, `todo-variable-2` → actual `name` and `key` of found variables (add more lines if useful).

### Step 3. Update DS links in config.md (optional)

If the user provided links, fill in the `www.link.com` placeholders in `references/config.md` → **DS Links**. Can be skipped and done later.

## Completion Criteria

Setup is complete when `references/config.md` contains no `todo-*` values in name fields:

- library collection → real name;
- control component → real name, node, key;
- example screen → real name, file key, node;
- at least one control text style with a real key (capture a few if available);
- at least one control variable with a real key (capture a few if available).

Important: delete any `todo-text-style-*` and `todo-variable-*` lines that remain unfilled in `config.md`. Leftover `todo-*` slots will be read as missing assets on the next run.

Notify the user that the skill is configured, and proceed with the original task — run Quick Start from `SKILL.md`.
