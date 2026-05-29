# Forms And Interactions

Forms and controls must be understandable, keyboard usable, and programmatically connected.

## Labels

Every form control needs a label.

Preferred:

```html
<label for="name">Name</label>
<input id="name" name="name" autocomplete="name">
```

Wrapped label is also valid:

```html
<label>
  <span>Email</span>
  <input name="email" type="email" autocomplete="email">
</label>
```

Placeholders are not labels. They disappear, are often low contrast, and are not always exposed as a robust name.

## Required Fields

Use native `required` when the field is required. Explain required field conventions near the form.

```html
<input id="email" name="email" type="email" required aria-describedby="email-error">
```

## Errors

Errors should be:

- close to the field;
- associated with `aria-describedby`;
- reflected with `aria-invalid="true"`;
- clear about how to fix the problem;
- not communicated by color alone.

```html
<label for="password">Password</label>
<input id="password" name="password" type="password" aria-invalid="true" aria-describedby="password-error">
<p id="password-error">Password must be at least 12 characters.</p>
```

For page-level summaries, link each error to its field.

## Fieldsets

Use `fieldset` and `legend` for grouped controls, especially radios and checkboxes:

```html
<fieldset>
  <legend>Notification preference</legend>
  <label><input type="radio" name="notifications" value="email"> Email</label>
  <label><input type="radio" name="notifications" value="sms"> SMS</label>
</fieldset>
```

## Autocomplete And Input Types

Use accurate `type`, `inputmode`, and `autocomplete` attributes:

- `type="email"`, `autocomplete="email"`;
- `type="tel"`, `autocomplete="tel"`;
- `autocomplete="name"`, `given-name`, `family-name`;
- `autocomplete="current-password"` / `new-password`;
- `inputmode="numeric"` for numeric entry that is not a mathematical number.

Do not use `type="number"` for credit cards, postal codes, OTPs, or IDs that can have leading zeros.

## Buttons

Always set button type unless submit is intended:

```html
<button type="button">Cancel</button>
<button type="submit">Save</button>
```

Disabled buttons may be unreachable to keyboard and screen readers. If users need to understand why an action is unavailable, provide visible explanation.

## Links

Use descriptive link text:

```html
<a href="/invoices/123">View invoice #123</a>
```

Avoid ambiguous duplicates like `Read more` without accessible context. If visually repeated text is required, use `aria-label` or hidden text carefully.

## Keyboard

All interactive UI must be operable with keyboard:

- Tab reaches controls in logical order.
- Enter/Space activate buttons as expected.
- Escape closes dismissible dialogs/popovers when appropriate.
- Arrow keys are implemented only for widgets whose pattern expects them.
- Focus is never trapped accidentally.

## Focus Management

- Preserve visible focus styles.
- Move focus into modals/dialogs on open.
- Return focus to the invoker on close.
- Move focus after destructive/removing actions only when needed to prevent lost focus.
- Do not use positive `tabindex` values.
- Use `tabindex="-1"` for programmatic focus targets.

## Laravel Blade

For state-changing forms, include CSRF:

```blade
<form method="POST" action="{{ route('profile.update') }}">
    @csrf
    @method('PUT')
</form>
```

Connect Laravel validation errors:

```blade
<label for="title">Title</label>
<input
    id="title"
    name="title"
    value="{{ old('title') }}"
    @error('title') aria-invalid="true" aria-describedby="title-error" @enderror
>

@error('title')
    <p id="title-error">{{ $message }}</p>
@enderror
```

Use Blade directives like `@checked`, `@selected`, `@disabled`, `@readonly`, and `@required` to keep attributes correct.

## Livewire

- Avoid replacing focused DOM unless necessary.
- Use `wire:loading.attr="disabled"` carefully and keep status feedback available.
- Use stable keys for repeated interactive elements.
- Announce async completion or errors with visible text or live regions when needed.

## Filament

Prefer Filament field labels, helper text, validation, actions, and table components. When customizing Filament views, do not strip generated labels, headings, button semantics, or focus behavior.
