---
name: php85-modern-development
description: Use when writing, reviewing, refactoring, or upgrading PHP code for PHP 8.5+ projects, including Laravel, Filament, Livewire, Pest tests, service classes, actions, DTOs, enums, value objects, controllers, jobs, commands, middleware, policies, migrations, factories, and package code. Trigger for any task mentioning PHP 8.5, modern PHP, latest PHP features, pipe operator, NoDiscard, clone with, URI extension, attributes, enums, typed constants, readonly, constructor property promotion, match expressions, null-safe operator, or PHP best practices. Enforce current PHP 8.5 syntax, strict typing, readable modern idioms, safe operators, deprecation avoidance, and Laravel-compatible style.
---

# PHP 8.5 Modern Development

Write PHP as PHP 8.5 code, not legacy PHP. Prefer clear native language features, explicit types, immutable/value-oriented design where useful, and framework conventions where the project already provides them.

## Required Workflow

1. Inspect the project first: PHP version, `composer.json`, framework conventions, Pint style, Larastan/PHPStan level, tests, and sibling files.
2. Use official documentation when a feature is version-sensitive. Primary sources: `https://www.php.net/releases/8.5/en.php` and `https://www.php.net/manual/en/migration85.php`.
3. Use PHP 8.5 features when they improve clarity or safety. Do not force new syntax when an established local Laravel/Filament pattern is clearer.
4. Keep public APIs explicit: typed parameters, typed returns, typed properties, enum types, array-shape PHPDoc for structured arrays, and generics in PHPDoc when native PHP has no generic syntax.
5. Avoid PHP 8.5 deprecations and migration traps. Read `references/php85-migration-pitfalls.md` before touching older code or code that relies on casts, locales, resources, or magic behavior.
6. After PHP edits in Laravel projects, run `vendor/bin/pint --dirty --format agent`, then focused tests such as `php artisan test --compact --filter=...`.

## Read References As Needed

- Read `references/php85-features.md` before using or explaining PHP 8.5-only features.
- Read `references/php85-style-rules.md` before writing substantial PHP code, DTOs, services, enums, tests, or refactors.
- Read `references/php85-migration-pitfalls.md` before upgrading legacy code or fixing warnings/deprecations.

## Modern PHP Defaults

- Use `declare(strict_types=1);` in new standalone PHP files when the project convention permits it. Do not add it inconsistently to Laravel files if siblings do not use it.
- Use constructor property promotion for dependency injection and simple value objects.
- Use `readonly` classes/properties for immutable DTOs and value objects when mutation is not part of the model.
- Use enums for closed sets of domain states, not string constants scattered across code.
- Use `match` for exhaustive value mapping and avoid fall-through `switch` unless intentional.
- Use null-safe access `?->` for optional object chains, but do not hide missing required data.
- Use first-class callables and closures where they improve local readability.
- Use `array_is_list()`, `str_contains()`, `str_starts_with()`, `str_ends_with()`, `array_key_first()`, `array_key_last()`, and PHP 8.5 `array_first()`/`array_last()` where they express intent directly.
- Use pipe operator `|>` for readable left-to-right transformations when every step is simple and named. Avoid long anonymous callback chains that become harder to debug.
- Use `clone($object, [...])` for safe immutable updates when working with cloneable value objects.
- Use `#[NoDiscard]` on project functions/methods whose return value must be used when ignoring it would likely be a bug.

## Laravel Project Rules

- Follow Laravel conventions over abstract PHP purity: form requests for validation, policies for authorization, resources for API transformation, jobs for queued work, collections where they are clearer than raw array loops.
- Use dependency injection and container bindings carefully under Octane. Avoid request/config/container objects captured in long-lived singletons.
- Keep Eloquent queries explicit and testable. Avoid hiding complex query behavior inside accessors that run per row.
- Use PHPDoc array shapes for request data, DTO payloads, config arrays, event payloads, and complex return arrays.
- Prefer Pest tests for behavior and regression coverage.

## Quality Gate

- New or changed methods must have explicit parameter and return types.
- Prefer native types over redundant PHPDoc. Add PHPDoc only for generics, array shapes, callables, templates, complex iterable contents, or non-obvious domain contracts.
- Avoid dynamic properties, loose comparisons, stringly-typed state, unchecked array offsets, untyped mixed data, and silent type juggling.
- Do not introduce deprecated PHP 8.5 behavior.
- Keep code easy for static analysis: no unnecessary magic, hidden side effects, or overly broad `mixed`.
