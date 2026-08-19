---
name: web-design
description: Use when starting any web design task (new page, component, or design system), especially before generating UI code, adding color or spacing values, or deciding whether to hand-roll a component.
user_invocable: true
---

# Web Design

Self-contained. Portable: every gate in this skill runs from the skill's own
text and the user's own answers, with no external hook or plugin required.
If your harness supports PreToolUse-style hooks, you can additionally build
a machine-level enforcement layer on top (see "Optional: machine-level
enforcement" near the end), but nothing here depends on one existing.

The scope-spec gate below is adapted from [mattpocock/skills](https://github.com/mattpocock/skills)'
`skills/productivity/grilling/SKILL.md` and `skills/productivity/grill-me/SKILL.md`,
by way of a `scope-grill` skill that wrapped that interview loop with a
mandatory written spec. This version folds that spec requirement into the
Confidence Check below rather than running a second, separate interview.
This skill's Confidence Check already batches every open question into one
`AskUserQuestion` call. Its own Failure Modes table rejects one-at-a-time
questioning. So the spec-gate's job here is to make sure those resolved
answers land on disk, not just in chat.

## When to Use

Any web design task: new page, component, or design system. Run this skill **before** writing any UI code.

Do not skip to implementation. This skill runs first, every time.

## Required Sub-Skills (hard dependency, all of them)

This skill orchestrates the following. Every row is a hard dependency, installed by Method Step -1 before any other step runs. Invoke each one when its trigger fires. Skipping a sub-skill without an explicit recorded N/A reason in the Pre-Build Manifest is a failure mode. "Not needed" alone is rejected. State the specific reason this task does not trigger that skill.

"Not installed" is never a valid N/A reason. Step -1 installs whatever is missing, which makes that reason impossible by construction. Every command in the Install column was verified working on 2026-08-19.

| Sub-skill | Trigger condition | Install |
|-----------|-------------------|---------|
| `huashu-design` (Advisor Mode), plus a taste library filesystem check alongside it | Always, for new page/full-site/full-aesthetic-direction tasks, no exceptions. For single-component or established-project tasks: ask the user in the Confidence Check batch rather than silently skipping. See `references/taste-library-and-variants.md` | `npx skills add alchaincyf/huashu-design -g -y` |
| `huashu-design` (Brand Asset Protocol) | Specific company, product, or existing brand named | same package as above |
| `huashu-design` (design_canvas.jsx) | Comparison view for Step 3's variant set (structural wireframes or Wide-Net variants), any new full page or multi-region layout | same package as above |
| `huashu-design` (deck/animations/frames) | Presentation, pitch deck, motion export (MP4/GIF), device mockup | same package as above |
| `shadcn/ui` registry | Any UI component before hand-rolling | CLI, no skill install. See `references/component-sources.md` |
| `21st.dev` registry | UI component not covered by shadcn | CLI, no skill install |
| `ElevenLabs UI` registry | Voice/agent state-machine components | CLI, no skill install |
| `spline-3d-integration` | Hero section, product showcase, or any 3D scene | `npx skills add sickn33/agentic-awesome-skills -g -y -s spline-3d-integration` |
| `svg-animations` | Any animated SVG (path, stroke, morph) | `npx skills add supermemoryai/skills -g -y -s svg-animations` |
| `gsap-core` / `gsap-timeline` / `gsap-plugins` / `gsap-react` | Motion or sequenced animation | `npx skills add greensock/gsap-skills -g -y -s gsap-core -s gsap-timeline -s gsap-plugins -s gsap-react` |
| `frontend-design` | Aesthetic direction within token constraints | `npx skills add anthropics/skills -g -y -s frontend-design` |
| `excalidraw-diagram` | Visual wireframes | `npx skills add coleam00/excalidraw-diagram-skill -g -y` |
| `brainstorming` | Layout exploration, design tradeoffs | `npx skills add obra/superpowers -g -y -s brainstorming` |
| `higgsfield-generate`, or any image-generation tool you already have | Hero image on a full-aesthetic build (Method Step 6b) | `npx skills add higgsfield-ai/skills -g -y -s higgsfield-generate` |

One exception to the install rule, and only one. `higgsfield-generate` drives a paid third-party service through its own `higgsfield` CLI. A working install still needs credentials this skill cannot provide. Substitute whatever image tool you already have and name it on the manifest. Every other row installs free and offline-clean, so no other row gets this latitude.

Two syntax notes, both learned by testing the CLI rather than reading its help text. Repeated `-s` flags select multiple skills from one repo. A single comma-separated `-s` value fails with "No matching skills found". The `-g` flag installs to `~/.agents/skills/<name>` and links into each supported agent's own directory.

Substituting an equivalent skill you already have is fine. Record the substitution on the manifest by name. An empty slot is not fine.

## External Design-Quality Tools (hard dependency)

Not agent skills, npx-installed tools that transform/critique generated UI. **Both
are required for this skill to proceed, not optional.** Method Step -1 checks for both
and self-installs whichever is missing before any other step runs. There is no N/A path
for "not installed" on the Pre-Build Manifest. That reason is invalid. Install them
instead. Full command reference: `references/design-quality-tools.md`.

| Tool | Use for |
|------|---------|
| Impeccable (`npx impeccable install`) | 23 named transform commands (`bolder`, `overdrive`, `clarify`, etc.), plus `impeccable detect` (59-rule deterministic slop/quality scan) and `impeccable live` (browser-based visual variant mode) |
| Taste Skill (`npx skills add https://github.com/Leonxlnx/taste-skill -g -y`) | Passive taste pressure during generation. VARIANCE/MOTION/DENSITY dials, auto-triggers like an installed skill, no explicit invocation needed. Install globally (`-g`), not per-project. One install then covers every project |

Not mutually exclusive with each other or with the manual anti-slop check in Step 2.

## Execution Contract

This skill produces UI code only after every gate below clears. Skipping any gate fails the task.

**Required output sequence, in this order:**

Item numbers below are execution order, not Method step numbers. Each item names the
Method step it runs, in parens, so the two never drift out of sync.

1. **Acknowledge skill loaded.** State: "running web-design skill against [task]". Name the build target candidate (Production UI, Static single-file demo, or Presentation/motion).
2. **Run Method Step -1 (Dependency check, hard dependency).** Two classes, both mandatory. Verify Impeccable and Taste Skill are installed. Then run Step -1's filesystem loop across every Required Sub-Skill name. Install everything missing in either class now, before any other step. No skip path, and no deferring an install until the build reaches the step that needs it.
3. **Confidence check (mandatory).** Before locking direction, evaluate confidence on each of: `build target`, `brand identity`, `aesthetic direction`, `scope and sections`, `target audience`, `tech stack constraints`. Two conditions add an extra question here: the task is single-component, or it targets an established project (DESIGN.md + taste library already set). Both conditions also require that unconditional Advisor Mode does not already trigger (see next item). When both hold, add one more question to this same batch. Phrase it as: "Run a fresh Advisor Mode pass (3 new directions) for this task, or keep the existing brand direction"? If confidence on any item is below ~90%, batch all unknowns into a single `AskUserQuestion` call (1–4 questions max, multi-question form). Do this alongside the Advisor Mode question if it applies. Each question presents 2–4 concrete options with descriptions. Wait for answers before any further step. Do not ask questions sequentially when they can be batched. Do not proceed on assumed defaults.
4. **Write the scope spec (mandatory, every task, no size exception).** Every branch resolved in item 3 above, plus every Required Sub-Skill's trigger decision, gets written to a spec file before Advisor Mode or Orient runs. See Method Step -0.25 below. This is what makes "shared understanding" a file on disk instead of something that only existed in the chat transcript.
5. **Run Method Step 0 (Taste library check + Advisor Mode).** For new pages/full-site/full-aesthetic-direction tasks: run `huashu-design` Advisor Mode unconditionally, regardless of whether a taste library exists or the ask already sounds specific. Its 3 directions become extra candidates for Step 3's Wide-Net Variant Sequence, additive with any taste-library families found (not a replacement for them). For single-component/established-project tasks: honor whatever the user answered in item 3's Advisor Mode question.
6. **Run Method Step 1 (Orient).** Produce DESIGN.md (or confirm existing). Compile tokens.
7. **Run Method Step 2 (Anti-slop).** Produce explicit pass/fail with named patterns checked, plus `impeccable detect` output (Impeccable is a hard dependency as of item 2, always installed by this point).
8. **Run Method Step 3 (Wireframe/Variants).** For new full pages or multi-region layouts: run the Wide-Net Variant Sequence. Build one full variant per direction sourced in Step 0 (taste-library families plus Advisor Mode's picks, or user-named directions when no library exists). Narrow to 3 refinement iterations of the chosen one, then 1. Not a single structural wireframe. Skip only for single components, where a plain 3-variation structural wireframe still applies.
9. **Run Method Step 4 (Discover Components).** Produce registry findings (shadcn/21st.dev/ElevenLabs/Spline) per component need.
10. **Run Method Step 5 (Pre-Build Manifest).** Output the manifest, including the scope-spec row from item 4. **Stop.** Wait for explicit user sign-off in chat. Do not infer approval. Do not proceed on silence.
11. **Only after sign-off:** Run Method Step 6 (Build), 6b (Hero image, full-aesthetic builds), and 6c (Tweaks Bar, mandatory for every build, no exceptions).
12. **Run Method Step 7 (Validate)** before declaring complete, including `impeccable detect`.
13. **Run Method Step 8 (Post-Build Confirmation, mandatory gate).** Print the fixed template for Hero image, Tweaks Bar, Validate, and the ledger file. Do this before any "done" or "here's your page" message, not instead of one.
14. **Confirm the ledger (mandatory, gated).** Every roster entry from the Pre-Build Manifest gets one line with an explicit verdict, unchanged since sign-off. See "Design ledger" below.

### Design ledger

The Pre-Build Manifest produced in Method Step 5 **is** the design ledger. There
is no separate file format or external tool required to enforce it. The gate is
that this skill refuses to write UI code before that manifest has been produced
in chat and the user has explicitly signed off on it.

Also write the manifest's content to a persistent file so it survives the
chat: `docs/design/design-ledger.md` (or `DESIGN.md`, or
`.claude/design-ledger.md`, whichever your project already uses). Append
Step 8's Post-Build Confirmation to that same file once the build finishes.
The ledger is not done at sign-off. It closes when Step 8 does. One line
per entry:

```
- <id>: <VERDICT>. <what it produced, or why it does not apply>
```

Verdicts are `INVOKED`, `CONSULTED`, `PENDING`, `N/A`, `BLOCKED`. `N/A` is always
acceptable and always needs a reason. "N/A, the app has no hero or marketing surface,
its first screen is a task list" is a decision. An omitted line is not, and the two are
indistinguishable in a transcript a week later.

There is no verdict for a dependency that is absent. Step -1 installs every one of them
before this manifest exists, so "absent" is not a state the manifest can reach. If you
find yourself wanting to write one, Step -1 did not complete. Go back and finish it.

Use `BLOCKED` only when an install genuinely failed after a real attempt. Record the
command you ran and the error it returned, then stop and report to the user. Never
downgrade a failed install to `N/A` and continue building. This rule closes an observed
hole: a live Codex run of this skill on 2026-08-19 wrote `frontend-design | NOT FOUND`
into its own scope spec and kept building. See the source doc at
https://github.com/Synx-x/web-design-skill for the current gate text.

**Refusal contract:** If asked to write UI code without these outputs in sequence, refuse. Explain that the skill is active and the gates have not cleared. Restart from Step 1.

**Acceptable bypass paths (none of these cancel the manifest):**
- "Just a quick fix to existing component": run Step 4 + manifest only, with N/A reasons for the rest.
- "User says skip the manifest": refuse politely. The manifest is the user's review surface, not yours to skip on their behalf.

## Method

### -1. Tooling check (hard dependency, before everything else)

Two classes of dependency, one gate. Both classes are mandatory. Neither has a skip path.

**Class 1, external npx tools.** Check for each tool's installed-state marker on disk. Runtime commands like `npx impeccable detect` work standalone via npx even without `install` ever having run. A successful `detect` call is therefore not evidence the skill is installed. Check the marker files instead:

```bash
ls .impeccable/config.json .claude/skills/impeccable 2>/dev/null   # Impeccable installed if either exists
find .claude/skills .cursor/skills ~/.claude/skills ~/.cursor/skills ~/.agents/skills -maxdepth 1 -iname "*taste-skill*" -o -iname "*taste*" 2>/dev/null   # Taste Skill installed if a match exists, project or global
```

Install whichever check found nothing:

```bash
npx impeccable install                                            # Impeccable markers absent
npx skills add https://github.com/Leonxlnx/taste-skill -g -y      # Taste Skill markers absent. -g: global, not per-project
```

**Class 2, the Required Sub-Skills.** Resolve every name in the Required Sub-Skills table against disk. Run this check verbatim:

```bash
for s in huashu-design frontend-design excalidraw-diagram spline-3d-integration \
         svg-animations gsap-core gsap-timeline gsap-plugins gsap-react brainstorming \
         higgsfield-generate; do
  found=""
  for base in ./.claude/skills ./.cursor/skills ~/.claude/skills ~/.codex/skills ~/.agents/skills; do
    [ -e "$base/$s" ] && found=1
  done
  [ -n "$found" ] && echo "OK      $s" || echo "MISSING $s"
done
```

Install every name the loop printed as MISSING, using that row's command from the Required Sub-Skills table. Then rerun the loop. Proceed only when it prints zero MISSING lines.

**No skip path, either class.** "Not installed" is not a valid Pre-Build Manifest reason for anything in either class. This step exists precisely to make that reason impossible. A `MISSING` line that survives into the manifest is a hard stop, not a recorded N/A. Full command reference in `references/design-quality-tools.md`.

One caveat on agent linkage. The `npx skills add` output can name an agent directory it did not actually write. Trust the filesystem loop above over the installer's own summary. Verified against Codex on 2026-08-19: the installer printed "copy → Codex" while `~/.codex/skills/` stayed empty. A manual symlink from `~/.agents/skills/<name>` fixed it.


### -0.5. Confidence Check (mandatory, see Execution Contract item 3)

Runs between Step -1 and Step -0.25, always. Evaluate confidence on `build target`, `brand
identity`, `aesthetic direction`, `scope and sections`, `target audience`, `tech stack
constraints`. Below ~90% on any axis triggers a batched `AskUserQuestion`. For
single-component/established-project tasks that wouldn't otherwise trigger Step 0's
unconditional Advisor Mode, add the Advisor-Mode opt-in question to this same batch (don't
ask it separately, and don't skip asking it). Full mechanics live in the Execution
Contract, this header exists only so Step -1 and Step 0 have something concrete to point
to in sequence.

### -0.25. Write the scope spec (mandatory, every task, see Execution Contract item 4)

Immediately after the Confidence Check resolves every axis (by user answer or by
looked-up fact), write a spec file before Advisor Mode or Orient runs. Path:
`docs/specs/<task-slug>-spec.md` (fall back to `.claude/specs/<task-slug>-spec.md`
if the project has no `docs/` tree). This is what turns "we discussed it in
chat" into a file the next session, or a different agent picking up the same
task, can read without re-deriving the discussion.

The spec has three required sections:

**1. Feature inventory** — every Confidence Check axis and every Required
Sub-Skill trigger decision, its resolution, and whether that resolution came
from the user or was looked up as a fact:

```markdown
| Branch | Resolution | Source |
|---|---|---|
| Build target | Production UI (React + Tailwind v4 + shadcn) | user |
| Brand identity | Existing brand, tokens in DESIGN.md | looked up |
| huashu-design Advisor Mode | Unconditional, full-page task | looked up (task class) |
| spline-3d-integration | N/A, no hero section on this task | user |
```

**2. User-journey matrix** — rows are journeys/personas that touch this
feature. Columns are, at minimum, happy path / edge cases / destructive
actions:

```markdown
| Journey | Happy path | Edge cases | Destructive actions |
|---|---|---|---|
| First-time signup | ... | empty email, duplicate account | account deletion mid-signup |
```

An empty cell is not "not applicable" by default. It means this pass didn't
reach it. Either fill it in or move it to Non-goals explicitly.

**3. Non-goals** — everything explicitly ruled out, so a later pass doesn't
relitigate it or silently add it back:

```markdown
- Not building the admin-panel variant of this page in this pass
- Not covering the RTL layout case yet (tracked separately)
```

No skip path for the spec file itself. Individual rows can be resolved as
N/A with a reason. The spec's existence cannot be. If a branch can't be
resolved after asking twice, record it as an open risk in Non-goals rather
than guessing.

### 0. Taste library check + Advisor Mode (new pages/full-site/full-aesthetic-direction tasks)

Before Orient, check for an existing taste library (a curated, tagged folder of design
inspiration built from Dribbble/Pinterest/X, see `references/taste-library-and-variants.md`).

**Advisor Mode runs unconditionally for this task class**, regardless of whether a taste
library exists or the ask already sounds specific. Invoke `huashu-design` Advisor Mode now.
Its 3 philosophically distinct directions are additive with any taste-library families
found, not a replacement for them, both feed Step 3's Wide-Net Variant Sequence together.
"The user already gave a specific direction" is not a reason to skip this: a specific ask
still benefits from seeing distinct alternatives before committing.

**If a taste library was found:** its families plus Advisor Mode's 3 directions together
become the seed set for Step 3.

**If no taste library:** Advisor Mode's 3 directions are the seed set alone. Optionally
suggest starting a taste library for future tasks, don't block on building one now.

For single-component or established-project tasks (not this task class): whether Advisor
Mode runs was already settled by the Confidence Check's opt-in question (Execution
Contract item 3). Honor that answer here rather than deciding again.

### 1. Orient: DESIGN.md (mandatory)

Check project root for `DESIGN.md`.

**If it exists:** Parse YAML frontmatter. Extract colors, typography, spacing, component tokens. All downstream code uses these tokens only. No exceptions.

**If missing:** Step 0 already resolved the direction. It is either the user's chosen pick among Advisor Mode's 3 (unconditional there for full-aesthetic-direction tasks) plus any taste-library families. Or it is the Confidence Check opt-in's outcome for smaller tasks. Generate a draft using the schema in `references/design-md-schema.md`, built from that chosen direction. Show the draft to the user. **Wait for confirmation before continuing.** Do not auto-proceed, and do not guess a direction that skips Step 0.

**If brand/product work is detected** (specific company, product, or existing brand): run Huashu's Brand Asset Protocol before generating DESIGN.md tokens. Search for logos, color values, fonts, and screenshots. Document found assets. Token values must derive from real brand assets, not invented.

After tokens are confirmed, compile DESIGN.md tokens into Tailwind v4 CSS-first config. The shadcn/ui pattern (verified at https://ui.shadcn.com/docs/tailwind-v4):

```css
@import "tailwindcss";

:root {
  --background: hsl(0 0% 100%);
  --foreground: hsl(0 0% 3.9%);
  --primary: hsl(222 47% 11%);
  /* ...all semantic tokens, light mode */
}

.dark {
  --background: hsl(0 0% 3.9%);
  --foreground: hsl(0 0% 98%);
  --primary: hsl(0 0% 98%);
  /* ...all semantic tokens, dark mode */
}

@theme inline {
  --color-background: var(--background);
  --color-foreground: var(--foreground);
  --color-primary: var(--primary);
  /* ...maps semantic tokens to Tailwind utility namespace */
}
```

Light/dark values live in `:root` and `.dark`. The `@theme inline` block maps them into Tailwind's `--color-*` namespace so utility classes like `bg-background` and `text-foreground` work, and CSS in components can read `var(--background)` directly.

DESIGN.md is the single source. Never set token values in two places. Tokens in `:root`/`.dark` are the only place hex/HSL appears.

Verify WCAG AA contrast on the token pairs: text on background ≥ 4.5:1, UI elements ≥ 3:1. Run `design.md lint` if available. See `references/wcag-checks.md` for manual check steps.

### 2. Anti-slop check (mandatory before any visual work)

Reject these patterns before writing a single line of UI code. They are training-data clichés that undermine brand recognition:

- Purple/blue gradients as default palette
- Emoji used as icons
- Rounded cards with a colored left-border accent
- SVG-drawn imagery substituting real assets
- Excessive decorative elements with no content purpose

If any appear in a design direction or generated output: stop, remove them, replace with brand-derived or neutral alternatives.

Also run `npx impeccable detect .` (or scope to `src/`) and fold its findings into the pass/fail. Impeccable is a hard dependency (Step -1), always present by this point. It catches 59 deterministic issues, a strict superset of the manual list above. See `references/design-quality-tools.md`.

### 3. Wireframe / Wide-Net Variants (skip for single components)

Use when building a full page or multi-region layout.

**Full aesthetic build (default when the task is a new page's overall direction):** run the Wide-Net Variant Sequence in `references/taste-library-and-variants.md`. Build one full variant per direction sourced in Step 0. Use taste-library families plus Advisor Mode's picks together when a library exists, or Advisor Mode's picks alone when it doesn't. Typically 3-5 depending on how many directions Step 0 produced. Pick one, then 3 refinement iterations of it, then pick one final. This replaces plain structural wireframing for this case. It compares complete visual directions, not bare layout skeletons.

**Structural wireframe (single components, or no aesthetic ambiguity):** default to **3 variations** before committing to one direction. Use a brainstorming/exploration skill to explore layout options. Reference `excalidraw-diagram` for visual wireframes. Brand palette comes from DESIGN.md tokens. No free-form color choices.

**Comparison layout, either branch:** use `huashu-design`'s `design_canvas.jsx` to show whichever set you're comparing side by side, the 3 structural wireframes or the Wide-Net Variant Sequence's variants. It's a side-by-side tool for both branches, not a branch of its own.

Get early validation on a skeleton/placeholder version before full implementation. Show assumptions and structural layout first. Fill detail after user confirms direction.

### 4. Discover Components (mandatory before hand-rolling)

Never hand-roll a component before checking registries. Check in this order:

1. **shadcn/ui** (`https://ui.shadcn.com/docs/components`): composable base components, CSS var native
2. **21st.dev** (`https://21st.dev/home`): AI/agent-focused React + shadcn components
3. **ElevenLabs UI** (`https://ui.elevenlabs.io/docs`): voice/agent state-machine components
4. **Spline** (`https://spline.design`): interactive 3D scenes for hero sections and product showcases. Use the `spline-3d-integration` skill for embedding. Do not hand-roll WebGL or Three.js when a Spline scene covers the need.

Rule: if a registry component covers ≥80% of the need, use and customize. Hand-roll only below 80% coverage.

Full registry details in `references/component-sources.md`.

### 5. Pre-Build Manifest (mandatory gate)

Before writing any UI code, output the following manifest. Each row is INVOKED with a one-line summary of what was used, or N/A with a specific reason. "Not needed" alone is rejected.

A prose paragraph summarizing your decisions does not satisfy this gate, no matter how
complete it reads. Every line below must appear verbatim, with its id and a verdict. A
live Codex run of this skill on 2026-08-19 printed a casual bullet summary instead of
this template, and skipped two rows in the process without anyone noticing until the
transcript was read back. See https://github.com/Synx-x/web-design-skill for the run.

Two rows below only cover work this skill can do before the build exists. Hero image and
Tweaks Bar happen after Step 6, so they cannot get a truthful verdict here. Step 8 covers
them. Do not write a verdict for either row in this manifest. Do not skip Step 8 because
this manifest felt complete without it.

```
PRE-BUILD MANIFEST for: <task description>

- Scope spec (feature inventory + journey matrix + non-goals): <path to spec file>, no N/A path
- Impeccable / Taste Skill (Step -1, hard dependency): INVOKED <commands run, e.g. install (first-time) + detect + bolder>, no N/A path
- Taste library check: FOUND <library path, families used> | NOT FOUND <no library, Advisor Mode/explicit directions cover it>
- huashu-design (Advisor Mode): INVOKED <3 directions, additive with taste library above>, unconditional for full-aesthetic-direction tasks, no N/A path | For single-component/established tasks: ASKED in Confidence Check, user said <run it | keep existing direction>
- Wide-Net Variant Sequence (N -> 1 -> 3 -> 1): INVOKED <N, source of directions, chosen direction + iteration> | N/A <reason, e.g. single component>
- Structural wireframe (3 variations, Step 3 single-component branch): INVOKED <3 options, chosen> | N/A <reason, e.g. full-page task used Wide-Net instead>
- huashu-design (Brand Asset Protocol): INVOKED <assets found> | N/A <reason>
- huashu-design (design_canvas.jsx): INVOKED <variant/wireframe set shown> | N/A <reason>
- huashu-design (deck/animations/frames): INVOKED <components> | N/A <reason>
- shadcn/ui: INVOKED <components> | N/A <reason>
- 21st.dev: INVOKED <components> | N/A <reason>
- ElevenLabs UI: INVOKED <components> | N/A <reason>
- spline-3d-integration: INVOKED <scene> | N/A <reason>
- svg-animations + gsap-*: INVOKED <animations> | N/A <reason>
- frontend-design: INVOKED <direction summary> | N/A <reason>
- excalidraw-diagram: INVOKED <wireframe> | N/A <reason>
- Brainstorming/exploration pass: INVOKED <decisions> | N/A <reason>
- Hero image and Tweaks Bar: see Step 8, printed after the build, no verdict here

DESIGN.md: <path> (created/exists/updated)
Token location: <path>/styles/globals.css (:root + .dark + @theme inline)
Build target: <Production UI (Tailwind v4 + shadcn) | Static single-file demo | Presentation/motion>
Anti-slop check: PASSED (no purple gradient, emoji icons, left-border cards, SVG-as-imagery, decoration-without-purpose)
```

**Hard stop.** Output the manifest to chat. Do not write UI code. Do not generate filenames. Do not draft markup. Wait for explicit user sign-off (e.g. "go", "approved", "proceed"). Silence is not approval. A vague "sounds good" is not approval. Reject N/A rows that lack a specific reason and require the user to elaborate before continuing.

If you find yourself writing UI code and have not produced this manifest, stop immediately. Discard the in-progress code. Restart from Step 1.


### 6. Build: Token-First, State-First

Route to the right build target before writing any code:

**Presentations, pitch decks, motion exports (MP4/GIF):** invoke `huashu-design`. Use its starter components: `deck_index.html` + `deck_stage.js` for slide foundation, `animations.jsx` for motion, `ios_frame.jsx` / `android_frame.jsx` for device mockups. Do not build these in React + shadcn. Wrong runtime for export.

**Static / single-file HTML demo (landing page, prototype, embedded preview):** native CSS only, no build step. Token discipline still applies. Required:

- DESIGN.md exists. Tokens defined once in `:root` and `.dark` inside the `<style>` block, using HSL with `hsl()` wrapper at usage. No raw hex in markup.
- Native `@layer` for cascade order: `@layer reset, base, components, utilities;`
- Component shapes derive from shadcn/ui patterns translated to vanilla HTML+CSS (button variants, card, nav, etc.)
- Anti-slop check passed
- Pre-Build Manifest signed off

Use this branch when the output is one HTML file. No Tailwind build step. Token names mirror shadcn convention (`--background`, `--foreground`, `--primary`, etc.) so future migration to React + Tailwind is trivial.

**Interactive web app / production UI:** Tailwind v4 + shadcn/ui + React stack.

Setup verified at https://ui.shadcn.com/docs/tailwind-v4 and https://tailwindcss.com/docs/theme:

- `@import "tailwindcss";` once at the top of the global CSS
- HSL tokens in `:root` and `.dark`. Light/dark switch via `.dark` class on `<html>` or `<body>`
- `@theme inline { ... }` block maps `--background` → `--color-background` so utility classes work (`bg-background`, `text-foreground`)
- Components use shadcn's named-function pattern (no `React.forwardRef`) and `data-slot` attributes for styling hooks
- `size-*` utility instead of paired `w-* h-*`
- `cn()` helper from `lib/utils.ts` for conditional classes
- `cva` (Class Variance Authority) for component variants

Cascade control comes from Tailwind's own layers (`base`, `components`, `utilities`). Custom CSS goes in `@layer components` blocks inside the global stylesheet:

```css
@layer components {
  .my-custom-block {
    background: var(--background);
    color: var(--foreground);
  }
}
```

App-level overrides stay unlayered, automatically winning specificity.

**JS/HTML structure (mandatory for production UI):**

```
src/
  config/          # env vars, constants, feature flags. No deps.
  types/           # TS interfaces, domain types, enums. config only.
  tokens/          # design tokens. JS twin of :root/.dark CSS variables
  schemas/         # validation schemas (Zod/Yup). config + types only.

  lib/             # third-party wrappers (auth client, API client, analytics)
  services/        # data access, external integrations. lib + types only.

  features/        # feature-sliced domains
    auth/
      components/
      hooks/
      services/
      types/
    billing/       # each feature mirrors the same internal structure
      ...

  components/      # shared reusable UI — tokens + types, no feature imports
  layouts/         # page shells — components only

  pages/           # route-level composition — features + layouts
  app/             # entry point, providers, router — wires everything
```

Dependency rule (enforced via ESLint `import/no-restricted-paths` or barrel files):

- `config/`, `types/`, `tokens/`, `schemas/` import nothing
- `lib/` imports config + types only
- `services/` imports lib + types + schemas
- `features/[name]/` imports services, shared components, hooks, tokens
- No feature imports another feature directly. Cross-feature communication goes through services or shared state only.
- `components/` never imports from `features/`
- `pages/` imports features + layouts
- `app/` imports all

`tokens/` holds the same values as the global stylesheet's `:root`/`.dark` blocks in JS format (e.g., exported constants or a theme object). Both derive from DESIGN.md. Keep them in sync. A token change in DESIGN.md updates both files.

**Before markup:** define all component states upfront. Minimum: `idle | loading | error | success`. Add states the component demands (timeout, rateLimit, empty, etc.). Build UI around the state transitions, not the reverse.

Express component-level state via `data-state` attributes in markup. Style them with `data-[state=loading]:opacity-50` Tailwind variants or `[data-state="loading"] {}` in custom CSS. Use shadcn's `data-slot` attribute for nested-element styling hooks (`data-slot="trigger"`, `data-slot="content"`). Both keep state logic in markup and visual logic in CSS, with no className toggling.

**During markup:** all colors, spacing, and typography use Tailwind utility classes backed by tokens (`bg-background`, `text-foreground`, `p-4`, `gap-2`). No hardcoded hex values. No inline `style` with raw color values. Custom one-off styles go in `@layer components` blocks in the global stylesheet, never inline.

Default stack: React 19 + Tailwind v4 + shadcn/ui + `cn()` + `cva`.

**Required:** invoke `frontend-design` for aesthetic direction within token constraints. Verify by producing a one-paragraph direction statement before component work begins.

**For animated SVGs:** invoke `svg-animations` first to structure paths and coordinate system correctly. Then invoke the appropriate GSAP skill for the animation layer:
- `gsap-core`: basic tweens, easing, stagger
- `gsap-timeline`: sequenced multi-step animations
- `gsap-plugins`: DrawSVG (stroke tracing), MorphSVG (shape transitions), MotionPath (element along a path)
- `gsap-react`: when inside a React component — handles `useGSAP`, refs, and cleanup

### 6b. Hero image (full aesthetic builds only)

After the direction from Step 3's Wide-Net Variant Sequence is chosen:

1. Generate 4 hero-image variants via your image-generation tool of choice, sourced from the taste library's reference images for the chosen aesthetic family. Pick one, then generate color/mood variants of that pick and pick a final. Full sequence in `references/taste-library-and-variants.md`.
2. Polish the hero-to-body transition and page-load sequencing with `gsap-timeline`/`gsap-core`. No abrupt cuts.

### 6c. Tweaks Bar (mandatory, every build, no exceptions)

Build a thorough live tweaks panel for every build, full page or single component, regardless of build size. Not conditional, not a fallback for when `impeccable live` "can't expose a control," a separate requirement that always runs. Full rationale and coverage checklist in `references/taste-library-and-variants.md`. It is a forcing function for the token-first/state-first architecture Step 6 already mandates. A control can only exist for a token or state that the build actually factored out cleanly.

Run `impeccable live` too, as a supplement (it's a hard dependency, always available), but it does not replace this. Coverage: every DESIGN.md token this build actually uses, every named motion parameter, every defined component state, all live-toggleable. For full-aesthetic builds, also cover hero-level decisions once 6b's pick is locked.

Report this step's outcome in Step 8, not by inference from silence. Step 8 is where an
omitted Tweaks Bar becomes visible instead of quietly passing as done.


### 7. Validate

Before shipping any UI:

- **Impeccable detect**: run `npx impeccable detect .` and resolve findings (or justify skips). Hard dependency, always installed by Step -1. See `references/design-quality-tools.md`.
- **WCAG AA**: text contrast ≥ 4.5:1, UI elements ≥ 3:1. See `references/wcag-checks.md`.
- **Token integrity**: no orphaned token names, no broken `var(--...)` references.
- **Responsive**: mobile-first, touch targets ≥ 44px.
- **State coverage**: every defined state has a visual representation.
- **Polish artifact scan**: for static prototypes and inherited drafts, grep for visible/internal artifacts before delivery: `placeholder|pending|TBC|Junior pass|Created by|style=`. Remove or justify every hit. Then regenerate screenshots.
- **Screenshot + vision loop**: capture desktop and mobile screenshots, then run vision review. Ask it specifically about clipping, awkward wrapping, horizontal overflow signs, placeholder-looking areas, and touch-target issues. Fix concrete findings and rerun only the affected screenshots.
- **Micro-details pass**: check text-wrap, nested radii, shadow layering, transition/animation timing, font smoothing, and alignment against `references/micro-details.md`.

Run `design.md diff` if tokens changed from a prior version.

**Tailwind v4 + CSS checks:**
- `:root` and `.dark` are the only places token values (HSL) appear
- `@theme inline` block maps tokens to Tailwind namespaces, no values redefined
- No hardcoded hex/HSL in components or markup
- No inline `style` attributes with raw colors
- `cn()` used for conditional classes, no string concatenation
- Custom CSS lives in `@layer components` or `@layer utilities`, not unlayered globals
- Light/dark switching driven by `.dark` class, not separate stylesheets

### 8. Post-Build Confirmation (mandatory gate, before declaring the task done)

The Pre-Build Manifest catches a skipped dependency or a skipped pre-build decision. It
cannot catch a skipped post-build step, since Hero image, Tweaks Bar, and Validate all
run after that manifest is signed off. This gate closes that hole the same way: a fixed
template, printed in chat, before any "done", "built", or "here's your page" message.

```
POST-BUILD CONFIRMATION for: <task description>

- Hero image (Step 6b): INVOKED <variants generated, final pick> | N/A <reason, e.g. single component, no hero section>
- Tweaks Bar (Step 6c, mandatory every build): INVOKED <controls exposed: which tokens, which motion parameters, which states>, no N/A path
- Validate (Step 7): PASSED <impeccable detect result, contrast/responsive/state checks> | FAILED <finding, fix applied>
- Design ledger file: WRITTEN <path> | UPDATED <path>
```

A summary sentence like "built the page, ran the quality check, done" does not satisfy
this gate. Each of the four lines above needs its own verdict. If you catch yourself
about to declare the task finished without having printed this block, stop and print it
first. An omitted Tweaks Bar row is not a smaller failure than an omitted dependency row.
Both are the same failure: a mandatory step that ran silently, or did not run at all, and
nothing forced it into view.


## Optional: machine-level enforcement

Everything above is self-sufficient. The gates run from the skill text and
your agent's own discipline in following it, with the Pre-Build Manifest
and the scope-spec file as the two artifacts that make the gates checkable
after the fact.

If your harness supports pre-write hooks (something that runs before a
file write and can block it), you can add a second, independent
enforcement layer: block the first UI file write in a session until the
scope-spec file and the design-ledger file both exist and carry a verdict
for every required row. That is a machine-level backstop on top of the
skill's own chat-level gate, not a replacement for it. Build one against
whatever hook mechanism your own harness exposes. There is no external
dependency required to make the skill itself work correctly without one.

## Failure Modes

| Symptom | Fix |
|---------|-----|
| One-shot a full page/design direction without variants | Stop. Run the Wide-Net Variant Sequence (N -> 1 -> 3 -> 1) from `references/taste-library-and-variants.md`. One-shotting regresses to generic output. |
| Taste library exists but wasn't checked | Stop. Step 0 runs before Step 1, every new page/full-site task. Source aesthetic families and references from it. |
| Guardrails given as only "avoid AI slop" with no named patterns | Reject. Require concrete named bans (specific gradients, fonts, decorative patterns), per the 4-part prompt structure in `references/taste-library-and-variants.md`. |
| Manifest marked any dependency N/A or NOT FOUND for "not installed" | Reject. Every Required Sub-Skill and both external tools are hard dependencies since Step -1. Run that step's filesystem loop, install every MISSING name from its table row, then continue. No N/A path exists for this reason. |
| Proceeded past Step -1 with a MISSING line unresolved | Stop. Return to Step -1. A dependency install is not optional work to be deferred until the build needs it. |
| Impeccable/Taste Skill installed but never invoked | Run `impeccable detect` at Anti-slop and Validate gates at minimum. Reach for named commands (`bolder`, `overdrive`, etc.) for targeted fixes. |
| Advisor Mode skipped for a full-page/full-aesthetic task because the ask "seemed specific enough" | Reject. Advisor Mode is unconditional for this task class since Step 0. Specificity is not a valid skip reason. |
| Advisor Mode skipped for a full-page task because a taste library exists | Reject. Taste library and Advisor Mode are additive, not either/or. Run both, feed both into Step 3. |
| Single-component/established-project task silently skipped Advisor Mode with no question asked | Stop. The Confidence Check batch (Execution Contract item 3) must include the opt-in question before deciding either way. |
| Declared the task done without printing the Step 8 Post-Build Confirmation | Failure. Print the fixed template first: Hero image, Tweaks Bar, Validate, ledger file. A closing summary sentence does not substitute for it. |
| Tweaks Bar (Step 6c) skipped, marked N/A, or treated as a fallback for when `impeccable live` "isn't enough" | Reject. Mandatory for every build, no N/A path, not a fallback. Build it, then report it on Step 8. |
| Manifest or Post-Build Confirmation given as a prose paragraph instead of the literal template | Reject. Neither gate is satisfied by a summary that reads as complete. Every listed line needs its own id and verdict. |
| Tweaks Bar built but missing a control for a DESIGN.md token the build uses, a motion parameter, or a component state | Reject. Coverage means every one the build actually has, a partial panel is a failed one. |
| Iterating by repeated vague re-prompts ("make it more premium") | Stop. Use the build's own Tweaks Bar (mandatory, Step 6c) or `impeccable live` for direct visual iteration instead of guessing through the terminal. |
| Skipped DESIGN.md, hardcoded hex in CSS | Stop. Extract to CSS vars. Update DESIGN.md tokens. |
| Hand-rolled a component that exists in registry | Delete. Add registry component. Customize to tokens. |
| WCAG check not done | Run contrast check now. Fix failing pairs before merge. |
| States emerged during coding, not defined first | Refactor: define state machine first, rebuild markup around it. |
| DESIGN.md draft generated but not confirmed | Stop. Show draft. Wait for user approval. |
| Used Three.js/WebGL for SVG animation | Delete. Use `svg-animations` + `gsap-plugins` instead. |
| Built presentation/motion export in React + shadcn | Delete. Rebuild with `huashu-design` starter components. |
| Guessed a design direction without vague-requirement check | Stop. Invoke `huashu-design` Advisor Mode. Show 3 directions. Wait for pick. |
| Used purple gradient, emoji icons, or left-border card | Anti-slop violation. Remove. Replace with brand-derived or neutral alternatives. |
| Built one version of a new page without showing variations | Generate 2 alternatives using `design_canvas.jsx`. Show all 3 before committing. |
| Used invented token values for brand work | Stop. Run Brand Asset Protocol. Find real logo/color/font sources. |
| Hardcoded hex/HSL in markup or component | Extract to `:root`/`.dark` in global CSS. Use `var(--token)` or Tailwind utility. |
| Token values defined outside `:root` and `.dark` | Move to `:root` (light) and `.dark` (dark mode override). Single source. |
| `@theme inline` block missing or redefining values | Add `@theme inline` mapping `--background` → `--color-background` etc. No values redefined. |
| Used Tailwind v3 `tailwind.config.js` patterns | Migrate to v4 CSS-first config via `@theme` directive in CSS. |
| Inline `style` attribute with raw colors | Replace with Tailwind utility class or `var(--token)` reference. |
| Custom CSS unlayered, breaking shadcn cascade | Wrap in `@layer components` or `@layer utilities`. Unlayered = app overrides only. |
| Used `React.forwardRef` for shadcn component | Switch to named function pattern. `forwardRef` removed in shadcn v4 update. |
| Used paired `w-* h-*` for square elements | Replace with `size-*` utility (Tailwind v4 + shadcn convention). |
| String concatenation for conditional classes | Use `cn()` helper from `lib/utils.ts`. |
| className toggling for state | Use `data-state` attributes. Style via `data-[state=loading]:*` Tailwind variants. |
| Skipped Pre-Build Manifest, jumped to code | Stop. Produce manifest. Get sign-off before any UI code. |
| Manifest used "Not needed" without specific reason | Reject. Replace with a one-line reason that names what about this task removes the trigger. |
| Delivered inherited/static prototype with visible draft artifacts (`placeholder`, `TBC`, `Junior pass`, inline `style=` cruft) | Run the polish artifact scan, remove public-facing internals, replace placeholders with intentional treatments or real assets, then rerender screenshots. |
| Screenshot/vision review finds mobile clipping, awkward CTA wrapping, or diagram edge cropping | Fix the concrete CSS/layout issue, regenerate the affected screenshot, and rerun the targeted visual review before delivery. |
| Built single-file HTML without DESIGN.md or token discipline | Failure. Use the static/single-file demo branch. Tokens in `:root`/`.dark`, native `@layer`, no raw hex. |
| Skipped registry discovery, hand-rolled all components | Stop. Run shadcn/21st.dev/ElevenLabs check. Replace hand-rolled with registry components above 80% coverage. |
| Skipped `frontend-design` invocation for aesthetic work | Stop. Invoke `frontend-design`. Apply direction within token constraints. |
| Skipped `spline-3d-integration` when hero needs depth/3D | Stop. Invoke `spline-3d-integration`. Embed scene. Do not hand-roll WebGL. |
| Animated SVG built without `svg-animations` + `gsap-*` | Stop. Restructure paths via `svg-animations`. Add motion via correct `gsap-*` skill. |
| Wrote UI code without producing Pre-Build Manifest first | Failed task. Discard the code. Restart from Step 1 of the Execution Contract. The manifest is non-negotiable. |
| User said "skip the manifest" or "just build it" | Refuse politely. The manifest is the user's review surface, not yours to bypass on their behalf. Produce the manifest. |
| Inferred approval from silence or a vague reply | Failed gate. Treat as not-approved. Re-prompt for explicit "go" / "approved" / "proceed". |
| Skill loaded but executor went straight to code | Acknowledge the skill at turn start. State the build target. Run Steps 1-5 in order before any code. |
| DESIGN.md missing for a single-file demo | Create one anyway. Tokens still need a source-of-truth document, even when output is one HTML file. |
| Started Step 1 with unclear inputs (build target, brand, scope, tech stack) | Failed gate. Stop. Batch all unknowns into one `AskUserQuestion` call (1–4 questions). Wait for answers. |
| Asked clarifying questions one at a time when they could be batched | Replace with a single `AskUserQuestion` call carrying all independent unknowns as separate questions. |
| Assumed a default (e.g. picked a brand archetype, build target, or palette) without asking | Stop. Roll back the assumption. Add it to the batched question set. Let the user pick. |
| Confidence check skipped because the request "felt clear" | Re-evaluate the six axes (build target, brand, aesthetic, scope, audience, tech stack). If any is below ~90%, the gate fires. |
| Confidence Check resolved but no scope-spec file written | Reject. The spec file is mandatory for every task, not just vague/large ones. Write it before Step 0. |
| Scope-spec written with an empty journey-matrix cell left blank | Reject. An empty cell means the interview didn't reach it. Fill it in or move it to Non-goals explicitly. |
