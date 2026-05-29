# FrankenPHP Worker Mode

Use worker mode to keep PHP applications in memory and serve multiple requests from long-running worker processes. This can be much faster than traditional per-request PHP execution, but it changes the safety model: memory and process state persist.

## Mental Model

- A worker boots PHP code once, then handles many requests.
- Global variables, static properties, singletons, loaded classes, opcache, and some extension state can survive between requests.
- Request input, auth state, tenant state, locale, feature flags, and other per-request data must not be stored in long-lived process state.
- Code changes usually require a worker reload, not a browser refresh.

## Worker-Safe Code

Prefer:

- immutable services;
- stateless services;
- method parameters for request-specific data;
- scoped/request bindings in frameworks that support them;
- resolver closures for current request/config/container state;
- explicit cleanup after temporary state;
- idempotent bootstrapping.

Avoid:

- appending to static arrays per request;
- storing `Request`, authenticated user, tenant, locale, or route state on services;
- mutable singleton properties that change during requests;
- global registries that accumulate data;
- using singleton services as request caches;
- long-lived database transaction state;
- lazy static caches that depend on current request or tenant.

## Bad Pattern

```php
final class CurrentTenant
{
    public static ?Tenant $tenant = null;
}

public function __invoke(Request $request): Response
{
    CurrentTenant::$tenant = Tenant::fromRequest($request);

    return $this->next->handle($request);
}
```

That can leak tenant state into the next request handled by the same worker.

## Safer Pattern

```php
final class TenantResolver
{
    public function resolve(Request $request): Tenant
    {
        return Tenant::fromRequest($request);
    }
}
```

Pass the current `Request` or tenant to the method that needs it. In Laravel, bind request-scoped state using scoped bindings or resolve it from the current request when needed.

## Custom Worker Scripts

For Laravel apps, prefer Laravel Octane's FrankenPHP integration. Custom FrankenPHP worker scripts are useful for non-Laravel apps or special runtimes.

When writing a custom worker:

- require bootstrap files once;
- handle each request in a loop using FrankenPHP's worker API;
- create a fresh request context each iteration;
- send a response and release per-request references;
- catch exceptions so a bad request does not poison the worker;
- avoid capturing request objects in closures that survive the loop.

## Reloads

Reload workers after:

- application code changes;
- composer autoload changes;
- `.env` or config changes;
- route/view/translation changes that are cached in memory;
- deployment;
- extension/config changes;
- detected memory growth.

During development, use a watcher or low max-request count if repeated manual reloads slow feedback.

## Memory Leak Review

Search for:

- `static` mutable properties;
- `singleton()` bindings;
- global variables;
- event listeners that accumulate;
- arrays appended to during requests;
- caches keyed by request/user/tenant without eviction;
- third-party static registries;
- closures capturing request-heavy objects.

Then test repeated requests and monitor process memory.
