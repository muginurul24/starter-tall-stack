# Modern CSS Feature Reference

Use this reference when selecting current CSS features or writing fallback strategies. Browser support changes; verify with MDN, web.dev Baseline, caniuse, or official browser docs when support status affects production risk.

## Layout And Responsiveness

- Container queries: prefer `@container` over viewport breakpoints for reusable components. Name containers when multiple ancestors could match.
- Style queries and scroll-state queries: use for state-aware components when support is acceptable; otherwise fall back to classes or attributes.
- Subgrid: use to align nested content to parent grid tracks without duplicating measurements.
- Logical properties: prefer `inline` and `block` properties for direction-safe spacing, sizing, borders, and positioning.
- Dynamic viewport units: use `dvh`, `svh`, and `lvh` for mobile viewport behavior instead of plain `vh` when browser chrome matters.
- `aspect-ratio`: use for media, tiles, canvases, charts, and fixed-format panels to prevent layout shift.
- `clamp()`, `min()`, `max()`: use for bounded spacing and sizing, not for viewport-scaled body text.

## Selectors And Cascade

- `:has()`: use for parent/state styling, form states, selected child styling, and content-aware layout. Keep selectors shallow and scoped.
- `:is()`: reduce repeated selector groups.
- `:where()`: apply base styles with zero specificity so components can override easily.
- `@layer`: declare cascade order explicitly; avoid fighting specificity with `!important`.
- `@scope`: use for local style boundaries where project support allows it.
- Nesting: use native nesting in moderation; keep depth low and avoid generating overly specific selectors.

## Color And Theming

- `oklch()` and `lab()`: use for perceptual color choices and theme scales.
- `color-mix()`: derive hover, active, border, shadow, and subtle background states from tokens.
- Relative color syntax: derive related colors while preserving token relationships when support is acceptable.
- `light-dark()`: use with `color-scheme` when native light/dark values are appropriate.
- `contrast-color()`: use only when target browser support is acceptable; otherwise choose explicit accessible foreground tokens.
- `accent-color`: align native form controls with the brand while preserving accessibility.

## Interaction And Motion

- `@starting-style`: animate newly inserted elements without JavaScript measurement hacks.
- `transition-behavior: allow-discrete`: transition discrete properties such as `display` when supported.
- Scroll-driven animations: use `animation-timeline`, `scroll()`, and `view()` for progress or reveal effects tied to scroll. Avoid decorative scroll effects in dense tools.
- View transitions: use for meaningful state/page continuity. Keep transitions short and respect reduced motion.
- Anchor positioning: use for popovers, tooltips, menus, and callouts anchored to controls. Provide a conventional absolute/fixed fallback when needed.
- Popover and top layer styling: prefer native popover/dialog behavior before custom stacks.

## Typography And Text

- `text-wrap: balance`: use for short headings and compact titles.
- `text-wrap: pretty`: use for paragraphs when supported and when it improves readability.
- `hyphens: auto`: use for narrow text columns with the correct `lang` attribute.
- `font-size-adjust`: use when fallback font metrics need to preserve readable sizing.
- Avoid negative letter spacing unless matching an existing brand system.

## Media And Performance

- `image-set()`: provide resolution-aware background images.
- `content-visibility`: use for long off-screen content, but avoid it when it breaks find-in-page, anchor navigation, or measurement-sensitive UI.
- `contain-intrinsic-size`: pair with content visibility or lazy rendering to reduce layout shift.
- `contain`: use for isolation when it helps rendering performance and does not break positioning or overflow needs.

## Accessibility Media Features

- `prefers-reduced-motion`: remove or simplify nonessential motion.
- `prefers-color-scheme`: support dark/light mode when the project has tokens for both.
- `forced-colors`: preserve usability in high contrast modes; avoid forcing decorative backgrounds.
- `prefers-contrast`: increase contrast or reduce subtle state styling when supported.
- `pointer` and `hover`: adapt dense hover-heavy controls for touch devices.

## Fallback Pattern

Use baseline CSS first, then enhance:

```css
.menu {
  position: absolute;
  inset-block-start: 100%;
  inset-inline-start: 0;
}

@supports (anchor-name: --trigger) {
  .trigger {
    anchor-name: --trigger;
  }

  .menu {
    position-anchor: --trigger;
    inset-block-start: anchor(block-end);
    inset-inline-start: anchor(inline-start);
  }
}
```

Do not use a new feature just to appear modern. Use it when it removes code, improves behavior, or makes the design more resilient.
