# WCAG AA Compliance Reference

## Contrast Requirements

| Element type | Minimum ratio | Fails at |
|---|---|---|
| Normal text (below 18pt or 14pt bold) | 4.5:1 | 4.49:1 |
| Large text (18pt+ or 14pt+ bold) | 3:1 | 2.99:1 |
| UI components (inputs, buttons, focus rings) | 3:1 | 2.99:1 |
| Decorative elements, logos, disabled states | Exempt | N/A |

Source: WCAG 2.1 Level AA success criteria 1.4.3 (text) and 1.4.11 (non-text).

## Token Pairs to Check

Every DESIGN.md foreground/background token pair needs a contrast check:

| Pair | Min ratio |
|------|-----------|
| `--color-on-primary` on `--color-primary` | 4.5:1 |
| `--color-on-background` on `--color-background` | 4.5:1 |
| `--color-on-surface` on `--color-surface` | 4.5:1 |
| Error/success text on their backgrounds | 4.5:1 |
| Placeholder text on input background | 4.5:1 |
| Focus ring color on adjacent background | 3:1 |
| Button border on adjacent background | 3:1 |

## Quick Contrast Check Methods

**Browser DevTools**: Elements panel, click any color swatch, the panel shows the contrast ratio against the current background automatically.

**Online tool**: `https://webaim.org/resources/contrastchecker/`

**JS formula** (for scripting or design tokens validation):

```javascript
function luminance(r, g, b) {
  return [r, g, b].reduce((acc, v, i) => {
    v /= 255;
    v = v <= 0.03928 ? v / 12.92 : ((v + 0.055) / 1.055) ** 2.4;
    return acc + v * [0.2126, 0.7152, 0.0722][i];
  }, 0);
}

function contrastRatio(l1, l2) {
  const [light, dark] = [l1, l2].sort((a, b) => b - a);
  return (light + 0.05) / (dark + 0.05);
}

// Usage:
// contrastRatio(luminance(26, 26, 46), luminance(248, 249, 250)) >= 4.5
// → true means passes AA for normal text
```

## design.md CLI (unverified, optional)

No install source is known for this tool. `npm view design.md` returned 404 on
2026-08-19, so treat the commands below as unverified. The contrast formula above
covers the same check with no dependency. Use that instead unless you already have
this CLI on your machine.

If `design.md` is installed:

```bash
design.md lint          # runs all 7 rules including contrast checks
design.md diff          # shows token changes vs the prior version
```

## Responsive and Touch Checks

- Minimum touch target: 44x44px (per Apple HIG and Google Material guidelines)
- Spacing between adjacent targets: 8px or more
- Text resize: UI must stay functional at 200% browser zoom without content loss or overlap
- Do not convey meaning through color alone; add an icon or text label

## State Visibility Requirements

Every interactive state must be distinguishable:

| State | Requirement |
|-------|-------------|
| hover | Cursor change plus a visible change in color, border, or background |
| focus | Visible focus ring with 3:1 contrast against the adjacent surface |
| disabled | Reduced opacity (at least 30% reduction) or distinct color; cursor: not-allowed |
| error | Not color alone; pair with an icon or inline text label |

## Pre-Ship Checklist

- [ ] Text contrast >= 4.5:1 for all foreground/background token pairs
- [ ] Large text contrast >= 3:1
- [ ] Interactive element (button/input) contrast >= 3:1
- [ ] Focus ring visible at 3:1 contrast against surrounding surface
- [ ] Touch targets >= 44x44px
- [ ] All states (hover, focus, disabled, error) are visually distinct from each other
- [ ] No information conveyed by color alone
- [ ] UI stays functional at 200% browser zoom
- [ ] Every defined component state has a corresponding visual representation
