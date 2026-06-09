---
name: figma-build-design
description: Builds or updates screens in Figma through the linked design system using native Figma MCP workflows. Use when creating or revising UI in Figma, reusing DS components and live library variables, inspecting example screens, running a mandatory preflight in the target file, enforcing library-first composition, and stopping cleanly when required assets are not confirmed available.
---

# Figma Build Design

This skill enforces a strict workflow for working with a design system in Figma via MCP. It prevents the agent from wasting time on manual redraws, false conclusions about missing library assets, and chaotic Figma searches.

## First Run

Before reading anything else, determine whether the skill is configured:

- `references/config.md` does **not exist**, **or** its **Library Collections** section still lists the placeholder `todo-collection-name` → the skill is **not configured**. Read `references/setup.md` and follow only it.
- Otherwise → the skill is configured. Continue to Quick Start.

Edge case: if `config.md` is missing but `config.example.md` has been filled in directly (its `todo-collection-name` placeholder is gone), the user edited the template by mistake — recover it first per `references/setup.md` → Recovering an Edited Template.

## Quick Start

1. Read [references/ds-structure.md](references/ds-structure.md) in full.
2. Read [references/workflow.md](references/workflow.md).
3. If the task is screen/page level, open an example screen before preflight — its link is in `config.md` → **DS Links** → **Example Layouts** (`ds-structure.md` explains how to choose).
4. Run [references/preflight.md](references/preflight.md) in the target Figma file.
5. If preflight fails, follow [references/fallback-policy.md](references/fallback-policy.md).
6. For precise Figma MCP behavior, use [references/figma-mcp-native-paths.md](references/figma-mcp-native-paths.md).

## Hard Rules

- Work in `strict-library-first` mode unless the user explicitly allows rescue mode.
- Do not conclude that `variables` or `text styles` are unavailable until you have checked the live target file and example screens.
- Do not use `search_design_system` as the sole source of truth.
- For variables, always start with `teamLibrary`.
- For text styles, always start by inspecting example screens and already-built screens.
- For screen/page tasks, example layout references are mandatory, not optional.
- Do not manually redraw what can be imported or safely reused as an instance.
- Vary instances only through their defined properties (variant, boolean, text, instance-swap) and text content — never resize internals, swap fonts, or restyle child layers. Use the component built for the role; do not repurpose one component as another. If a role has no component, use system-bound custom composition or stop — never bend an unrelated instance to fit.
- Compose incrementally — one major section per `use_figma` call — and validate each section before the next; do not report done until the build is validated (workflow.md → Validation). Large single-shot scripts are a primary failure mode.
- If the live variables path is not confirmed, do not use local snapshot/json fallback.
- If a required asset is not confirmed live, stop or enter fallback only according to skill rules.

## Compatibility

This skill defines the workflow. For Plugin API details, also load Figma's companion MCP skills (not part of this repo):

- Before every `use_figma` call, load the **figma-use** skill (0–1 color range, font recipe, page switching, return node IDs, and other critical rules).
- For building a screen from code, also load **figma-generate-design** (Code Connect discovery, `search_design_system`, incremental composition).
- If the screen needs images loaded from external URLs, a parallel `generate_figma_design` call is required (`use_figma` cannot load external URLs; described in figma-generate-design). Images already present as DS image fills, components, or vectors do not need this.

If a companion skill is not installed locally, load it from the Figma MCP resource instead: `skill://figma/figma-use/SKILL.md` or `skill://figma/figma-generate-design/SKILL.md`.

## Main Path

The authoritative order of operations lives in [references/workflow.md](references/workflow.md). In short:

1. **Build an asset map** — confirm library collections, control variables, control component import, and text style/token usage on the example screen.
2. **Compose through the system, incrementally** — one major section per `use_figma` call: import and bind variables and components, reuse instances and component properties, follow example-screen composition, and default to auto layout for new containers.
3. **Validate each section** — screenshot the result and confirm instances, bound variables, and styles were used, with no hardcoded value where a matching token exists and no sanctioned component skipped (details in [references/workflow.md](references/workflow.md) → Validation). Fix the mapping before the next section.
4. **Stop cleanly on broken assumptions** — if library access, the live variables path, or the text style path is not confirmed, do not silently fall back to manual rescue; stop or enter fallback only per [references/fallback-policy.md](references/fallback-policy.md). If a text override is needed inside a system instance, verify the font load path first.

## When to Read References

- [references/config.md](references/config.md) — user-specific data (DS links, control assets, example screen), created from `config.example.md` during setup and git-ignored. **Never overwritten by skill updates.**
- [references/ds-structure.md](references/ds-structure.md) — source map and decision priorities.
- [references/workflow.md](references/workflow.md) — the authoritative action order.
- [references/preflight.md](references/preflight.md) — run before any build.
- [references/fallback-policy.md](references/fallback-policy.md) — when an asset path is not confirmed.
- [references/figma-mcp-native-paths.md](references/figma-mcp-native-paths.md) — Figma MCP routes confirmed in practice.
- [references/setup.md](references/setup.md) — initial configuration only.
