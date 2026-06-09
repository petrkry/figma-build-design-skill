# Figma Build Design

LLM agent skill for building Figma screens through a linked design system — library-first via Figma MCP, not manual redraws.

## What it does

- Preflight before each build: confirms library collections, variables, text styles, and control component.
- Library-first composition: import and bind tokens, reuse instances, follow example screens, build one section at a time.
- Validation gate: after each section, confirm instances and bound tokens were used — no hardcoded drift, no skipped components.
- Clear stop/fallback rules when assets aren't confirmed.

## Requirements

- Works with any agent that supports Agent Skills and can call the Figma MCP — Claude Code, Codex, Cursor, and similar.
- **Figma MCP connected.** The skill is non-functional without it. Easiest path on Claude Code: `claude plugin install figma@claude-plugins-official` (other agents: see Figma's MCP setup docs). The official Figma plugin also ships the companion skills below.
- **Companion skills** — **figma-use** (loaded before every `use_figma`) and **figma-generate-design** (code-to-design). They come with the Figma plugin; if absent, the skill loads them from the `skill://figma/...` MCP resource.
- **Write access** — building on canvas needs edit access to the file and a Full Figma seat (Dev seats are read-only).

## Install

Place this skill in your agent's skills directory so that `SKILL.md` sits at the folder's root.

### Via git (recommended — easy updates)

```bash
# Claude Code — personal skills (all projects)
git clone https://github.com/petrkry/figma-build-design-skill.git ~/.claude/skills/figma-build-design

# …or project-local skills (current project only)
git clone https://github.com/petrkry/figma-build-design-skill.git YOUR-PROJECT/.claude/skills/figma-build-design
```

For Codex (`~/.codex/skills/`), Cursor, or another agent, clone into that tool's skills directory instead.

### One clone for several agents (optional)

Several agents? Clone once and symlink the folder into each agent's skills directory — one source of truth, one shared `config.md`, and `git pull` updates them all.

```bash
REPO=~/figma-build-design   # wherever you cloned it
ln -s "$REPO" ~/.claude/skills/figma-build-design   # Claude Code
ln -s "$REPO" ~/.codex/skills/figma-build-design    # Codex
# Cursor and others: symlink into that agent's skills directory
```

### Manual

Download the repo as a ZIP (GitHub → **Code** → **Download ZIP**) and extract it into a `figma-build-design` folder inside your agent's skills directory.

## Quick start

**1. First run — required.** Install, invoke (`/figma-build-design`), and give one example screen URL. Setup runs automatically and fills `config.md` from that screen. This is the minimum to start building.

**2. Fill the config by hand — strongly recommended.** Add your DS section links to `config.md` (Foundation, base components, patterns, example layouts). The fuller this map, the more accurately the skill finds and reuses the right components — it's the single biggest lever on output quality.

`config.md` holds all your project-specific data (DS links, component/style/variable keys, example screen). It's git-ignored, so your DS keys stay out of version control.

## Usage

Once configured, drive a build in plain language. You don't need to mention preflight or library rules — the skill enforces them on its own:

> Using `<Figma file URL>`, build a settings screen from our design system.

Behind the scenes the agent runs the preflight, composes section by section from library components and bound tokens, validates each one, and reports a short build summary — including anything it couldn't do systemically. If required assets aren't confirmed, it stops and tells you why instead of silently improvising.

## Updating

```bash
cd <skill-folder> && git pull --ff-only
```

`config.md` is untracked, so it survives. See [CHANGELOG.md](CHANGELOG.md) for what changed.

To re-run setup (e.g. after a DS rename): delete `config.md` and invoke the skill again.

## Disclaimer

This project is not affiliated with, endorsed by, or sponsored by Figma. Figma is a trademark of its respective owner.

## License

MIT — see [LICENSE](LICENSE.txt).
