# Octane Configuration And Operations

Use this when editing `config/octane.php`, commands, Docker/Sail, process management, or deployment docs/scripts.

## Start Commands

Default:

```bash
php artisan octane:start
```

FrankenPHP:

```bash
php artisan octane:start --server=frankenphp
```

Common options:

```bash
php artisan octane:start --server=frankenphp --host=127.0.0.1 --port=8000
php artisan octane:start --workers=4
php artisan octane:start --max-requests=250
php artisan octane:start --watch
```

Confirm exact installed options with:

```bash
php artisan octane:start --help
```

## Worker Count

By default, Octane starts one request worker per CPU core. Tune `--workers` based on:

- CPU cores;
- memory per worker;
- blocking I/O behavior;
- database capacity;
- queue/background workload;
- benchmark results.

Do not blindly increase workers. More workers can increase DB pressure and memory use.

## Max Requests

Octane gracefully restarts workers after a max request count to limit leak impact. Laravel docs mention a default of 500 requests. Use `--max-requests` to adjust:

```bash
php artisan octane:start --max-requests=250
```

Use lower values while diagnosing memory leaks; tune higher only after observing stable memory.

## Max Execution Time

`config/octane.php` has `max_execution_time`, defaulting to 30 seconds in Laravel docs. Restart Octane after changing it.

Use queues for long tasks when possible instead of increasing request execution time.

## Watch Mode

`--watch` restarts Octane on file changes. It requires Node and Chokidar:

```bash
npm install --save-dev chokidar
php artisan octane:start --watch
```

Configure watched files in `config/octane.php`.

## Reloads

Use after deployment or code/config changes:

```bash
php artisan octane:reload
```

Use status/stop when needed:

```bash
php artisan octane:status
php artisan octane:stop
```

## HTTPS

When Octane generates URLs while serving HTTPS, set:

```php
'https' => env('OCTANE_HTTPS', false),
```

and configure `OCTANE_HTTPS=true` for HTTPS deployments.

## Process Management

Production Octane must be supervised by Supervisor, systemd, Docker, Kubernetes, or another process manager.

Supervisor command shape:

```ini
command=php /path/to/app/artisan octane:start --server=frankenphp --host=127.0.0.1 --port=8000
autostart=true
autorestart=true
redirect_stderr=true
stdout_logfile=/path/to/app/storage/logs/octane.log
stopwaitsecs=3600
```

## Reverse Proxy

If serving behind Nginx/Apache/CDN:

- serve static assets at the proxy/CDN layer when appropriate;
- preserve `Host`, scheme, remote address, and `X-Forwarded-*` headers;
- configure Laravel trusted proxies;
- support WebSocket/upgrade headers if the app needs them.

## Deployment Checklist

1. Put app in maintenance mode if required.
2. Pull/build new code.
3. Install dependencies.
4. Build assets.
5. Run migrations carefully.
6. Build Laravel caches intentionally.
7. Reload Octane workers.
8. Check status/logs.
9. Smoke-test routes.
