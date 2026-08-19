# web-design-skill test plan (Pi)

Status as of 2026-08-19: install succeeded, functional test blocked. See "Blockers" below
before running anything in this plan.

## 1. Dependency check (run first, every time)

`pi list` on this machine shows only `superpowers` and `web-design-skill` as skill-shaped
packages. None of the Required Sub-Skills below are installed. Confirm the current state
with `pi list` before assuming any row here is fixed.

| Sub-skill | Installed on Pi? | Install command |
|---|---|---|
| `huashu-design` | No | `pi install <huashu-design source>` |
| `spline-3d-integration` | No | `pi install <spline-3d-integration source>` |
| `svg-animations` | No | `pi install <svg-animations source>` |
| `gsap-core` / `gsap-timeline` / `gsap-plugins` / `gsap-react` | No | `pi install <gsap-* source>` per variant |
| `frontend-design` | No | `pi install <frontend-design source>` |
| `excalidraw-diagram-skill` | No | `pi install <excalidraw-diagram-skill source>` |
| A brainstorming/exploration skill | Unconfirmed. `npm:superpowers` is installed but its package directory has no bundled `SKILL.md` files on this machine, only a `lib/` folder and a README | Confirm inside a live Pi session, or install a dedicated one |
| Impeccable (`npx impeccable install`) | Unconfirmed, not checked from inside Pi | `npx impeccable install` |
| Taste Skill | Unconfirmed, not checked from inside Pi | `npx skills add https://github.com/Leonxlnx/taste-skill -g -y` |

## 2. Branch checklist

Run each prompt in the `pi-skill-test` herdr pane once Pi has a working model backend.
Pass means the named sub-skill is invoked. Fail means it is silently skipped, which is
the exact failure mode this plan exists to catch.

| Path | Prompt to give Pi | Pass signal | Fail signal |
|---|---|---|---|
| New full page/site | "Design a landing page for a fictional coffee subscription" | Runs `huashu-design` Advisor Mode unconditionally, shows 3 directions | Jumps straight to code, no directions shown |
| Single component, established project | "Add a pricing card to this existing app" | Asks in the Confidence Check batch whether to run `huashu-design` | Silently assumes yes or no without asking |
| Named brand | "Design a page for Nike's new running shoe" | Runs Brand Asset Protocol, searches for real Nike assets | Invents colors/fonts with no asset search |
| Variant comparison | Any full-page task past Step 3 | Uses `huashu-design`'s `design_canvas.jsx` to show variants side by side | Shows variants as separate unlinked outputs |
| Deck/motion export | "Build a 5-slide pitch deck with slide transitions" | Uses `huashu-design` starter components (`deck_index.html`, `animations.jsx`) | Builds it in React + shadcn |
| Hero needs 3D | "Landing page hero with an interactive 3D product model" | Invokes `spline-3d-integration`, embeds a Spline scene | Hand-rolls Three.js/WebGL |
| Animated SVG | "Animate this logo's stroke drawing on scroll" | Invokes `svg-animations` first, then the matching `gsap-*` skill | Uses CSS-only animation or skips straight to GSAP without path structuring |
| Aesthetic direction | Any task with visible UI | Invokes `frontend-design`, states a one-paragraph direction before building | No direction statement, jumps to code |
| Visual wireframes | "Sketch 3 layout options before building" | Invokes `excalidraw-diagram-skill` | Describes layouts in prose only |
| Layout exploration | Any task with more than one reasonable layout | Runs a brainstorming/exploration pass before committing | Picks one layout with no exploration step |
| Scope spec gate | Any task, no exceptions | Writes the scope spec file (feature table, journey matrix, non-goals) before Advisor Mode or Orient run | Starts building without a spec file on disk |
| Tooling gate | Any task, no exceptions | Confirms Impeccable and Taste Skill are installed, or installs them, before Step 0 | Skips straight past the check |

## 3. Blockers (confirmed 2026-08-19)

- Pi's actual live backend, `qwen-vast` at `http://206.223.162.3:16893/v1`, refused the
  connection when tested with `pi -p "list every skill you have available"`.
- `~/.pi/agent/settings.json` names a different default (`ollama` / `ornith-64k:latest`),
  not the backend that actually ran. That mismatch is unresolved.
- Local Ollama is not a fallback: `systemctl status ollama` shows it has been
  `inactive (dead)` since 2026-08-16, and both `::1:11434` and `127.0.0.1:11434` refuse
  connections.
- None of this plan's dependency checks have been run live against a working Pi session.
  Everything in section 1 is a static filesystem check (`pi list`), not a confirmed
  runtime trigger.

## 4. Before this plan is actually runnable

1. Point Pi at a reachable model (fix the Vast.ai endpoint, restart local Ollama, or pick
   a different configured provider). This needs your call. I won't restart a system
   service without asking first.
2. Install each Required Sub-Skill in Pi via its own `pi install <source>` call. There is
   no bundled equivalent today.
3. Confirm Impeccable and Taste Skill from inside a live Pi session, not just by
   filesystem presence.
