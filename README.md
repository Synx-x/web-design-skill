# web-design

Design method for AI coding agents. One skill, eleven required sub-skills,
a mandatory pre-build manifest, and a post-build confirmation gate.

> Quick start: `npx skills add https://github.com/Synx-x/web-design-skill -g -y`,
> then ask your agent to build a page or component. The skill triggers on
> its own description, no explicit invocation needed.

## Why this skill

Anthropic's `frontend-design` skill covers taste and direction. This skill
is the method around it: the gate that runs before any UI code, not a
style guide on its own. It enforces a written scope spec, a batched
question set instead of guessed defaults, a signed-off pre-build manifest,
and a post-build confirmation naming every dependency's outcome.

## What's included

### The skill: web-design
Single entry point. Ask for any web design task and it runs before code:
tooling check, confidence check, scope spec, taste/Advisor Mode direction,
wireframe or variant sequence, component discovery, pre-build manifest,
build, and a post-build confirmation.

### Required sub-skills

| Sub-skill | Purpose |
|-----------|---------|
| `huashu-design` | Advisor Mode, Brand Asset Protocol, variant comparisons, deck/motion output |
| `frontend-design` | Aesthetic direction within token constraints |
| `spline-3d-integration` | Embeds a pre-authored Spline scene |
| `svg-animations` | Animated SVG: path, stroke, morph |
| `gsap-core` / `gsap-timeline` / `gsap-plugins` / `gsap-react` | Motion and sequenced animation |
| `excalidraw-diagram` | Visual wireframes |
| `brainstorming` | Layout exploration, design tradeoffs |
| `higgsfield-generate` | Hero image on a full-aesthetic build |

Full trigger conditions and install commands are in `SKILL.md`'s Required
Sub-Skills table.

### Rules the skill enforces

- No dependency gets an N/A verdict for being absent. Install it instead.
- A design direction never stands in for the batched question set the
  user is meant to answer.
- Picking a variant is not the same as signing off on the pre-build
  manifest. Both need their own explicit approval.
- The Tweaks Bar is mandatory on every build, static or single-file
  included, and gets a named list of the controls it exposes, not `N/A`.
- Every build ends with a printed confirmation naming the outcome of the
  hero image, the Tweaks Bar, validation, and the design ledger.
- A 3D task asks whether a Spline scene already exists to embed before
  defaulting to a hand-rolled library.

## Installation

### Option 1: skills CLI (recommended)

```bash
npx skills add https://github.com/Synx-x/web-design-skill -g -y
```

Drop `-g` to install into the current project instead of globally.

### Option 2: git clone into a project

```bash
git clone https://github.com/Synx-x/web-design-skill .claude/skills/web-design
```

Adjust the target path for your harness (`.codex/skills/`, `.cursor/skills/`,
`~/.agents/skills/`, etc).

## Usage

No slash command. Ask for the task directly:

```
Design a landing page for a coffee subscription service.
Add a pricing card component to my existing React app.
```

The skill announces itself, runs its tooling gate, then stops and asks a
batched question set before doing anything else.

## Requires

- [Impeccable](https://github.com/pbakaus/impeccable) and the
  [Taste Skill](https://github.com/Leonxlnx/taste-skill), both npx-installed.
  The skill's own Step -1 installs whichever is missing. Neither has a
  valid N/A path.
- An agent/harness that supports the [skills CLI](https://skills.sh/)
  convention.

## Supported tools

Anything the [skills CLI](https://skills.sh/) targets: Claude Code, Codex
CLI, Cursor, and the other agents it lists with `npx skills add -a '*'`.

## Contributing

Open an issue or PR at
[github.com/Synx-x/web-design-skill](https://github.com/Synx-x/web-design-skill).

## Repository layout

- `SKILL.md`: the full method
- `references/`: taste-library and Wide-Net Variant Sequence detail, the
  DESIGN.md schema, WCAG contrast checks, component-source registries, the
  design-quality tooling command reference, and a micro-details checklist
