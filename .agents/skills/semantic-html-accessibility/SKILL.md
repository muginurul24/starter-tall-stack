---
name: semantic-html-accessibility
description: Use when creating, editing, reviewing, or refactoring HTML, Blade, Livewire, Filament views, JSX, Vue, Svelte, forms, navigation, layouts, dashboards, modals, dialogs, tabs, accordions, tables, cards, images, icons, links, buttons, metadata, SEO markup, or any frontend markup. Enforce HTML5 semantic structure, native element choice before ARIA, accessible names/descriptions, keyboard and focus behavior, WCAG 2.2 AA practices, SEO-friendly document structure, valid landmarks/headings, form labels/errors, image alt text, and safe aria-* usage.
---

# Semantic HTML Accessibility

Write markup that browsers, assistive technologies, search engines, and humans can understand. Prefer native semantic HTML over ARIA and JavaScript behavior whenever possible.

## Required Workflow

1. Inspect existing markup/component conventions first: Blade components, Livewire patterns, Filament components, IDs, form error patterns, heading hierarchy, landmarks, and design system components.
2. Choose the correct native HTML element before adding ARIA. Buttons are for actions; links are for navigation; headings structure content; lists are for lists; tables are for tabular data.
3. Use ARIA only to add missing semantics or state when native HTML cannot express the UI. Never use ARIA to override broken semantics that can be fixed with the right element.
4. Verify accessible names, keyboard access, focus order, visible focus states, color-independent meaning, error messaging, and reduced motion where relevant.
5. Keep SEO and accessibility aligned: one clear page topic, meaningful headings, descriptive links, useful alt text, canonical/metadata when appropriate, and valid document structure.

## Read References As Needed

- Read `references/semantic-structure.md` before writing page layouts, landmarks, headings, navigation, articles, sections, tables, or dashboard markup.
- Read `references/accessibility-aria.md` before using `aria-*`, roles, dialogs, menus, tabs, accordions, live regions, hidden content, or icon-only controls.
- Read `references/forms-interactions.md` before writing forms, validation errors, buttons, links, modals, keyboard interaction, or focus management.
- Read `references/seo-metadata.md` before writing page metadata, headings, links, images, structured content, or public-facing pages.

## Core Rules

- Use landmarks intentionally: `header`, `nav`, `main`, `aside`, `footer`. Each page should have one primary `main` landmark.
- Use a logical heading outline. Do not skip heading levels for visual size; style headings with CSS instead.
- Do not use `<div>` or `<span>` as buttons, links, headings, or form controls when native elements exist.
- Every interactive control must be keyboard reachable, have a visible focus state, and have an accessible name.
- Every form input needs a programmatic label. Use `for`/`id`, wrapped labels, `aria-labelledby`, or framework-native label APIs.
- Associate validation messages with inputs using `aria-describedby`; set `aria-invalid="true"` only when invalid.
- Use `alt` text that communicates image purpose. Decorative images get `alt=""` and should not add noise.
- Use descriptive link text. Avoid `click here`, duplicate ambiguous links, or icon-only links without names.
- Use tables only for tabular data and include headers. Do not use tables for layout.
- Keep IDs unique and stable, especially when connecting labels, descriptions, controls, and panels.

## Laravel / Filament / Livewire

- In Blade forms, include `@csrf` for state-changing forms and connect `@error` output to the input with `aria-describedby`.
- In Livewire, preserve focus behavior after updates and avoid replacing focused DOM unnecessarily.
- In Filament, prefer native Filament form/table/action/schema components because they already provide much accessibility behavior. Customize labels, helper text, placeholders, and actions without breaking generated semantics.
- In Tailwind, do not remove outlines without replacing them with visible focus styles.

## Final Check

Before finishing markup work, scan for:

- missing labels or duplicate IDs;
- fake buttons/links built from non-interactive elements;
- skipped heading levels;
- multiple `main` landmarks;
- ARIA roles that duplicate or contradict native semantics;
- icon-only controls without accessible names;
- validation errors not associated with inputs;
- hidden content still reachable by keyboard;
- links/images/headings that are vague for SEO or screen reader users.

## Sources

- MDN HTML: https://developer.mozilla.org/en-US/docs/Web/HTML
- MDN ARIA: https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA
- WAI ARIA Authoring Practices: https://www.w3.org/WAI/ARIA/apg/
- WCAG 2.2 Quick Reference: https://www.w3.org/WAI/WCAG22/quickref/
- Google Search Central SEO Starter Guide: https://developers.google.com/search/docs/fundamentals/seo-starter-guide
