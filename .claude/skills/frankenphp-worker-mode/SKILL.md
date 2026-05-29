---
name: frankenphp-worker-mode
description: Use when configuring, deploying, debugging, reviewing, or writing PHP/Laravel code that runs under FrankenPHP worker mode, including Caddyfile configuration, Docker images, Laravel Octane FrankenPHP server setup, long-running workers, memory leaks, request state leakage, worker reloads, static assets, Early Hints, Mercure, file watchers, production process management, and server tuning. Trigger for any task mentioning FrankenPHP, worker mode, octane:frankenphp, Caddyfile, php_server, php_worker, frankenphp config, FrankenPHP Docker, worker count, max requests, graceful reload, long-running PHP, or Octane with server=frankenphp.
---

# FrankenPHP Worker Mode

Treat FrankenPHP worker mode as a long-running PHP runtime. PHP files are not executed from a blank process per request; workers keep the app, code, objects, static properties, and some extension state alive across many requests.

## Required Workflow

1. Inspect the runtime path first: direct FrankenPHP config, Laravel Octane, Docker, Sail, Caddyfile, Supervisor/systemd, environment variables, and `config/octane.php`.
2. If this is a Laravel project using Octane, also activate `laravel-octane-development`, `laravel-best-practices`, and `php85-modern-development`.
3. Read `references/worker-mode.md` before writing worker-mode code or config.
4. Read `references/laravel-octane-frankenphp.md` before changing `octane:start`, `octane:frankenphp`, Docker, Sail, Caddyfile, or deployment scripts.
5. Read `references/production-checklist.md` before changing production config, process management, proxying, HTTPS, static files, logs, reloads, worker count, or max requests.
6. Verify changes by restarting/reloading workers when required; ordinary browser refreshes do not reload app code in worker mode.

## Core Rules

- Never assume request isolation. Avoid request-specific state in static properties, global variables, singletons, memoized closures, cached service fields, or long-lived objects.
- Do not inject request, container, config repository, auth user, tenant, locale, or per-request data into singleton constructors.
- Prefer resolver closures, scoped bindings, method parameters, or framework helpers that resolve current request state at call time.
- Reset or avoid third-party libraries that hold per-request state globally.
- Keep worker bootstrap code idempotent. Code that runs at worker boot may run once per worker, not once per request.
- Plan reloads after deployment, `.env` changes, config changes, route changes, translations, views, or code changes.
- Use low `--max-requests` during debugging if leaks are suspected; tune upward only after observing memory behavior.

## FrankenPHP-Specific Guidance

- Use FrankenPHP's Laravel/Octane integration for Laravel apps unless there is a clear reason to write a custom worker script.
- Use Caddyfile configuration when you need advanced routing, static assets, compression, HTTPS, Mercure, or Caddy directives beyond Octane defaults.
- In Docker, prefer official `dunglas/frankenphp` images or a project-specific image that installs required PHP extensions.
- Ensure `pcntl` is installed when Laravel Octane's FrankenPHP Docker examples require it.
- Treat logs and stdout/stderr as production observability surfaces; if Octane passes `--log-level`, FrankenPHP may emit structured JSON logs.

## Validation

- For config changes, run command help first: `php artisan octane:start --help` and `php artisan octane:frankenphp --help` if available.
- Check status with `php artisan octane:status` where applicable.
- Reload with `php artisan octane:reload` after deployments or code/config changes.
- Run focused tests for code changes and monitor memory under repeated requests when touching singleton/static/cache-heavy code.

## Sources

- FrankenPHP docs: https://frankenphp.dev/docs/
- FrankenPHP worker mode: https://frankenphp.dev/docs/worker/
- FrankenPHP Laravel docs: https://frankenphp.dev/docs/laravel/
- Laravel Octane 13.x: https://laravel.com/docs/13.x/octane
