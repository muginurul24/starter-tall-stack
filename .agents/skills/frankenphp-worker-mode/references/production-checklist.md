# FrankenPHP Production Checklist

Use before changing deployment, Docker, Caddyfile, proxying, process management, or worker tuning.

## Runtime

- Confirm the app runs through Octane with `--server=frankenphp` or `octane:frankenphp`.
- Confirm required PHP extensions are installed in the FrankenPHP runtime.
- Confirm `.env`, config, route, event, and view caches are built intentionally.
- Confirm workers are reloaded after deployment.

## Worker Tuning

- Start with default worker count unless traffic and CPU metrics justify changes.
- Use `--workers=N` to match workload and CPU characteristics.
- Use `--max-requests=N` to recycle workers and limit impact from leaks.
- Lower `--max-requests` temporarily while diagnosing memory growth.
- Avoid long-running requests in workers when jobs/queues are more appropriate.

## Proxy And Static Files

- Decide whether FrankenPHP/Caddy serves static assets directly or sits behind Nginx/Apache/CDN.
- Ensure `Host`, scheme, client IP, and forwarded headers are preserved when proxying.
- Configure trusted proxies in Laravel if behind a proxy/load balancer.
- Verify generated URLs use the correct scheme, especially HTTPS.

## Caddy/FrankenPHP

- Use a custom Caddyfile only when defaults are insufficient.
- Keep Caddyfile routing simple and explicit.
- Validate Caddyfile syntax before deployment when possible.
- Be careful with global directives that affect all sites/workers.

## Observability

- Send stdout/stderr to persistent logs.
- Capture Octane/FrankenPHP structured logs when enabled.
- Monitor worker memory, CPU, restart count, request latency, and error rate.
- Add health checks for the process manager/load balancer.

## Deployment Procedure

1. Pull/build new code.
2. Install dependencies.
3. Build frontend assets if needed.
4. Run migrations with an explicit deployment plan.
5. Build Laravel caches intentionally.
6. Reload Octane workers.
7. Verify status and smoke-test important routes.

## Safety Review

Before production, audit for:

- mutable static properties;
- per-request state in singletons;
- request/config/container injected into singleton constructors;
- tenant/auth/locale stored in static state;
- large in-memory caches without eviction;
- files or sockets left open across requests;
- queues/schedulers accidentally duplicated in web workers;
- Swoole-only Octane features used while server is FrankenPHP.
