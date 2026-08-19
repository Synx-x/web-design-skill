# Component Registry Reference

## Decision Tree

Before building any component:

1. Search shadcn/ui for a base primitive
2. Search 21st.dev for a richer variant or AI-specific pattern
3. Search ElevenLabs UI if voice or agent state is involved
4. Hand-roll only if no registry covers 80%+ of the need

## shadcn/ui

Site: `https://ui.shadcn.com/docs/components`

Use for all base UI primitives. Composable, accessible, uses CSS vars natively.

Install: `npx shadcn@latest add <component>`

**Component categories:**
- Layout: Separator, ScrollArea, AspectRatio
- Forms: Button, Input, Textarea, Select, Checkbox, RadioGroup, Switch, Slider, Form
- Feedback: Alert, Badge, Progress, Skeleton, Toast / Sonner
- Overlay: Dialog, Sheet, Drawer, Popover, Tooltip, HoverCard, DropdownMenu, ContextMenu, Menubar, NavigationMenu
- Display: Card, Table, Avatar, Calendar, Carousel
- Data: Accordion, Collapsible, Tabs, Command

Why first: shadcn components use `var(--primary)`, `var(--background)`, etc. natively. DESIGN.md tokens map directly with no additional work.

## 21st.dev

Site: `https://21st.dev/home`

Use for AI/agent UIs, chat interfaces, dynamic content, and richer visual variants beyond shadcn's defaults.

Install via CLI — no MCP, no copy-paste:
```bash
npx shadcn@latest add https://21st.dev/r/<component-name>
```

Find the component name from the URL when browsing 21st.dev (e.g. `https://21st.dev/r/animated-hero` → `animated-hero`). The shadcn CLI fetches the component JSON directly from the registry endpoint.

Best for:
- Agent/chatbot UI blocks (message threads, typing indicators)
- Hero sections, landing page components
- Complex card layouts, dashboard widgets
- Animated transitions and micro-interactions

Stack assumption: React + Tailwind. May need token adaptation to CSS custom properties. Check each component's dependencies before installing.

## ElevenLabs UI

Site: `https://ui.elevenlabs.io/docs`

Use for voice agent UIs, real-time audio visualizers, and pre-wired agent state-machine components.

Install: `@elevenlabs/cli@latest components add <component>`

Key components:
- ConvAI: full conversational AI widget with built-in state handling
- AudioVisualizer: real-time waveform display
- Voice state components: idle, listening, thinking, speaking states are pre-wired

Reach for ElevenLabs UI when the task involves voice input/output, real-time agent interaction, or audio streaming.

## The 80% Rule

If a registry component covers 80%+ of what's needed: install and customize. Never hand-roll what a registry provides.

If nothing covers 80%: build token-first. All color, spacing, and typography values come from DESIGN.md custom properties. No hardcoded hex. No inline styles with raw color values. States defined upfront before any markup.
