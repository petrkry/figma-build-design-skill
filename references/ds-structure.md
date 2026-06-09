# Design System Map

The agent's navigation map: what to open, in what order, and which source wins. It is a map, not a guarantee — it never confirms that an asset can be imported right now or that a font is editable via the API. Those are verified live by `preflight.md` before any build. The default assumption is that the DS is connected to the target file as a library, and the default mode is `strict-library-first` (see `fallback-policy.md`).

## Related Documents

- `workflow.md` — the authoritative action order.
- `preflight.md` — live library and control-asset checks.
- `fallback-policy.md` — resolution ladder and what's allowed on failure.
- `figma-mcp-native-paths.md` — verified Figma MCP call sequences (canonical for "how").

## Decision Priorities

Three distinct orderings — don't conflate them:

| Axis | Order | When you use it |
|---|---|---|
| **Discovery** — what to consult to learn the system | existing screens in the target file → example screens → foundation → base components → complex patterns → legacy | while exploring, before and during a build |
| **Source of truth** — which source wins when two disagree | live file & built screens → variables/tokens (values) → text styles (typography) → base components (structure/states) → complex components (composed layout) → example screens (screen composition) → legacy (compatibility only) | on conflict |
| **Build resolution** — what to place for each element | see `fallback-policy.md` → Default Mode (published instance → bound variable/style → local → system-bound custom) | when composing |

## How to Choose What to Open

- Typography, font family/size/weight, line-height, letter-spacing, text styles: open the `Typography` section.
- Color, background, border, radius, spacing, numeric values: open the `System` section.
- A single controlled UI entity: the relevant item from `Base Components`.
- A composed block (header, card, banner, content section): `Complex Components & Patterns`, or a custom section the user defined.
- A new screen or page: `Example Layouts` first, then relevant foundation and component links.
- Unclear task: start from the nearest layout example and nearest base component, then work up to the systemic source.

Section names under `config.md` → **DS Links** are user-authored and may be renamed, dropped, or extended. Match the task to the closest section by intent, not by exact name. All DS links live in `config.md` → **DS Links**.

## Notes

- Use example screens not just as visual references but as sources of composition principles, auto-layout rules, and real-screen token/style usage. For screen/page tasks they are required reading before composition (see `workflow.md`).
- Use legacy elements only if the task is explicitly tied to an old screen, or no current equivalent exists.
- Do not invent a new style/size/color/state, or a new pattern, when a suitable token, component, or example already exists — check the nearest compatible one first.
- If the task conflicts with an existing component, prefer DS consistency over local customization unless the task explicitly requires otherwise.
