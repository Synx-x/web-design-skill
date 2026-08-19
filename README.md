# web-design-skill

A portable agent skill for web design work: taste-library-driven direction
finding, a token-first/state-first build pipeline, a mandatory pre-build
manifest, and a written scope spec before any UI code gets written.

No external plugin or hook required. Every gate in `SKILL.md` runs from the
skill's own text and your agent's own discipline in following it.

## Install

```bash
npx skills add https://github.com/Synx-x/web-design-skill -g -y
```

Drop `-g` to install it into the current project instead of globally.

## What's in here

- `SKILL.md` — the full method: tooling check, confidence check, scope-spec
  gate, taste-library + Advisor Mode direction finding, wireframing, build,
  and validation.
- `references/` — supporting detail the main skill file points to: the
  taste-library and Wide-Net Variant Sequence, the DESIGN.md schema, WCAG
  contrast checks, component-source registries, design-quality tooling
  command reference, and a micro-details polish checklist.

## Requires

- [Impeccable](https://github.com/impeccable-app/impeccable) and the
  [Taste Skill](https://github.com/Leonxlnx/taste-skill) (both npx-installed,
  the skill's own Step -1 installs them for you the first time it runs).
- An agent/harness that supports the [skills CLI](https://skills.sh/)
  convention (Claude Code, Codex, Cursor, and others).
