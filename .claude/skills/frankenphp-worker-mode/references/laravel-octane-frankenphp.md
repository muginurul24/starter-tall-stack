# Laravel Octane With FrankenPHP

This project has `laravel/octane` installed and was installed with `php artisan octane:install --server=frankenphp`. Use Laravel Octane as the primary integration layer for FrankenPHP.

## Common Commands

Start Octane with FrankenPHP:

```bash
php artisan octane:start --server=frankenphp
```

Common development options:

```bash
php artisan octane:start --server=frankenphp --watch
php artisan octane:start --server=frankenphp --workers=1 --max-requests=1
```

Common production-style options:

```bash
php artisan octane:start --server=frankenphp --host=127.0.0.1 --port=8000 --workers=4 --max-requests=500
```

Operational commands:

```bash
php artisan octane:status
php artisan octane:reload
php artisan octane:stop
```

Use `php artisan octane:start --help` and `php artisan octane:frankenphp --help` to confirm exact options for the installed version.

## Octane Behavior

- Laravel boots once per worker.
- Service provider `register()` and `boot()` methods run when the worker boots, not on every request.
- First-party Laravel state is reset where Octane knows how to reset it.
- App-created global/static/singleton state is the developer's responsibility.
- Code/config changes require worker reload unless using a watcher.

## FrankenPHP Docker Pattern

Laravel docs show official FrankenPHP images as a base:

```dockerfile
FROM dunglas/frankenphp

RUN install-php-extensions \
    pcntl

COPY . /app

ENTRYPOINT ["php", "artisan", "octane:frankenphp"]
```

During development, a low worker/max-request count makes behavior closer to classic request isolation:

```yaml
services:
  frankenphp:
    build:
      context: .
    entrypoint: php artisan octane:frankenphp --workers=1 --max-requests=1
    ports:
      - "8000:8000"
    volumes:
      - .:/app
```

## Caddyfile

Use a custom Caddyfile when you need advanced FrankenPHP/Caddy behavior:

```bash
php artisan octane:start --server=frankenphp --caddyfile=/path/to/Caddyfile
```

Prefer Octane defaults until a concrete routing, TLS, compression, static file, proxy, Mercure, or Caddy directive need appears.

## HTTPS And URLs

When Octane serves HTTPS, set the Octane HTTPS config/env so generated links use `https://`:

```php
'https' => env('OCTANE_HTTPS', false),
```

Set `OCTANE_HTTPS=true` in the environment when appropriate.

## Watch Mode

`--watch` restarts workers when files change. Laravel docs note it requires Node and Chokidar:

```bash
npm install --save-dev chokidar
php artisan octane:start --server=frankenphp --watch
```

Configure watched paths in `config/octane.php`.

## Production Process Management

Use Supervisor, systemd, container orchestration, or another process monitor. Do not rely on an interactive shell process for production.

Example Supervisor command shape:

```ini
command=php /path/to/app/artisan octane:start --server=frankenphp --host=127.0.0.1 --port=8000
```

After deploy:

```bash
php artisan octane:reload
```

## Swoole-Only Octane Features

Do not assume these are available when using FrankenPHP:

- `Octane::concurrently()`;
- Octane ticks/intervals;
- Octane cache driver;
- Octane tables.

Laravel docs mark these as requiring Swoole. For FrankenPHP, use queues, cache/database backends, scheduler, or external services instead.
