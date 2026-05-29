---
name: filament-v5-development
description: Use when creating, editing, reviewing, testing, styling, or upgrading Filament 5 code in Laravel projects, including panels, dashboards, resources, pages, widgets, relation managers, schemas, forms, infolists, tables, filters, actions, notifications, imports, exports, plugins, themes, render hooks, and Pest/Livewire tests. Trigger for any task mentioning Filament, filament/filament:^5.0, Panel Builder, Dashboard, Resource, Schema, TextInput, Select, Table, TextColumn, Action, RelationManager, Widget, or Filament namespaces. Enforce Filament 5 APIs, namespaces, security practices, testing patterns, version-specific migration pitfalls, and require uncodixfy for dashboard/panel/widget styling.
---

# Filament v5 Development

Build Filament code against v5 APIs for this project. Prefer official Filament conventions over custom Livewire/Blade/JavaScript, and verify current syntax with project docs when behavior is version-sensitive.

## Required Workflow

1. Inspect the local project first: Filament version, panel providers, existing resources, page structure, model relationships, policies, factories, tests, and naming conventions.
2. Search version-specific docs before changing code. In Laravel Boost projects, use `search-docs` scoped to `filament/filament` with broad topic queries.
3. Use Filament artisan generators for new Filament files when available, with `--no-interaction`. Inspect command options with `php artisan list` or `php artisan make:filament-resource --help`.
4. Follow the v5 namespace map in `references/v5-api-map.md`; do not use older v3/v4 action or schema namespaces.
5. Keep resource code maintainable. For large resources, prefer v5 schema/table classes and small component/action classes over one oversized resource file.
6. For Filament dashboard, panel, widget, custom page, table layout, stats card, or admin UI styling, also activate `uncodixfy` and `modern-css-styling`. If Tailwind classes are involved, also activate `tailwindcss-development`.
7. Add or update Pest tests for resource pages, schemas, tables, actions, notifications, and policies when behavior changes.
8. Run `vendor/bin/pint --dirty --format agent` after PHP edits and run focused tests such as `php artisan test --compact --filter=...`.

## Read References As Needed

- Read `references/v5-api-map.md` before writing imports, resource classes, schemas, tables, actions, or tests.
- Read `references/patterns.md` when building resources, forms, tables, actions, relation managers, widgets, plugins, or panel configuration.
- Read `references/testing-security.md` when writing tests, using file uploads, imports/exports, authorization, notifications, or deployment-sensitive code.
- Read `references/migration-pitfalls.md` when updating older Filament code or fixing namespace/API errors.

## Core Rules

- Use static `make()` methods for Filament components and fluent configuration.
- Use schema APIs consistently: form fields live in `Filament\Forms\Components`, infolist entries in `Filament\Infolists\Components`, layout components in `Filament\Schemas\Components`, and schema utilities in `Filament\Schemas\Components\Utilities`.
- Use the unified `Filament\Actions` namespace for actions. Do not import `Filament\Tables\Actions`, `Filament\Forms\Actions`, or other old action sub-namespaces.
- Use `->schema()` for actions, forms, filters, repeaters, and modal forms in v5 contexts where docs specify schema-based APIs.
- Use `Get $get` and `Set $set` utility injection for reactive schema logic; prefer `->live(onBlur: true)` for text fields that update derived state.
- Use `Select::make('foreign_key')->relationship('relation', 'title')` for `BelongsTo` fields; do not invent legacy components such as `BelongsToSelect`.
- Never add `->dehydrated(false)` to fields that must be saved.
- Do not assume file uploads are public. Explicitly set `->visibility('public')` only when public access is required.
- With `InteractsWithSchemas` on reachable custom Livewire components, add `RestrictsFileUploadsToSchemaComponents` unless arbitrary upload RPC access is acceptable.

## Quality Bar

- Authorization belongs in policies, panel access checks, action visibility/enablement, and query scoping, not only in hidden UI.
- Avoid N+1 queries in tables, option labels, badges, relation columns, and action callbacks. Use eager loading, counts, scoped queries, or cached options as appropriate.
- Keep table pages fast: searchable/sortable columns should map to database-backed values or explicit query logic.
- Keep Filament UI native. Prefer Filament components, layout primitives, actions, notifications, and render hooks before custom Blade/JS.
- Keep Filament dashboards utilitarian, dense, scannable, and non-generic. With `uncodixfy`, avoid decorative hero sections, fake KPI filler, floating glass panels, glow-heavy dark SaaS composition, oversized cards, ornamental badges, and explanatory dashboard copy.
- Use `Heroicon` enum values from `Filament\Support\Icons\Heroicon` for icons.

## Source Links

Primary docs: https://filamentphp.com/docs/5.x

Useful sections: resources, schemas, forms, tables, actions, panel configuration, testing, advanced security, deployment.
