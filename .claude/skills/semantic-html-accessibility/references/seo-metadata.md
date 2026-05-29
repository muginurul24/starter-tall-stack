# SEO And Metadata

Semantic HTML and accessibility usually improve SEO because they make content easier to parse and understand. Do not sacrifice accessibility for SEO tricks.

## Document Basics

Every HTML document should have:

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Specific page title</title>
  <meta name="description" content="Clear summary of the page.">
</head>
<body>
  <main>...</main>
</body>
</html>
```

Use the correct `lang` value for the page. For mixed-language snippets, set `lang` on the specific element.

## Titles

Use unique, specific titles. Put the page-specific topic first when possible.

Good:

```html
<title>Invoice #1234 - Acme Billing</title>
```

Avoid generic titles like `Home`, `Dashboard`, or repeated product-only titles across many pages.

## Meta Descriptions

Descriptions should summarize the page honestly. They are not a ranking magic trick, but they can affect search result snippets and sharing quality.

Use one description per public page when useful. Avoid duplicate descriptions across many pages.

## Headings And Content

- Use a clear `h1` that matches the page purpose.
- Use headings to structure content sections.
- Do not use headings only for visual styling.
- Keep link text and headings meaningful out of context.

## Links

- Use real `href` values for crawlable navigation.
- Avoid using buttons for navigation.
- Avoid JavaScript-only navigation without real links for content that should be discoverable.
- Use descriptive anchor text.
- Use `rel="noopener noreferrer"` for untrusted new-tab links.

## Images

Use useful alt text for meaningful images:

```html
<img src="/products/camera.jpg" alt="Black mirrorless camera with 35mm lens">
```

Decorative images:

```html
<img src="/decorative-divider.svg" alt="">
```

Use dimensions or CSS aspect ratio to reduce layout shift. Use responsive image techniques (`srcset`, `sizes`, `picture`) when they improve performance.

## Canonical And Robots

Use canonical links when duplicate or parameterized URLs can represent the same content:

```html
<link rel="canonical" href="https://example.com/articles/semantic-html">
```

Use robots directives carefully. Do not accidentally `noindex` production pages.

## Open Graph And Social Metadata

For public pages that are shared, include Open Graph metadata when appropriate:

```html
<meta property="og:title" content="Semantic HTML Guide">
<meta property="og:description" content="How to write accessible, SEO-friendly markup.">
<meta property="og:type" content="article">
<meta property="og:url" content="https://example.com/guides/semantic-html">
<meta property="og:image" content="https://example.com/images/semantic-html.png">
```

Keep metadata consistent with visible page content.

## Structured Data

Use JSON-LD structured data only when it accurately represents visible page content and matches search engine guidelines. Do not invent schema data for content that is not present.

Common candidates:

- Article;
- Product;
- BreadcrumbList;
- Organization;
- FAQPage only when FAQs are visible and eligible.

## Performance And Crawlability

- Server-render important content when possible.
- Avoid hiding primary content behind interactions that do not expose real links or markup.
- Use semantic markup before client-side enhancements.
- Keep status/error pages meaningful and correctly coded.

## Laravel Blade

Use layouts/sections/components for metadata so every page can set:

- title;
- description;
- canonical URL when needed;
- Open Graph fields for public pages;
- body landmarks and page heading.

Prefer named routes for canonical links and URLs.
