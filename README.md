# web-design

A design process for AI coding agents (Claude Code, Codex, Cursor, and others).
Instead of jumping straight to code, it makes the agent ask you the right
questions first, show you a plan, and confirm what it actually built at the
end.

> Quick start: `npx skills add https://github.com/Synx-x/web-design-skill -g -y`,
> then just ask your agent to build a page or component. You don't need to
> name the skill or run a special command, it kicks in on its own.

## Why this skill

Anthropic's `frontend-design` skill helps an agent pick a good look and feel.
This skill is the process wrapped around that: the checklist an agent runs
through before it writes any interface code, not another style guide. It
makes the agent write down what it's about to build, ask you a short set of
questions instead of guessing, show you a plan before touching any code, and
tell you exactly what it did once the build is finished.

## What's included

### The skill: web-design
One thing to install. Ask for any web design task, and before writing code
the agent will: check its tools are installed, ask you a short set of
questions, write down the plan, pick a visual direction, sketch or compare
layout options, check what's already built for this (so it isn't
reinventing a button), show you the final plan for approval, build it, and
report back on what it did.

### Sub-skills it uses

A sub-skill is a smaller, focused skill this one calls in for a specific
job, the way a general contractor calls in an electrician. This skill
installs and uses these automatically:

| Sub-skill | What it's for |
|-----------|----------------|
| `huashu-design` | Picks a visual direction, checks brand assets, compares layout options |
| `frontend-design` | General taste and visual direction |
| `spline-3d-integration` | Adds an interactive 3D scene, if one already exists to embed |
| `svg-animations` | Animated icons and illustrations |
| `gsap-core` / `gsap-timeline` / `gsap-plugins` / `gsap-react` | Motion and animation |
| `excalidraw-diagram` | Quick visual sketches |
| `brainstorming` | Working through layout ideas and tradeoffs |
| `higgsfield-generate` | Generates a hero image for the page |
| `redesign-existing-projects` | Scans and diagnoses an existing project's code for a review |
| `avoid-ai-writing` | Checks any written text the agent produces for you (a critique, a plan, a summary) before showing it, and cleans it up if it reads stiff or robotic |

Full details and install commands for each one are in `SKILL.md`.

### What the skill guarantees

- It never skips a missing tool. If something it needs isn't installed, it
  installs it, it doesn't just shrug and move on.
- It never invents an answer to a question you were supposed to answer. If
  it's unsure what you want, it asks.
- Choosing a design direction and approving the final build plan are two
  separate steps. Picking a direction doesn't count as approving the plan.
- Every build gets a small settings panel so you can tweak colors, spacing,
  and other details live, in the browser, without asking the agent to redo
  the work.
- At the end of every build, the agent tells you exactly what it did and
  didn't do, no vague "all done!" summaries.
- If a task needs a 3D model, the agent checks whether you already have one
  before building a custom one from scratch.

## Reviewing existing work

You don't have to be building something new. You can also ask it to
critique or improve a design you already have:

```
Review my landing page and tell me what's wrong with it.
Here's a screenshot of my app, what would you improve?
Audit my site at mysite.com for accessibility issues.
```

Point it at a screenshot, a live website, or your project's code, and it
will give you a written critique: what's working, what isn't, and how
serious each issue is. It won't touch your code unless you ask it to fix
something. If you do ask for a fix, it only changes what you asked for,
it doesn't rebuild the whole page.

## Installation

### Option 1: skills CLI (recommended)

```bash
npx skills add https://github.com/Synx-x/web-design-skill -g -y
```

Drop `-g` to install it for just the current project instead of every
project on your machine.

### Option 2: git clone into a project

```bash
git clone https://github.com/Synx-x/web-design-skill .claude/skills/web-design
```

Change the folder path to match your tool: `.codex/skills/`,
`.cursor/skills/`, `~/.agents/skills/`, and so on.

## Usage

There's no special command to type. Just describe the task:

```
Design a landing page for a coffee subscription service.
Add a pricing card component to my existing React app.
```

The agent will say it's using this skill, check its tools are ready, then
stop and ask you a few questions before doing anything else.

## Requires

- [Impeccable](https://github.com/pbakaus/impeccable) and the
  [Taste Skill](https://github.com/Leonxlnx/taste-skill), two design-quality
  tools this skill relies on. You don't need to install these yourself,
  the skill installs whichever one is missing the first time it runs.
- An AI coding tool that supports the [skills CLI](https://skills.sh/)
  standard for installing skills.

## Supported tools

Anything the [skills CLI](https://skills.sh/) supports: Claude Code, Codex
CLI, Cursor, and the other tools it lists.

## Contributing

Found a bug or have an idea? Open an issue or pull request at
[github.com/Synx-x/web-design-skill](https://github.com/Synx-x/web-design-skill).

## What's in this repository

- `SKILL.md`: the full set of instructions the agent follows
- `references/`: supporting detail, like color-contrast rules, where to
  find reusable UI components, and a checklist for small polish details
