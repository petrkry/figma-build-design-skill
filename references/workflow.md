# Workflow

## Goal

Build screens through the connected design system, not through manual redraws.

## Required Order

(Discovery, source-of-truth, and build-resolution orderings live in `ds-structure.md` → Decision Priorities.)

1. Determine the task type:
   foundation;
   primitive;
   composed block;
   whole screen/page.
2. If the task is whole screen/page:
   open an assembled layout example from the `Example Layouts` section.
3. Run `preflight.md` in the target file.
4. Build a map of available assets:
   component keys;
   control variables;
   text styles;
   example screens with bound variables and style usage.
5. Only then start composition — one major section per `use_figma` call, validating each (see Validation) before the next.

## Library-First Rule

Compose library-first. For the exact, verified call sequences — variables via `teamLibrary`, text styles via `textStyleId`, components by key, token usage from `boundVariables` on real screens — follow [figma-mcp-native-paths.md](figma-mcp-native-paths.md). Bind, don't copy: bind values via `setBoundVariable`; copy a raw value only when no matching token exists and binding is impossible.

## Validation

After each section, and again before reporting the build done, confirm:

- the section uses component instances, bound library variables, and text styles — not hardcoded values where a matching token exists;
- no sanctioned component or token was skipped in favor of a manual primitive;
- spacing, typography, color, and states match the example screen and task intent;
- new containers use auto layout;
- any custom-built block (no DS match) stays token-bound, style-bound, in auto layout, and is recorded as custom (`fallback-policy.md` → System-bound custom composition).

Catch clipping and overflow deterministically, not by screenshot: a child's bounds should stay within its parent; a clipping frame should not hide content; a text node should not be truncated unless intended. Then use a screenshot as a coarse second pass for what structural checks can't see. If a check fails, fix it before moving on — do not accumulate drift across sections.

## What Not to Do

- Do not start whole screen/page composition without viewing an example.
- Do not manually redraw a component if an instance or master component can be imported.
- Do not enter manual rescue mode without an explicit basis in `fallback-policy.md`. (System-bound custom composition is not rescue — it's a sanctioned path.)

## What This Workflow Fixes

- Reduces the risk of the false conclusion "nothing in the library".
- Reduces the risk of manual assembly instead of library reuse.
- Makes screen composition closer to real product screens, not just isolated components.
- Speeds up subsequent work because the asset map is already built after the first preflight.
