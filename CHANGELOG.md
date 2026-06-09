# Changelog

Notable changes per version. Newest first.

## [1.0.0] - 09.06.2026

### Added

- Validation phase after composition, with a completion gate: no hardcoded value where a matching token exists, and no sanctioned component skipped (`workflow.md` → Validation).
- Incremental composition rule: one major section per `use_figma` call, validated before the next.
- Canonical resolution order for any UI target (published instance → bound variable/style → local → system-bound custom) in `fallback-policy.md`.
- System-bound custom composition: a sanctioned path for building from primitives when no DS asset fits — with hygiene rules (bind tokens/styles, auto layout, reuse primitives, meaningful names) and a record of what was built custom, distinct from permission-gated manual rescue.
- Fallback to Figma MCP resource skills (`skill://figma/...`) when companion skills aren't installed locally.
- Deterministic structural checks in validation (child bounds within parent, no unintended clipping, no text truncation), with the screenshot demoted to a coarse second pass.
- Build Summary template reported on completion: custom blocks, substitutions, font fallbacks, and deviations to revisit.
- Instance-discipline rule: vary instances only through their defined properties and text content, never resizing internals, swapping fonts, or restyling child layers, and never repurposing one component as another.
- Font handling hardened: check `hasMissingFont`, note that the MCP runtime may not expose locally-installed fonts, and never silently swap to an arbitrary font (substitution is a recorded deviation).
- Setup response template added: standardises the first-run message structure, including the DS-links tip explaining why manual `config.md` filling maximises asset discovery.
- Edited-template safeguard: the template header now tells users to copy before editing, and first-run logic self-heals if `config.example.md` was filled in directly (preserves the data into `config.md`, restores the template).

### Changed

- README example prompts no longer ask the user to mention preflight or library rules (the skill enforces them automatically); Quick start now frames the two phases plainly — first run (required) and manual config filling (strongly recommended).
- Preflight and stop policy now flag missing write access (Dev seat / no edit access / MCP not connected) as a likely failure cause.
- `config.example.md` reorganised: hand-edited **DS Links** moved to the top, agent-filled keys/IDs grouped below and relabelled a "connectivity sample" (a canary, not a DS catalog) to set the right expectation. Section names and the setup sentinel are unchanged.
- Clarified that the "manual mode" guardrails mean manual *rescue*, distinct from sanctioned system-bound custom composition.
- Deduplication pass: each rule now has a single canonical home (with `SKILL.md` Hard Rules as the deliberate always-loaded summary); `ds-structure.md` trimmed (~120 → ~40 lines) with its three orderings consolidated into one **Decision Priorities** table; the circular "read ds-structure" instruction removed; and the unimplemented machine-readable inventory layer (`inventory-contract.md`) removed.


## [Unreleased] - 08.06.2026

### Added

- Strict `library-first` workflow for building Figma screens through a linked design system via Figma MCP.
- Mandatory preflight (`references/preflight.md`) and a stop/fallback policy (`references/fallback-policy.md`).
- Template-based configuration: `references/config.example.md` is copied to a git-ignored `references/config.md` during setup, so design-system keys stay out of version control.
- Codex skill metadata (`agents/openai.yaml`).
