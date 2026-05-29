---
name: laravel-octane-development
description: Use when writing, reviewing, refactoring, configuring, testing, or deploying Laravel code that runs under Laravel Octane, including FrankenPHP, RoadRunner, Swoole, long-running workers, service providers, container bindings, singleton/scoped services, request/config/container injection, static state, memory leaks, worker reloads, max requests, watch mode, config/octane.php, Octane commands, deployment, and Octane-specific features. Trigger for any task mentioning Laravel Octane, laravel/octane, octane:start, octane:reload, octane:frankenphp, workers, max requests, Swoole tables/cache/ticks/concurrently, RoadRunner, FrankenPHP, or long-running Laravel processes.
---

# Laravel Octane Development

Laravel Octane boots the application once, keeps it in memory, and serves many requests from long-running workers. Write Laravel code as if app state can persist across requests unless explicitly reset.

## Required Workflow

1. Inspect installed Octane server and config: `composer.json`, `config/octane.php`, `.env`, Docker/Sail/Supervisor, and current start command.
2. Search version-specific Laravel docs with `search-docs` before changing Octane code/config.
3. If the server is FrankenPHP, also activate `frankenphp-worker-mode`.
4. Read `references/octane-safe-code.md` before changing services, providers, middleware, controllers, auth/tenant code, caches, static properties, or bindings.
5. Read `references/configuration-operations.md` before changing `octane:start`, `octane:reload`, worker count, max requests, watch mode, HTTPS, Docker, Supervisor, or deployment scripts.
6. Read `references/feature-matrix.md` before using `Octane::concurrently`, ticks, intervals, Octane cache, or Octane tables.
7. Run Pint after PHP edits and focused tests for behavior changes. Reload/restart Octane workers after code/config changes.

## Core Rules

- Never store request-specific state in static properties, global variables, singletons, or long-lived service properties.
- Do not inject `Request`, container, config repository, authenticated user, tenant, route, or locale into singleton constructors.
- Use `scoped()` bindings for request-lifetime services. Use resolver closures when a singleton must read current request/config/container state.
- Prefer passing request data into methods at call time.
- Keep service providers idempotent. `register()` and `boot()` run at worker boot, not every request.
- Avoid appending to static arrays or long-lived collections during requests.
- Monitor memory while developing Octane code; lower max requests while debugging suspected leaks.
- Do not use Swoole-only features when the active server is FrankenPHP.

## Safe Binding Patterns

Bad singleton:

```php
$this->app->singleton(Service::class, fn (Application $app) => new Service($app['request']));
```

Better scoped binding:

```php
$this->app->scoped(Service::class, fn (Application $app): Service => new Service($app['request']));
```

Better singleton with resolver:

```php
$this->app->singleton(Service::class, fn (): Service => new Service(fn () => request()));
```

Best when possible: pass the specific request data to the method that needs it.

## Verification

- Use `php artisan octane:status` to inspect server status where applicable.
- Use `php artisan octane:reload` after deployments or code/config changes.
- Use `php artisan octane:start --watch` in development when file watching is configured.
- Use repeated requests and memory observation when changing long-lived services.

## Sources

- Laravel Octane 13.x: https://laravel.com/docs/13.x/octane
- FrankenPHP docs: https://frankenphp.dev/docs/
