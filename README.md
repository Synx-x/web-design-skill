# web-design

Design method for AI coding agents. 1 skill, 11 required sub-skills, a
mandatory pre-build manifest, and a post-build confirmation gate, for
AI-generated frontend design that doesn't skip its own steps silently.

> Quick start: `npx skills add https://github.com/Synx-x/web-design-skill -g -y`,
> then ask your agent to build a page or component. The skill self-triggers
> from its own description, no explicit invocation needed.

## Why this skill?

Anthropic's `frontend-design` skill is one input into taste and direction.
This skill is the method around it: the gate that runs before any UI code,
not a style guide on its own. It grew out of `scope-grill`'s written-spec
discipline and huashu-design's Advisor Mode, then hardened against failure
modes found by running it live against Codex, not by guessing at them.

web-design closes three separate silent-skip risks:
- A tooling or sub-skill dependency that's absent gets installed, not N/A'd
- A design direction invented by the agent doesn't stand in for the
  batched question set the user was supposed to answer
- A build step that ran (or didn't) gets a printed verdict, not silence

## What's included

### The skill: web-design
Single entry point. Ask for any web design task and it runs before code:
tooling check, confidence check, scope spec, taste/Advisor Mode direction,
wireframe or variant sequence, component discovery, pre-build manifest,
build, and a post-build confirmation.

### 11 Required Sub-Skills

| Sub-skill | Purpose |
|-----------|---------|
| `huashu-design` | Advisor Mode, Brand Asset Protocol, variant comparisons, deck/motion output |
| `frontend-design` | Aesthetic direction within token constraints |
| `spline-3d-integration` | Embeds a pre-authored Spline scene, does not generate one |
| `svg-animations` | Animated SVG: path, stroke, morph |
| `gsap-core` / `gsap-timeline` / `gsap-plugins` / `gsap-react` | Motion and sequenced animation |
| `excalidraw-diagram` | Visual wireframes |
| `brainstorming` | Layout exploration, design tradeoffs |
| `higgsfield-generate` | Hero image on a full-aesthetic build, needs its own credentials |

Full trigger conditions and verified install commands are in `SKILL.md`'s
Required Sub-Skills table.

### Named anti-patterns

Each one below was observed in a live Codex run against this skill, not
imagined in advance:
- Inventing a design direction, then asking one closing yes/no question
  instead of the real batched multi-axis Confidence Check
- Treating a variant pick ("A", "the second one") as Pre-Build Manifest
  sign-off
- `N/A` for the Tweaks Bar on a "static" page that has a real interactive
  feature
- Writing the Post-Build Confirmation into the ledger file but never into
  chat
- Defaulting to Three.js for a 3D model without asking whether a Spline
  scene already exists to embed

Each is named and banned in `SKILL.md` with the run that produced it.

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
  The skill's own Step -1 installs whichever is missing the first time it
  runs. Neither has a valid N/A path.
- An agent/harness that supports the [skills CLI](https://skills.sh/)
  convention. Verified against Codex CLI, should work anywhere the `skills`
  CLI installs to.

## Keeping test/build output out of git

This repo's own `.gitignore` excludes `.codex/`, `.grok/`, and any
`codex-test-run*` directory left behind by live testing. Apply the same
pattern in a project that adopts this skill, so a design tool's working
files never land in your commits.

## Supported tools

Anything the [skills CLI](https://skills.sh/) targets: Claude Code, Codex
CLI, Cursor, and the other agents it lists with `npx skills add -a '*'`.
Only Codex CLI has been exercised against a live run as of this writing.

## Contributing

Open an issue or PR at
[github.com/Synx-x/web-design-skill](https://github.com/Synx-x/web-design-skill).
Every fix in `SKILL.md` cites the session log that produced it. A PR that
adds a new anti-pattern should do the same.

## What's in the repo

- `SKILL.md`: the full method
- `references/`: taste-library and Wide-Net Variant Sequence detail, the
  DESIGN.md schema, WCAG contrast checks, component-source registries, the
  design-quality tooling command reference, and a micro-details checklist
- `TEST-PLAN.md`: the manual test matrix this skill was verified against
