# Taste Library, Wide-Net Variants, and the Tweaks Bar

Source: [Turn Claude Into A Design GENIUS In 3 Simple Steps](https://www.youtube.com/watch?v=7FU98O0JLHs)
(Chase AI, 2026-07-23). Method for injecting a specific, non-generic aesthetic instead
of taking whatever the model regresses to by default.

## Taste library (persistent, ongoing, not per-task)

A curated folder of screenshots, tagged by aesthetic family, that seeds every future
design task. Build it once, add to it over time, reuse across projects.

**Where to look**: Dribbble (search "web design," sort popular), Pinterest (search
"web design," browse hero pages), Twitter/X (design/UI creator accounts, often the
best source for work not indexed elsewhere).

**What to save**: screenshot or link for anything you like. Group by aesthetic family
as the library grows (e.g. "print-tech paper," "dither mono," "vast quiet cinematic,"
"classical remix," the four families used in the video's worked example).

**Per-entry metadata** (manual, or scripted if the library gets large):
- Aesthetic family / keywords (e.g. "Vauxhall-rendered landscape")
- Image prompt (reusable for hero-image generation)
- Brief (a short paragraph capturing what makes this entry work, usable as a build prompt)

**Where it lives in a project**: no fixed path enforced. A `design/taste-library/`
folder at the repo root, or a separate personal library reused across projects, both
work. Document the chosen location in DESIGN.md once decided so future sessions find it.

**Precedence with `huashu-design` Advisor Mode**: not two competing gates, both feed the
same Wide-Net Variant Sequence, additively. Advisor Mode picks *which* directions to
explore; the Wide-Net Variant Sequence is *how* those directions get turned into
comparable full builds.

Advisor Mode is unconditional for any new-page/full-site/full-aesthetic-direction task,
regardless of whether a taste library exists or the ask already sounds specific. "The
user gave a specific direction" and "a taste library already exists" are both invalid
reasons to skip it: a specific ask still benefits from seeing distinct alternatives, and
a taste library's families still benefit from Advisor Mode's fresh, non-library-derived
picks alongside them. Run `huashu-design` Advisor Mode (3 philosophically distinct
conceptual directions) every time this task class fires.

- Taste library exists: its families plus Advisor Mode's 3 directions together seed the
  Wide-Net Variant Sequence below, one full build per direction across both sources.
- No taste library: Advisor Mode's 3 directions are the seed alone.

Don't run Advisor Mode's own build/pick flow separately from the Wide-Net Sequence,
they're the same step. Advisor Mode picks the directions, Wide-Net builds and compares
them.

For single-component or established-project tasks (existing DESIGN.md and taste library,
no new aesthetic direction being set): Advisor Mode isn't automatic, but it's also not a
silent skip. Ask the user directly, in the same batched question as the Confidence Check,
whether they want a fresh Advisor Mode pass anyway. Only proceed without it if they say no.

## The 4-part prompt structure

Not a rigid template. Four things to pass every time a design direction is being set,
in this order:

1. **Aesthetic**: the general design family this should follow (e.g. "vast, quiet,
   cinematic minimalism").
2. **Reference**: screenshot(s) or URL(s) from the taste library. The goal is matching
   *feel*, not copying content or layout. Multiple references are fine.
3. **Intent**: what is being built and why. Product type, target audience, the action
   the page should drive (book a demo, read and leave, fill a form). This shapes
   structure, not just decoration.
4. **Guardrails**: explicit do/don't list. Always name concrete anti-slop items here,
   not just "avoid AI slop": e.g. "never purple gradients," "never Inter font," "never
   3D SaaS blobs." Generic instructions get generic results; specific bans get specific
   results.

## Wide-Net Variant Sequence (full aesthetic builds, not structural wireframes)

Distinct from Step 3's structural wireframe (which compares layout/skeleton before any
visual treatment). This sequence compares full aesthetic directions and is the default
build path once directions exist to compare, from a taste library, from `huashu-design`
Advisor Mode, or from explicit user-named styles (see Precedence above).

**Funnel: N wide-net variants → 3 → 1, then hero, then tweaks.**

1. **N wide-net variants**, one per direction sourced upstream: N taste-library families,
   or Advisor Mode's 3 picks, or however many style directions the user named explicitly.
   The video's worked example used 5 taste-library families; 3-5 is typical. Build a full
   variant per direction, not a placeholder. Run each through `impeccable` and/or Taste
   Skill if installed, split across variants so the comparison also surfaces
   tool-quality differences. View all N side by side. Do not evaluate one at a time in
   isolation, the whole point is comparison.
2. **Pick one direction.** User selects the aesthetic family to commit to.
3. **3 refinement iterations.** Generate 3 versions of the chosen direction, varying a
   named structural axis (e.g. body layout: vertical minimal vs. sidebar index vs.
   framed sections). Pick one.
4. **Nail the hero image.** Generate 4 hero-image variants via the `higgsfield-generate`
   skill, or any image tool you already have, sourced from the taste library's reference
   images for that aesthetic family. Install command and the credentials caveat are in
   SKILL.md's Required Sub-Skills table. Pick one, then generate color/mood variants of that
   pick (the video's example: original, then color accent, golden hour, alpenglow,
   duotone) and pick a final.
5. **Polish transitions and motion.** Hero-to-body transition should not be abrupt.
   Page-load sequencing should feel deliberate (staggered reveal, not everything at
   once). Use `gsap-timeline` / `gsap-core` for this, not ad hoc CSS transitions.

## Tweaks Bar (mandatory, every build, not just post-hero refinement)

**Build a thorough tweaks panel for every build, full page or single component, no
exceptions.** This isn't just an iteration convenience, it's a forcing function: a
control that exposes a token or a state cleanly only exists if that token or state was
actually defined cleanly in the first place. A build that can't produce a working tweaks
control for its own fonts, colors, spacing, motion, and component states usually means
those weren't factored out as real tokens/states, they're buried inline. Building the
panel is how the skill's own token-first, state-first mandate (Step 6) gets checked, not
just claimed.

Guessing prompts ("make it more premium") to iterate visually is slow and imprecise
regardless of build size. A live tweaks panel replaces that: a floating control panel
exposing font choice, font size, accent colors, spacing, motion weight, reveal distance,
and every defined component state (`idle`/`loading`/`error`/`success`/etc., live-toggleable)
as controls, mimicking the pattern from Claude Design (Anthropic's own design tool).

**`impeccable live` is not a substitute, run both.** It's a useful quick-check (visual
variant mode, iterate on elements in the browser) worth trying alongside the build's own
panel, but it's generic across projects and won't expose this specific build's own named
tokens and component states the way a hand-built panel tied directly to this DESIGN.md
and this component tree will. There is no scenario where `impeccable live`'s presence
makes building the project's own thorough tweaks panel optional.

**Coverage, thorough means every one of these that the build actually has:**
- Every DESIGN.md token (colors, fonts, spacing scale) as a live-adjustable control
- Every named animation/motion parameter (weight, easing, reveal distance)
- Every defined component state, toggleable live, not just visible by accident of props
- For full-aesthetic builds specifically: also cover hero-level decisions (image variant,
  color/mood treatment), not just body-level, once the hero from the sequence above is
  locked

Every control must map directly to a DESIGN.md token, a named animation parameter, or a
named component state, never to an arbitrary inline style, so tweaks made live can be
captured back into DESIGN.md afterward rather than living only in the browser.

## Failure modes specific to this method

| Symptom | Fix |
|---|---|
| One-shot a design without variants | Stop. Run the 5-variant wide net first. One-shotting is what produces generic regression-to-the-mean output. |
| Taste library never referenced | Check for one before running Advisor Mode. If it exists, use its families alongside Advisor Mode's picks, additive, not either/or. |
| Advisor Mode skipped because a taste library covered it, or the ask seemed specific | Reject. Advisor Mode is unconditional for full-aesthetic-direction tasks. Neither reason is valid. |
| Tweaks panel treated as optional, or skipped because `impeccable live` covers iteration | Reject. Build the panel for every build regardless. `impeccable live` is a supplement, not a substitute. |
| Tweaks panel built but missing a control for a defined token or component state | Reject. Coverage means every DESIGN.md token, motion parameter, and defined state, not a partial set. |
| Guardrails section says only "avoid AI slop" | Reject. Require named concrete bans (specific gradient types, specific fonts, specific decorative patterns). |
| Reference given as vague description instead of image/URL | Ask for an actual screenshot or URL from the taste library. Descriptions alone regress toward generic output. |
| Iterating by re-prompting "make it look better" repeatedly | Stop. Build or invoke the tweaks bar (`impeccable live` or custom panel) instead of guessing through the terminal. |
| Hero and body built with no shared aesthetic reference | Body-level design decisions also need taste-library references, not just the hero section. |
