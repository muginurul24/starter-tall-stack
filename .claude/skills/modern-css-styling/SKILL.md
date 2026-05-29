---
name: modern-css-styling
description: Use when creating, editing, reviewing, or refactoring frontend styling in HTML, CSS, Tailwind, Blade, JSX, Vue, Svelte, React, Livewire, Filament views, Filament dashboards, design systems, component libraries, landing pages, dashboards, forms, tables, navigation, responsive layouts, animations, themes, or accessibility-related UI work. Enforce modern CSS best practices, current platform features, progressive enhancement, maintainable architecture, accessible interaction states, responsive behavior, and a restrained contemporary visual style. Also use when the user asks for modern UI, latest CSS, CSS best practices, polished styling, responsive design, dark mode, animations, or to avoid generic AI-looking interfaces. For Filament dashboard/panel/widget styling, require uncodixfy too.
---

# Modern CSS Styling

Create UI styling that feels current, durable, accessible, and specific to the product. Prefer native CSS platform features before adding JavaScript or layout hacks.

## Workflow

1. Inspect existing UI conventions first: framework, design tokens, spacing scale, color palette, typography, component APIs, dark mode strategy, and naming conventions.
2. Keep the product context visible. Operational tools should be dense, quiet, and scannable; marketing or editorial surfaces can be more expressive when the content needs it.
3. Use modern CSS primitives where they reduce complexity: container queries, cascade layers, custom properties, logical properties, modern color functions, subgrid, `:has()`, `@scope`, `@property`, scroll-driven animation, anchor positioning, and view transitions.
4. Apply progressive enhancement for newer or unevenly supported features. Provide a solid fallback with `@supports` when the feature changes layout, interaction, or readability.
5. When styling Filament dashboards, panels, widgets, custom pages, stats cards, or admin dashboard layouts, also activate `uncodixfy` and follow its anti-generic dashboard rules.
6. Verify the result across desktop and mobile dimensions. Check text wrapping, overflow, hit targets, focus states, reduced motion, color contrast, and dark/light mode behavior.

## Visual Standard

- Use existing project tokens before inventing colors, radii, shadows, or typography.
- Prefer calm, functional hierarchy over decorative effects. Use spacing, alignment, type scale, borders, and contrast before shadows or gradients.
- Avoid generic AI UI patterns: oversized radius, glassmorphism, glow-heavy dark SaaS screens, decorative blobs, gradient text, fake metrics, ornamental badges, meaningless hero copy, and card grids used as filler.
- For Filament dashboards specifically, do not create hero sections, fake KPI filler, decorative dashboard copy, floating glass panels, oversized stat cards, random chart placeholders, or generic SaaS control-room layouts.
- Keep components compact but not cramped. Use stable dimensions for controls, grids, toolbars, counters, and fixed-format UI so state changes do not shift layout.
- Use real content and domain-specific labels. Do not add explanatory marketing copy inside app interfaces unless the product already uses that voice.
- Use icons only for clear actions or known controls, with accessible names or tooltips when meaning is not obvious.

## CSS Architecture

- Prefer custom properties for theme values and component-local decisions.
- Use `@layer` to make cascade order explicit for reset, tokens, base, components, utilities, and overrides.
- Use logical properties such as `padding-inline`, `margin-block`, `inset-inline`, and `border-inline` unless physical direction is required.
- Use container queries for component responsiveness. Reserve viewport breakpoints for page-level layout changes.
- Use `:where()` to lower selector specificity for base styles and `:is()` to simplify grouped selectors.
- Use `:has()` for parent/state styling when it removes JavaScript or duplicate classes, but keep selectors narrow.
- Use `@scope` when styling should stay local and the project build/browser target supports it, otherwise follow the local component scoping convention.
- Avoid hard-coded magic sizes. Use `clamp()`, `min()`, `max()`, aspect ratios, grid tracks, and content-aware sizing.

## Modern Features

Read `references/css-modern-features.md` when choosing specific current CSS features, checking fallback patterns, or answering questions about latest CSS capabilities.

Default choices:
- Colors: use `oklch()` for new palettes, `color-mix()` for derived states, `light-dark()` only when the project supports native color-scheme theming.
- Responsive UI: use container queries, dynamic viewport units (`svh`, `lvh`, `dvh`), `clamp()`, `subgrid`, and logical properties.
- Interaction: use `@starting-style`, `transition-behavior: allow-discrete`, popover/top-layer styles, anchor positioning, and view transitions where they simplify real UI behavior.
- Motion: keep transitions 120-220ms for ordinary UI, use scroll-driven animation only when it communicates position or progress, and always respect `prefers-reduced-motion`.
- Text: use `text-wrap: balance` for short headings, `text-wrap: pretty` for readable paragraphs when supported, and avoid viewport-scaled font sizes.

## Accessibility

- Preserve semantic HTML and native controls whenever possible.
- Ensure visible focus states for every interactive element.
- Keep contrast readable in all states, including disabled, hover, active, selected, error, and dark mode.
- Prefer `prefers-color-scheme`, `prefers-reduced-motion`, `forced-colors`, and `prefers-contrast` where they improve real accessibility.
- Use minimum practical hit targets of about 40px for dense desktop UIs and 44px for touch-heavy UIs.
- Do not rely on color alone for status, validation, or destructive actions.

## Tailwind And Frameworks

- If Tailwind is in use, express modern CSS through project-approved utilities and CSS-first configuration. Use arbitrary values sparingly and extract repeated patterns through the project's component system.
- In Laravel, Blade, Livewire, or Filament work, follow existing component conventions and use framework-native APIs before custom JavaScript.
- In React, Vue, or Svelte, keep styling close to the component only when that matches the project; otherwise use the existing design system or stylesheet structure.

## Final Check

Before finishing UI work, scan for:
- Overflowing text, clipped labels, layout shifts, and overlapping content.
- Repeated decorative cards or nested cards that could be plain sections.
- Color palettes dominated by one hue without functional reason.
- Browser support risks for newer features without fallbacks.
- Inconsistent spacing, radius, shadow, icon size, or focus treatment.
