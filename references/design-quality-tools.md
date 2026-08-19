# Design-Quality Tools: Impeccable + Taste Skill

**Hard dependency.** Method Step -1 checks both are installed before any other step runs,
and self-installs whichever is missing. Neither is optional and there's no "not installed"
skip path on the Pre-Build Manifest.

**Detection**: `npx impeccable detect` and similar runtime commands work standalone via
npx even without `install` ever having run (confirmed against
[github.com/pbakaus/impeccable](https://github.com/pbakaus/impeccable), 2026-08-04: its
`detect` is described as "a standalone CLI for detecting anti-patterns without an AI
harness"). A successful `detect` call is therefore not evidence Impeccable is installed.
Check its real install markers instead: `.impeccable/config.json`,
`.impeccable/design.json`, `.impeccable/live/config.json`, or a `.claude/skills/impeccable`
/ `.cursor/skills/impeccable` directory. Taste Skill has no documented marker path (its
own repo doesn't specify one, checked 2026-08-04); since it installs as a plain `SKILL.md`
the harness auto-triggers by description, check for a matching skill directory under
`.claude/skills/` or `.cursor/skills/` by name instead.

External, npx-installed tools that operate ON generated UI to strip AI slop and push
aesthetic quality. Not component registries (see `component-sources.md` for those).
These transform and critique code that already exists.

Source: [Turn Claude Into A Design GENIUS In 3 Simple Steps](https://www.youtube.com/watch?v=7FU98O0JLHs)
(Chase AI, 2026-07-23), cross-verified against [github.com/pbakaus/impeccable](https://github.com/pbakaus/impeccable)
and [github.com/Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill) on 2026-08-04.

## Impeccable

Site: [impeccable.style](https://impeccable.style/) · Repo: [pbakaus/impeccable](https://github.com/pbakaus/impeccable)

Install once per project:

```bash
npx impeccable install   # detects harness folders (.claude/, .cursor/, .github/), installs skill
/impeccable init         # one-time setup: gather design context
```

Update: `npx impeccable update`.

### Deterministic detector (run before/instead of manual anti-slop review)

```bash
npx impeccable detect src/
npx impeccable detect --json .
```

Catches 59 deterministic issues across AI-slop tells (side-tab borders, purple gradients,
bounce easing, dark glows) and general quality (line length, cramped padding, small touch
targets, skipped headings). Run this as part of Step 2 (Anti-slop check) and Step 7
(Validate). It's a strict superset of the manual pattern list already in SKILL.md.

### All 23 commands (invoke via `/impeccable <command>`)

| Command | Does |
|---|---|
| `craft` | Full shape-then-build flow with visual iteration |
| `init` | One-time setup: gather design context |
| `document` | Generate root DESIGN.md from existing code |
| `extract` | Pull reusable components and tokens |
| `shape` | Plan UX/UI before writing code |
| `critique` | UX design review |
| `audit` | Technical quality checks (a11y, performance, responsive) |
| `polish` | Final pass and shipping readiness |
| `bolder` | Amplify understated designs |
| `quieter` | Tone down overly bold designs |
| `distill` | Strip to essence |
| `harden` | Error handling, i18n, edge cases |
| `onboard` | First-run flows and empty states |
| `animate` | Add purposeful motion |
| `colorize` | Introduce strategic color |
| `typeset` | Fix font choices and hierarchy |
| `layout` | Fix spacing and visual rhythm |
| `delight` | Add moments of joy |
| `overdrive` | Add technically extraordinary effects |
| `clarify` | Improve unclear UX copy |
| `adapt` | Adapt for different devices |
| `optimize` | Performance improvements |
| `live` | Visual variant mode: iterate on elements in the browser (dev-server live editing) |

`live` is the closest built-in equivalent to the Tweaks Bar pattern (see
`taste-library-and-variants.md`). Try it before hand-building a custom tweaks panel.

## Taste Skill

Site: [tasteskill.dev](https://www.tasteskill.dev/) · Repo: [Leonxlnx/taste-skill](https://github.com/leonxlnx/taste-skill)

Install:

```bash
npx skills add https://github.com/Leonxlnx/taste-skill
# single-skill variant:
npx skills add https://github.com/Leonxlnx/taste-skill --skill "design-taste-frontend"
```

No slash command — it installs as a `SKILL.md` the harness auto-triggers by description,
same mechanism as this skill. v2 (current default, marked experimental by upstream) adds:

- Brief inference + design-system mapping
- Three tunable dials: `VARIANCE` / `MOTION` / `DENSITY`
- Hard em-dash ban + canonical GSAP code skeletons
- Redesign-audit protocol
- Strict pre-flight check

Framework-agnostic (React, Vue, Svelte); works across Claude Code, Cursor, Codex.

## When to use which

Both fight the same problem (generic AI output) from different angles, and are not
mutually exclusive. Don't gate one behind the other:

- **Impeccable**: heavier, more prescriptive, 23 discrete transform commands + a
  deterministic 59-rule detector. Best for: systematic pass over an existing build
  (`audit`, `polish`), or a named targeted transform (`bolder`, `overdrive`, `clarify`).
- **Taste Skill**: lighter, dial-based (VARIANCE/MOTION/DENSITY), auto-triggered like any
  other installed skill. Best for: ambient taste pressure during normal generation, no
  explicit invocation needed once installed.

Both installed by Step -1, always. Use Taste Skill passively for every generation pass;
run `impeccable detect` explicitly at the Anti-slop and Validate gates; reach for named
Impeccable commands (`bolder`, `overdrive`, etc.) when a specific named problem shows up.

## Neither is a substitute for

- **Taste** itself. Both operate on structure and execution quality, not on picking an
  aesthetic direction. That's what the taste library (`taste-library-and-variants.md`)
  and `huashu-design` Advisor Mode are for.
- **Token discipline.** DESIGN.md remains the single source for color/type/spacing
  values. These tools flag violations; they don't replace the token pipeline.
