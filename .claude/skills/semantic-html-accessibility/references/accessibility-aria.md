# Accessibility And ARIA

ARIA changes the accessibility tree; it does not add behavior, keyboard support, styling, validation, or focus management by itself.

## First Rule Of ARIA

Use native HTML whenever it provides the semantics and behavior you need.

Prefer:

```html
<button type="button">Save</button>
```

Avoid:

```html
<div role="button" tabindex="0">Save</div>
```

Only use ARIA for custom widgets or states that native HTML cannot express.

## Accessible Names

Every interactive control needs an accessible name.

Sources include:

- visible text content;
- `<label>` for form controls;
- `aria-label` for short invisible names;
- `aria-labelledby` when another visible element names the control;
- `alt` on meaningful images;
- `title` only as a last resort, not a primary labeling strategy.

Prefer visible labels. Use `aria-label` mainly for icon-only controls:

```html
<button type="button" aria-label="Close dialog">
  <svg aria-hidden="true" focusable="false">...</svg>
</button>
```

## Descriptions

Use `aria-describedby` to connect helper text or errors:

```html
<label for="email">Email</label>
<input id="email" name="email" type="email" aria-describedby="email-help email-error" aria-invalid="true">
<p id="email-help">Use your work email.</p>
<p id="email-error">Enter a valid email address.</p>
```

Set `aria-invalid="true"` only when the field is currently invalid.

## Roles

Do not add redundant roles that duplicate native semantics:

```html
<!-- Avoid -->
<button role="button">Save</button>
```

Use roles only when necessary for custom widgets, and implement the full expected keyboard and state behavior from WAI-ARIA APG.

## States And Properties

Common safe states:

- `aria-expanded` for disclosure/menu buttons.
- `aria-controls` when a control toggles a specific region.
- `aria-current="page"` for current nav links.
- `aria-selected` for selected options/tabs where the widget pattern expects it.
- `aria-pressed` for toggle buttons.
- `aria-disabled` only when custom semantics need disabled state; native `disabled` is preferred for form controls/buttons.

Ensure ARIA state always matches visual and behavioral state.

## Dialogs And Modals

Native `<dialog>` can be used when project support and behavior are acceptable. Otherwise, custom dialogs need:

- `role="dialog"` or `role="alertdialog"`;
- `aria-modal="true"`;
- `aria-labelledby` referencing the visible title;
- focus moved into the dialog when opened;
- focus trapped while modal is open;
- Escape closes when appropriate;
- focus returned to the invoker on close;
- background content inert/unreachable.

## Live Regions

Use live regions for async updates that need announcement:

```html
<div role="status" aria-live="polite"></div>
```

Use `polite` for non-urgent status messages. Use assertive/alert sparingly for urgent errors.

Do not use live regions for every minor UI change.

## Icon-Only UI

- Hide decorative SVGs with `aria-hidden="true" focusable="false"`.
- Give icon-only controls an accessible name.
- Do not rely on tooltip text as the only accessible name unless it is programmatically connected and available to keyboard users.

## `aria-hidden`

Use `aria-hidden="true"` only for content that should be ignored by assistive technology.

Do not apply `aria-hidden="true"` to:

- focusable elements;
- ancestors of focused elements;
- meaningful text;
- active dialog content.

## Custom Widgets

If you build custom tabs, menus, comboboxes, treeviews, accordions, or sliders, follow WAI-ARIA Authoring Practices for:

- required roles;
- required aria states/properties;
- keyboard interaction;
- focus management;
- orientation;
- disabled state.

Prefer native controls or framework components that already implement the pattern.
