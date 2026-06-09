# Fallback Policy

## Default Mode

`strict-library-first`

This means, for any UI target, resolve in this order:

1. Instance of a published DS component — import by key, or reuse a confirmed instance.
2. Bound library variable / text style — for values and typography.
3. Local file component / style — only if no published equivalent exists.
4. System-bound custom composition — only when nothing above fits; build from primitives but stay bound to DS tokens, styles, and auto layout (see Allowed Fallback Modes → System-bound custom composition).

Manual recreation of an asset that already exists in the DS is not allowed. Raw, un-tokenized work is rescue mode only and needs explicit permission.

## Allowed Fallback Modes

### 1. Instance-copy-only

Allowed when:

- the master component is difficult to import directly;
- but an already-existing instance in an accessible file can be read and reused without losing system integrity.

In this mode, you may:

- copy an instance;
- change only safe overrides — text content and the component's own properties, never internal geometry, fonts, or styling (see `SKILL.md` → Hard Rules);
- build the screen from system instances.

In this mode, you may not:

- replace tokenized colors with raw hex values without explicit permission;
- replace text styles with arbitrary local styles;
- substitute live library variables with a static JSON snapshot;
- manually assemble new components instead of available system ones.

### 2. Example-derived composition

Allowed when:

- the task is to build a whole screen/page;
- the library contains foundation and components;
- but composition is more convenient to derive from example screens.

In this mode, the layout example is used as a required operational reference.

### 3. System-bound custom composition

Allowed when:

- no DS component, style, or variable fits the need — verified against the discovery order and the nearest-neighbor check, not assumed;
- the block is genuinely novel for this design system.

This is a normal path, not a failure: building from primitives does not mean leaving the design system. Allowed without asking permission, provided the hygiene below holds.

In this mode, you must:

- bind spacing, color, and radius to DS variables;
- set text via DS text styles, not ad-hoc local font settings;
- build in auto layout, not absolute positioning;
- reuse DS primitives (icons, dividers, chips) inside the custom block;
- give layers meaningful names;
- record the block as custom in the build summary, noting what should later become a component.

In this mode, you may not:

- hardcode a value where a matching token exists;
- recreate from scratch an asset that already exists in the DS — use its instance instead;
- introduce a font or color outside the DS.

### 4. Manual rescue mode

Allowed only by explicit user request or when specified in advance in the task definition.

In this mode, you may:

- temporarily build individual containers by hand;
- use visually compatible values;
- close the task as a rescue implementation.

In this mode, you must explicitly record:

- which assets could not be obtained systemically;
- which blocks were built manually;
- what needs to be replaced with library assets later.

## When to Stop

Stop and report to the user if:

- `use_figma` cannot write at all (not just a missing asset) — suspect no edit access / Dev seat / MCP not connected;
- the target file cannot see library collections;
- the target file does not confirm the live variables path;
- the control component cannot be imported and there is no safe instance-copy fallback;
- the text style path is not confirmed and the task requires precise typography reuse;
- the project font is unavailable and the task requires text overrides inside system instances;
- the screen cannot be built systemically without manual rescue mode.

### Stop Report Template

When stopping, report to the user in the following format:

```
STOP — build is not possible in strict-library-first mode.

Reason: [one of: no write access (Dev seat / no edit access / MCP not connected) /
          library collections not visible / live variables path not confirmed /
          control component not importable / text style path not confirmed /
          font unavailable]

What was checked:
- Collections: [list visible collections or "not visible"]
- Component key tried: [key or "not tried"]
- Text style key from example screen: [key or "not found"]

What is needed to continue: [specific question or action]
```

## Build Summary (on completion)

When the build finishes but something deviated from pure library-first, report a short summary. Omit empty lines; if everything resolved through the library with no deviation, no summary is needed.

```
BUILD SUMMARY — completed with notes.

Custom blocks (no DS match): [list, or omit]
Substitutions: [wanted asset → what was used, or omit]
Font fallbacks: [text left default / font substituted, or omit]
Deviations to revisit: [token or component-canon deviations, or omit]
```

## How to Re-Run Setup

If preflight fails due to stale data (collection renamed, style key changed, component removed):

1. Delete `references/config.md`, or restore the sentinel by replacing the collection name under `config.md` → **Library Collections** with `todo-collection-name`.
2. The next time the skill is invoked, setup mode will trigger.
3. The agent re-reads the example screen and rebuilds all control assets in `config.md`.

## What Must Not Be Done

- Silently fall back to manual redraws.
- Silently substitute styles and variables with local surrogates.
- Conclude that the DS is not connected without running preflight.
- Ignore example screens on screen-level tasks.
