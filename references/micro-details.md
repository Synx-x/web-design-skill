# Interface micro-details

Nine small-scale CSS/interaction rules that separate polished interfaces from flat ones.
Source: [Details that make interfaces feel better](https://jakub.kr/writing/details-that-make-interfaces-feel-better) (jakub.kr), fetched and quoted directly. Verify against the live article if a rule looks stale.

## 1. text-wrap: balance vs pretty

Titles wrapping unevenly (one short orphan word on its own line) read as sloppy. `balance` distributes text evenly across lines, good for short, large-type titles. `pretty` avoids orphaned words at sentence ends, better for body/description text, since `balance`'s even-distribution cost is too expensive for the browser to compute on long runs.

```css
h1, .title { text-wrap: balance; }
p, .description { text-wrap: pretty; }
```

## 2. Nested border-radius (concentric offset)

When an element sits inside a padded parent, mismatched radii make the corners look "off" even if neither radius is wrong in isolation. Rule: **outer radius = inner radius + padding**.

```css
/* padding: 8px, inner radius: 12px -> outer radius must be 20px */
.card { border-radius: 20px; padding: 8px; }
.card > .inner { border-radius: 12px; }
```

## 3. Layered transparent shadows over solid borders

Solid-color borders assume a specific background; they look wrong the moment the component sits on a different surface (dark card, image, gradient). A layered `box-shadow` using low-alpha black is background-agnostic and adds depth a flat border can't.

```css
.border-shadow {
  box-shadow:
    0px 0px 0px 1px rgba(0, 0, 0, 0.06),
    0px 1px 2px -1px rgba(0, 0, 0, 0.06),
    0px 2px 4px 0px rgba(0, 0, 0, 0.04);
}
/* hover: darken each layer slightly instead of swapping to a border */
.border-shadow:hover {
  box-shadow:
    0px 0px 0px 1px rgba(0, 0, 0, 0.08),
    0px 1px 2px -1px rgba(0, 0, 0, 0.08),
    0px 2px 4px 0px rgba(0, 0, 0, 0.06);
}
```

## 4. Interruptible transitions vs keyframe animations

Transitions interpolate toward the latest target value and can be interrupted mid-flight; keyframe animations run a fixed timeline and don't retarget once started. Users change intent mid-interaction constantly (open a dropdown, then move the mouse away before it finishes). Use `transition` for anything state-driven and interactive, reserve `@keyframes` for one-shot staged sequences (toasts entering, splash reveals).

```css
/* interactive, must retarget if state flips mid-animation */
.dropdown { transition: transform 200ms ease, opacity 200ms ease; }

/* one-shot staged sequence, never retargets */
.toast { animation: enter 800ms cubic-bezier(0.25, 0.46, 0.45, 0.94) both; }
```

## 5. Staggered enter animations

Animating a whole block at once reads as one flat "pop." Breaking it into smaller pieces (title, description, buttons) and staggering their entrance reads as considered motion. Article-verified stagger values: **100ms** between sibling sections (title/description/buttons), **80ms** between individual words when a title is split into per-word spans.

```css
@keyframes enter {
  from { transform: translateY(8px); filter: blur(8px); opacity: 0; }
}
.animate-enter {
  animation: enter 800ms cubic-bezier(0.25, 0.46, 0.45, 0.94) both;
  animation-delay: calc(var(--delay, 0ms) * var(--stagger, 0));
}
.animate-enter-section { --delay: 100ms; }      /* title / description / buttons */
.animate-enter-word { --delay: 80ms; }           /* per-word title split */
```

## 6. Icon animations: opacity + scale + blur, spring easing

A contextual icon (state-change checkmark, loading spinner swap) feels more responsive animating on three axes at once: opacity, scale, and blur, rather than a plain fade. Spring easing (not cubic-bezier) suits this because it settles naturally instead of hitting a rigid stop. The source article demonstrates the spring parameters on Motion (Framer Motion successor) for an exit transition. It does not give a single combined opacity+scale+blur code sample, so treat the three-property combination as the design principle and the spring params below as verified for opacity/blur only:

```js
// Motion (JS), verified spring params from source
transition={{ type: "spring", duration: 0.45, bounce: 0 }}
```

CSS-only equivalent needs `@starting-style` + `transition-behavior: allow-discrete` for the same interruptible spring-like feel. No exact CSS spring curve was given in the source, so use a cubic-bezier approximation (e.g. `cubic-bezier(0.34, 1.56, 0.64, 1)`) if avoiding a JS animation library.

## 7. -webkit-font-smoothing: antialiased

On macOS, default subpixel antialiasing can render body text heavier than the design intends. `antialiased` switches to grayscale antialiasing, producing thinner, crisper text. Apply once at the layout root, not per-component.

```html
<body class="font-sans antialiased">
```

```css
body { -webkit-font-smoothing: antialiased; }
```

## 8. tabular-nums for dynamic numbers

A number that updates in place (countdown, live counter, price ticker) visibly shifts width as digits change unless every digit occupies equal width. `font-variant-numeric: tabular-nums` fixes digit width. Caveat from the source: some fonts (Inter named explicitly) change numeral shape when this is applied — check visually after enabling.

```css
.live-counter { font-variant-numeric: tabular-nums; }
```

```html
<!-- Tailwind -->
<span class="tabular-nums">{count}</span>
```

## 9. Optical alignment over geometric alignment

Geometric (measured, centered-by-the-numbers) alignment looks wrong in specific cases, most commonly a button with both an icon and text, where the icon's own visual weight/whitespace makes geometric centering read as off-center. Fix with a slightly smaller padding on the icon side (`margin`/`padding` at the component level), or better, bake the optical correction into the icon's own SVG viewBox so no consumer needs to remember to add offsetting padding.

```css
/* component-level fix */
.button-with-icon { padding-inline-start: 12px; padding-inline-end: 14px; }

/* preferred: bake the offset into the icon asset itself, not the consumer */
```
