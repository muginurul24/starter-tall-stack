# Octane-Safe Laravel Code

Octane boots Laravel once and reuses the application across requests. First-party Laravel state is reset where Octane knows how, but your app's globals, statics, singletons, and third-party state need deliberate design.

## Service Providers

- `register()` and `boot()` run once when each worker boots.
- Do not put per-request setup in provider boot methods.
- Keep listener, macro, route, and singleton registration idempotent.
- Avoid appending to global registries repeatedly if a provider can be booted more than once in development/reloads.

## Container Bindings

Use transient `bind()` for services that may hold request-specific data.

Use `scoped()` for one instance per request/job lifecycle:

```php
$this->app->scoped(CurrentTenant::class, function (): CurrentTenant {
    return CurrentTenant::fromRequest(request());
});
```

Use `singleton()` only for stateless services or services with explicit resolver closures for current state:

```php
$this->app->singleton(UrlSigner::class, function (): UrlSigner {
    return new UrlSigner(
        configResolver: fn (string $key): mixed => config($key),
    );
});
```

## Request Injection

Do not inject `Illuminate\Http\Request` into singleton constructors. It can hold stale request data on later requests.

Safe:

- type-hint `Request` in controller methods and route closures;
- call `request()` inside a method at runtime;
- pass specific values into service methods.

## Container And Config Injection

Avoid injecting the application container or config repository into singleton constructors. If unavoidable, inject a resolver closure:

```php
final class FeatureDecider
{
    /** @param callable(string): mixed $config */
    public function __construct(private mixed $config) {}

    public function enabled(string $feature): bool
    {
        return (bool) ($this->config)("features.{$feature}");
    }
}
```

## Static State

Avoid mutable static state. Never append request data to static arrays:

```php
final class BadCollector
{
    /** @var list<string> */
    public static array $items = [];
}
```

If static cache is truly needed, make sure it is independent of request/user/tenant/locale and has bounded memory.

## Auth, Tenant, Locale, Timezone

Do not store these in static/singleton properties:

- current user;
- current tenant;
- locale;
- timezone;
- permissions for the current request;
- request URL/input/headers.

Resolve them per request and pass them explicitly.

## Database And Transactions

- Do not keep open transactions across request boundaries.
- Do not store query builders with request-specific constraints in singleton properties.
- Be careful with model instances cached in services; they can become stale and leak user/tenant data.

## Third-Party Libraries

Audit libraries that use static/global state for:

- current user or tenant;
- locale/timezone;
- global options;
- event listeners;
- in-memory caches.

Reset state after each request if the library supports it, or wrap usage in scoped services.

## Memory Leak Checklist

- Static arrays growing over time.
- Singleton properties accumulating request data.
- Event listeners registered per request.
- Closures retaining request/model graphs.
- Large result sets cached in long-lived objects.
- Debug collectors enabled in production.
- Files, streams, sockets, or image handles not closed.

## Testing Strategy

Normal feature tests may not catch worker leaks. Add targeted tests for service behavior, then manually or with smoke tests issue repeated requests under Octane and observe memory/state leakage for high-risk changes.
