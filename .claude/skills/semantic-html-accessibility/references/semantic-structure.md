# Semantic Structure

Use semantic elements to expose document meaning to browsers, assistive technologies, search engines, and maintainers.

## Landmarks

Use landmarks for major page regions:

- `header`: introductory content for the page or a section.
- `nav`: major navigation groups.
- `main`: the primary content of the page. Use one primary `main` per page.
- `aside`: complementary content that can stand apart from the main content.
- `footer`: footer content for the page or a section.
- `section`: a thematic grouping that normally has a heading.
- `article`: self-contained content that can stand alone.

Avoid wrapping everything in generic `div`s when a semantic element communicates structure. Avoid using landmarks for purely decorative layout wrappers.

## Headings

- Use headings to describe content structure, not visual size.
- Use one clear `h1` for the page or view title in standard pages.
- Do not skip heading levels for styling. Use CSS classes to adjust size.
- Component headings should fit into the surrounding hierarchy.
- Do not use heading tags for labels, badges, or decorative text.

Good:

```html
<main>
  <h1>Invoices</h1>

  <section aria-labelledby="overdue-heading">
    <h2 id="overdue-heading">Overdue invoices</h2>
  </section>
</main>
```

## Navigation

Use `nav` for major navigation groups and label multiple nav regions:

```html
<nav aria-label="Primary">
  <ul>
    <li><a href="/dashboard">Dashboard</a></li>
    <li><a href="/invoices">Invoices</a></li>
  </ul>
</nav>
```

Use `aria-current="page"` on the current navigation link.

## Buttons And Links

- Use `<a href="...">` for navigation.
- Use `<button type="button">` for actions.
- Use `type="submit"` only for form submission.
- Do not use `div`, `span`, or `a href="#"` as fake buttons.

## Lists

Use lists for grouped items where order or grouping matters:

- `ul` for unordered lists;
- `ol` for ordered steps/rankings;
- `dl` for name/value or term/description data.

Do not remove list semantics for navigation just to simplify styling.

## Tables

Use tables for tabular data, not layout.

Minimum table structure:

```html
<table>
  <caption>Monthly invoice totals</caption>
  <thead>
    <tr>
      <th scope="col">Month</th>
      <th scope="col">Total</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th scope="row">January</th>
      <td>$12,450</td>
    </tr>
  </tbody>
</table>
```

Use `scope` for simple tables. Use `headers`/`id` for complex tables only when necessary.

## Cards And Dashboards

Cards are usually articles, list items, sections, or plain containers depending on meaning:

- Use `article` when the card is self-contained and can stand alone.
- Use `li` inside a list when cards are repeated search results or items.
- Use `section` with a heading when it is a page section.
- Use `div` when it is only a visual wrapper and semantics come from children.

Do not make every dashboard tile an `article` by default. Match semantics to content.

## Hidden Content

- Use `hidden` for content that should be removed from accessibility tree and layout.
- Use CSS visually-hidden utilities for text that should be available to screen readers but not visible.
- Do not hide focusable content with `aria-hidden="true"`.
- When hiding modals/dropdowns, ensure hidden controls are not reachable by keyboard.

## IDs

IDs must be unique and stable. This is critical for:

- `label for`;
- `aria-labelledby`;
- `aria-describedby`;
- tab/panel relationships;
- error messages;
- headings referenced by landmarks/sections.
