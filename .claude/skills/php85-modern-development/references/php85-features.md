# PHP 8.5 Feature Reference

Use this file when deciding whether and how to use PHP 8.5-only features. Verify edge cases with official docs when production risk is high.

Primary sources:

- `https://www.php.net/releases/8.5/en.php`
- `https://www.php.net/manual/en/migration85.php`

## Pipe Operator

Use the pipe operator `|>` for readable left-to-right transformations. It passes the left-hand value into the right-hand callable or callable expression.

Prefer it when each step is named and conceptually transforms one value:

```php
$slug = $title
    |> trim(...)
    |> strtolower(...)
    |> fn (string $value): string => str_replace(' ', '-', $value);
```

Use a named private method or service method for complex steps. Avoid deep inline closures or pipelines with hidden side effects.

Good use cases:

- normalizing strings;
- transforming DTO payloads;
- composing pure data transformations;
- making nested function calls readable.

Avoid pipe when:

- each step mutates shared state;
- debugging intermediate values would be difficult;
- Laravel Collections already express the transformation more clearly;
- a normal variable assignment is simpler.

## URI Extension

PHP 8.5 adds the URI extension with standards-compliant URI and URL parsing based on RFC 3986 and WHATWG URL behavior.

Use it for robust URL/URI parsing when native `parse_url()` ambiguity matters, especially with user-supplied URLs. In Laravel apps, still use `route()`, `url()`, signed URLs, request helpers, and validation rules for framework concerns.

Prefer framework APIs for:

- generating application URLs;
- signed routes;
- redirect targets;
- validating request input.

Consider URI extension APIs for:

- parsing arbitrary external URLs;
- normalizing imported URL data;
- separating URI validation from application routing.

## Clone With

PHP 8.5 supports cloning an object while changing selected properties using `clone($object, [...])`.

Use it for immutable or value-object style updates:

```php
$updated = clone($address, [
    'lineOne' => '123 Main Street',
    'postcode' => '90210',
]);
```

Use this only when the object model supports cloning safely. Avoid it for Eloquent models, services, database connections, jobs with runtime state, or objects whose clone semantics are not obvious.

## `#[NoDiscard]`

Use `#[NoDiscard]` for functions and methods where ignoring the return value is probably a bug.

Good candidates:

- immutable `with...()` methods;
- validation or parsing methods returning a result object;
- methods returning a new DTO/value object;
- operations that do not mutate the receiver.

Example:

```php
use NoDiscard;

final readonly class Money
{
    public function __construct(public int $cents) {}

    #[NoDiscard]
    public function plus(self $other): self
    {
        return new self($this->cents + $other->cents);
    }
}
```

Do not add `#[NoDiscard]` to fluent mutating APIs where ignoring the return is harmless or where the framework expects fluent chains.

## Closures And First-Class Callables In Constant Expressions

PHP 8.5 allows closures and first-class callables in constant expressions.

Use this for static configuration that benefits from a callable value and does not require runtime container state.

Avoid capturing request-specific state, service container instances, database connections, or mutable globals in constant-expression callables.

## `array_first()` And `array_last()`

PHP 8.5 adds direct helpers to retrieve the first and last array values.

Use them when you need values, not keys:

```php
$firstUser = array_first($users);
$lastUser = array_last($users);
```

Use `array_key_first()` and `array_key_last()` when you need keys. In Laravel Collections, use collection methods when already working with a collection.

## IntlListFormatter

Use `IntlListFormatter` when formatting human-readable lists for localized UI text.

```php
$formatter = new IntlListFormatter('en', IntlListFormatter::TYPE_AND, IntlListFormatter::WIDTH_WIDE);

$label = $formatter->format(['Alice', 'Bob', 'Charlie']);
```

Prefer Laravel translation files for application text and use this for locale-aware list joining.

## cURL `CURLOPT_SERVER_RESPONSE_TIMEOUT`

PHP 8.5 exposes a cURL option for server response timeout. In Laravel apps, prefer Laravel HTTP client timeout APIs unless raw cURL is already necessary.

## New Array Behavior Utilities

Use exact native helpers instead of indirect patterns:

- `array_is_list()` for list detection;
- `array_first()`/`array_last()` for first/last values;
- `array_key_first()`/`array_key_last()` for keys.

## Attribute Improvements

Use attributes for metadata consumed by frameworks, static analysis, test runners, routing, dependency injection, or explicit language behavior. Keep domain logic in code, not attributes.

Useful attribute families in modern PHP:

- `#[Override]` for intentional overrides where supported by the target PHP version;
- `#[Deprecated]` to signal API deprecation;
- `#[NoDiscard]` in PHP 8.5 for ignored return-value bugs.

## Best Feature Selection Rule

Use a PHP 8.5 feature when it makes the code:

- more explicit;
- easier for static analysis;
- safer against bugs;
- easier to read left-to-right;
- closer to the project's existing conventions.

Do not use a feature just because it is new.
