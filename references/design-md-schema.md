# DESIGN.md Schema Reference

Source: google-labs-code/design.md (Google DESIGN.md format)

## YAML Frontmatter Structure

```yaml
---
visual-theme: |
  <prose description of the visual direction>

colors:
  primary: "#hex"
  secondary: "#hex"
  background: "#hex"
  surface: "#hex"
  on-primary: "#hex"        # text color on primary background
  on-background: "#hex"     # text color on background
  on-surface: "#hex"
  error: "#hex"
  success: "#hex"
  warning: "#hex"
  border: "#hex"
  muted: "#hex"
  on-muted: "#hex"

typography:
  font-family-heading: "Font Name, fallback"
  font-family-body: "Font Name, fallback"
  font-family-mono: "Mono Name, monospace"
  scale:
    xs: "0.75rem"
    sm: "0.875rem"
    base: "1rem"
    lg: "1.125rem"
    xl: "1.25rem"
    2xl: "1.5rem"
    3xl: "1.875rem"
    4xl: "2.25rem"

spacing:
  unit: "4px"
  scale: [4, 8, 12, 16, 24, 32, 48, 64, 96]

components:
  - name: Button
    variants: [primary, secondary, ghost, destructive]
    states: [default, hover, focus, disabled, loading]
  - name: Input
    variants: [default, error, success]
    states: [default, focus, disabled]

layout:
  max-width: "1280px"
  columns: 12
  gutter: "16px"
  breakpoints:
    sm: "640px"
    md: "768px"
    lg: "1024px"
    xl: "1280px"

depth:
  shadows:
    sm: "0 1px 2px rgba(0,0,0,0.05)"
    md: "0 4px 6px rgba(0,0,0,0.07)"
    lg: "0 10px 15px rgba(0,0,0,0.10)"
  radii:
    sm: "4px"
    md: "8px"
    lg: "16px"
    full: "9999px"

responsive:
  approach: mobile-first
  touch-target-min: "44px"

do-dont:
  do:
    - Use token names, never raw hex values
    - Define all component states before writing markup
  dont:
    - Use more than 3 font families
    - Hardcode pixel values outside the spacing scale
    - Use color alone to convey meaning
---
```

## Prose Sections (required, in canonical order)

1. **Visual Theme**: mood, atmosphere, reference designs
2. **Colors**: rationale for palette choices; accessibility notes
3. **Typography**: font selection reasoning; scale rationale
4. **Components**: shared interaction patterns across components
5. **Layout**: grid rationale; how breakpoints were chosen
6. **Depth**: shadow/radius philosophy; elevation system
7. **Responsive**: mobile-first strategy; touch/pointer notes
8. **Do/Don't**: antipatterns specific to this design system

Tool parsers expect sections in this order. Reordering breaks lint.

## Token Naming Conventions

Semantic names only; never color-scale names:

| Correct | Incorrect |
|---------|-----------|
| `--color-primary` | `--color-blue-500` |
| `--color-on-primary` | `--color-white` |
| `--space-4` | `--margin-small` |
| `--color-error` | `--color-red` |

shadcn/ui built-in token names: `--primary`, `--primary-foreground`, `--secondary`, `--secondary-foreground`, `--background`, `--foreground`, `--muted`, `--muted-foreground`, `--border`, `--ring`, `--radius`.

## CSS Custom Properties Compilation

DESIGN.md tokens compile to a `:root {}` block in `globals.css`:

```css
:root {
  --color-primary: #1a1a2e;
  --color-on-primary: #ffffff;
  --color-background: #f8f9fa;
  --space-4: 4px;
  --space-8: 8px;
  --font-heading: "Inter", sans-serif;
}
```

For shadcn/ui projects, map DESIGN.md tokens to shadcn CSS vars. shadcn expects HSL values:

```css
:root {
  --primary: 234 45% 14%;           /* HSL, no hsl() wrapper */
  --primary-foreground: 0 0% 100%;
  --background: 210 17% 98%;
  --foreground: 222 47% 11%;
}
```

Convert hex to HSL before mapping. Tools: `https://www.rapidtables.com/convert/color/hex-to-hsl.html`

## Lint Rules (7 rules, from design.md spec)

| Rule | Description |
|------|-------------|
| contrast | Text on any background >= 4.5:1; UI elements >= 3:1 |
| broken-refs | All `var(--token)` refs must resolve to declared tokens |
| orphaned-tokens | Declared tokens must appear in at least one component |
| missing-primary | `primary` and `on-primary` pair required |
| missing-background | `background` and `on-background` pair required |
| font-stack | Every font-family must include a generic fallback |
| spacing-scale | All spacing values must come from the defined scale |
