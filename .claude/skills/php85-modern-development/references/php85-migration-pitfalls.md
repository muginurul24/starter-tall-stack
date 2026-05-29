# PHP 8.5 Migration Pitfalls

Use this before upgrading older PHP code or resolving PHP 8.5 warnings. Confirm details with the official migration guide when changing behavior-sensitive code.

Primary source: `https://www.php.net/manual/en/migration85.php`

## Deprecation Discipline

Treat PHP 8.5 deprecations as code to fix, not warnings to suppress. Do not silence them with broad error reporting changes.

When a deprecation appears:

1. Locate the direct source in app code or vendor code.
2. If app code, rewrite to the recommended modern API.
3. If vendor code, check whether the package has a compatible update.
4. Add a focused regression test when behavior could change.

## Resource To Object Migration

PHP has been moving many extension resources to objects across releases. Do not depend on `is_resource()` checks for extension return values unless the official docs guarantee a resource.

Prefer type-aware checks and extension-specific APIs.

## Type Juggling

Avoid code that relies on implicit casts from malformed strings, arrays, null, false, or objects.

Bad patterns:

```php
$total += $_GET['amount'];

if ($input['enabled'] == true) {
    // ...
}
```

Preferred:

```php
$amount = filter_var($request->input('amount'), FILTER_VALIDATE_INT);

if ($amount === false) {
    throw new InvalidArgumentException('Invalid amount.');
}

$total += $amount;
```

In Laravel, prefer validation rules and typed DTOs before business logic.

## Dynamic Properties

Do not add undeclared properties dynamically. Declare properties, use constructor promotion, use dedicated DTOs, or use arrays only for intentionally dynamic data.

Avoid:

```php
$userData = new stdClass();
$userData->name = $name;
```

Prefer:

```php
final readonly class UserData
{
    public function __construct(
        public string $name,
        public string $email,
    ) {}
}
```

## Serializable, Magic Methods, And Reflection

Legacy serialization and magic behavior can change across PHP versions. Prefer explicit serialization methods, DTO mappers, Laravel casts/resources, and tested hydrators.

When touching magic methods:

- keep signatures compatible with PHP requirements;
- add return types when allowed;
- test serialization/hydration behavior;
- avoid relying on property order or reflection side effects.

## Locale And Formatting

Avoid process-global locale assumptions. For user-facing localized formatting, prefer `intl` APIs, Laravel localization, and explicit locale parameters.

Use `IntlListFormatter` in PHP 8.5 for human-readable localized lists instead of hand-joining with commas and translated conjunctions.

## URL Parsing

`parse_url()` can be ambiguous for user-supplied or standards-sensitive URLs. For PHP 8.5 projects, consider the URI extension when robust RFC 3986 or WHATWG parsing matters.

In Laravel applications:

- continue using `route()` and `url()` for app URLs;
- validate redirect URLs against allowed hosts;
- do not trust user-supplied redirect targets after parsing alone.

## Cloning

Do not use `clone($object, [...])` as a generic mutation shortcut.

Good targets:

- readonly DTOs;
- value objects;
- immutable configuration objects.

Avoid targets:

- Eloquent models;
- query builders;
- service objects;
- objects wrapping resources or open connections;
- objects with hidden identity or lifecycle state.

## Pipe Operator Overuse

The pipe operator is useful for transformations, but it can make debugging harder if abused.

Avoid:

```php
$result = $payload
    |> fn ($value) => $this->mutateDatabase($value)
    |> fn ($value) => dispatch(new Job($value))
    |> fn ($value) => response()->json($value);
```

Prefer explicit steps for side effects:

```php
$record = $this->storePayload($payload);

dispatch(new SyncRecord($record));

return response()->json($record);
```

## Backward Compatibility

Do not introduce PHP 8.5-only syntax in packages or shared code unless `composer.json` requires PHP 8.5+ and deployment targets match.

For this project, PHP 8.5 is available, so PHP 8.5 syntax is allowed. Still check package constraints before editing reusable packages.

## Static Analysis Checklist

- Replace `mixed` with precise types where possible.
- Add array shapes for structured arrays.
- Add generic PHPDoc for collections and iterables.
- Remove impossible `null` branches after validation.
- Replace loose comparisons with strict comparisons.
- Replace string status values with enums where the domain set is closed.
- Test edge cases around empty arrays, nulls, false, and invalid numeric strings.
