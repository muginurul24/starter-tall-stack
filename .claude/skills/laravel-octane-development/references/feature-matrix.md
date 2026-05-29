# Octane Feature Matrix

Octane supports multiple servers. Do not assume every Octane feature works on every server.

## FrankenPHP

Use for this project when installed via:

```bash
composer require laravel/octane
php artisan octane:install --server=frankenphp
```

Supported focus:

- long-running Laravel workers;
- fast request handling;
- FrankenPHP/Caddy features;
- Octane start/reload/status/stop operations;
- worker count and max request recycling;
- Docker/Sail integration;
- custom Caddyfile support.

Do not use Swoole-only Octane APIs on FrankenPHP.

## Swoole / Open Swoole Only

Laravel docs mark these features as requiring Swoole:

- `Octane::concurrently()` concurrent tasks;
- ticks and intervals;
- Octane cache driver;
- Octane tables.

If the app uses FrankenPHP, replace these with:

- queues/jobs for background or concurrent work;
- scheduler for repeated tasks;
- Redis/database/cache backends for shared state;
- regular Laravel cache stores;
- external services for distributed coordination.

## RoadRunner

RoadRunner is another long-running worker server. The same state-leak rules apply, but FrankenPHP/Caddy-specific configuration does not.

## Concurrent Work Alternatives

For FrankenPHP deployments:

- Use queues for background work.
- Use Laravel HTTP client pools for outbound HTTP concurrency when appropriate.
- Use database/cache locks for coordination.
- Use scheduler/queues for periodic jobs instead of Octane ticks.

## Shared State Alternatives

Avoid relying on process memory for cross-worker state. Use:

- Redis;
- database;
- Laravel cache stores;
- queue backends;
- external pub/sub systems;
- Mercure when the use case is real-time browser updates and FrankenPHP/Mercure is configured.

## Decision Rule

Before using an Octane facade feature, confirm the active server supports it. If docs say it requires Swoole and the project uses FrankenPHP, do not use it.
