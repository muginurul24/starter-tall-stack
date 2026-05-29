# PHP 8.5 Style Rules

Use these rules when writing PHP in this project. They complement framework-specific skills such as Laravel, Filament, Livewire, and Pest.

## Types First

Always add explicit parameter and return types:

```php
public function isAccessible(User $user, ?string $path = null): bool
{
    return $user->can('view', $path);
}
```

Use PHPDoc when native PHP cannot express the type:

```php
/**
 * @param  array{name: string, email: string, role?: string}  $data
 * @return array{id: int, name: string, email: string}
 */
public function normalizeUserData(array $data): array
{
    return [
        'id' => (int) ($data['id'] ?? 0),
        'name' => trim($data['name']),
        'email' => strtolower($data['email']),
    ];
}
```

Avoid redundant PHPDoc when native types are enough.

## Constructor Property Promotion

Use promotion for dependencies and value objects:

```php
final class SendInvoice
{
    public function __construct(
        private readonly InvoiceMailer $mailer,
        private readonly Clock $clock,
    ) {}

    public function handle(Invoice $invoice): void
    {
        $this->mailer->send($invoice, $this->clock->now());
    }
}
```

Do not leave empty zero-parameter constructors.

## Readonly And Immutability

Use `readonly` for DTOs and value objects:

```php
final readonly class AddressData
{
    public function __construct(
        public string $lineOne,
        public string $city,
        public string $postcode,
    ) {}
}
```

Use `clone($object, [...])` for safe immutable updates when the class is designed for it. Avoid cloning Eloquent models and services unless the semantics are explicit.

## Enums

Use enums for closed sets:

```php
enum InvoiceStatus: string
{
    case Draft = 'draft';
    case Sent = 'sent';
    case Paid = 'paid';
    case Void = 'void';
}
```

Use TitleCase enum case names. In Laravel, cast model attributes to enums when appropriate.

## Match Expressions

Use `match` for exact mappings:

```php
$color = match ($status) {
    InvoiceStatus::Draft => 'gray',
    InvoiceStatus::Sent => 'warning',
    InvoiceStatus::Paid => 'success',
    InvoiceStatus::Void => 'danger',
};
```

Prefer no `default` when all enum cases should be handled and static analysis can catch missing cases.

## Null Handling

Use null-safe access for optional relationships:

```php
$companyName = $user->profile?->company?->name;
```

Do not use `?->` to hide data that must exist. Throw a domain exception or validate earlier when absence is invalid.

## Pipe Operator Guidelines

Use pipe for simple transformations:

```php
$normalizedEmail = $email
    |> trim(...)
    |> strtolower(...);
```

Use local variables when they communicate better:

```php
$email = trim($input['email']);
$normalizedEmail = strtolower($email);
```

Avoid pipes that:

- call methods with side effects;
- hide exceptions across many steps;
- pass values through complex closures;
- duplicate Laravel Collection readability.

## Operators And Comparisons

- Use strict comparisons: `===`, `!==`.
- Use null coalescing `??` for default values only when missing/null is acceptable.
- Use null coalescing assignment `??=` for lazy initialization.
- Use spaceship `<=>` for comparator callbacks.
- Avoid loose truthiness for strings, integers, and user input.

## Arrays And Iterables

Use native helpers that state intent:

```php
if (array_is_list($payload)) {
    // list-like array
}

$first = array_first($items);
$last = array_last($items);
$firstKey = array_key_first($items);
$lastKey = array_key_last($items);
```

Use `foreach` for clear imperative loops. Use Collections when the project already uses Laravel collections and the chain stays readable.

## Exceptions And Results

Throw domain-specific exceptions for invalid states that cannot be handled locally.

Use result/value objects when callers must branch on expected outcomes:

```php
final readonly class ImportResult
{
    public function __construct(
        public int $imported,
        public int $skipped,
        /** @var list<string> */
        public array $errors,
    ) {}
}
```

Mark immutable methods that return new values with `#[NoDiscard]` if ignoring the return would be wrong.

## Attributes

Use attributes sparingly and intentionally:

```php
use Override;

#[Override]
public function handle(): void
{
    // ...
}
```

Good uses include framework metadata, static analysis signals, PHPUnit/Pest integration, and language attributes such as `#[NoDiscard]`.

## Laravel-Specific Style

- Use form requests for validation in controllers.
- Use policies for authorization.
- Use resources for API serialization.
- Use queued jobs for slow side effects.
- Use casts for dates, enums, arrays, encrypted fields, and value conversions.
- Avoid doing database work in accessors that are rendered per row.
- Under Octane, avoid request-specific state in singletons and static properties.

## Comments

Prefer expressive names and types over comments. Add PHPDoc for types and contracts. Add inline comments only for non-obvious algorithmic or framework constraints.
