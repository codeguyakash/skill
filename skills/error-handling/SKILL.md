---
name: error-handling
description: Proper exception and error handling for Node.js, TypeScript, React, and APIs. Use whenever code can fail: network, DB, parsing, I/O, user input, async flows.
---

# Error Handling

## Principles
1. **Fail loudly, recover gracefully.** Never swallow errors.
2. **Handle where you can act.** Catch at the layer that can recover or translate; otherwise let it propagate.
3. **Preserve context.** Wrap, don't replace: `new AppError('...', { cause: err })`.
4. **Separate audiences.** Users get safe, actionable messages. Logs get full detail. Never leak stack traces, SQL, or secrets to clients.
5. **Distinguish expected from unexpected.** Operational errors (bad input, not found, timeout) are handled. Programmer errors (bugs) are logged and crash/500.

## Forbidden
```ts
try { ... } catch (e) {}                  // empty catch
try { ... } catch (e) { console.log(e) }  // log and continue silently
catch (e: any) { throw new Error('failed') } // loses cause
promise.then(...)                          // no catch / floating promise
```

## Custom error class
```ts
export class AppError extends Error {
  constructor(
    message: string,
    public readonly code: string,
    public readonly status = 500,
    public readonly isOperational = true,
    options?: { cause?: unknown; details?: unknown },
  ) {
    super(message, { cause: options?.cause });
    this.name = new.target.name;
    this.details = options?.details;
    Error.captureStackTrace?.(this, new.target);
  }
  public readonly details?: unknown;
}

export class ValidationError extends AppError {
  constructor(message = 'Invalid input', details?: unknown) {
    super(message, 'VALIDATION_ERROR', 422, true, { details });
  }
}
export class NotFoundError extends AppError {
  constructor(resource = 'Resource') {
    super(`${resource} not found`, 'NOT_FOUND', 404);
  }
}
export class UnauthorizedError extends AppError {
  constructor() { super('Authentication required', 'UNAUTHORIZED', 401); }
}
export class ForbiddenError extends AppError {
  constructor() { super('Access denied', 'FORBIDDEN', 403); }
}
export class ConflictError extends AppError {
  constructor(message = 'Conflict') { super(message, 'CONFLICT', 409); }
}
```

## Express: async wrapper + central handler
```ts
export const asyncHandler =
  (fn: RequestHandler): RequestHandler => (req, res, next) =>
    Promise.resolve(fn(req, res, next)).catch(next);

export const errorHandler: ErrorRequestHandler = (err, req, res, _next) => {
  const isApp = err instanceof AppError;
  const status = isApp ? err.status : 500;

  logger[status >= 500 ? 'error' : 'warn']({
    err, requestId: req.id, path: req.path, method: req.method, userId: req.user?.id,
  });

  res.status(status).json({
    success: false,
    error: {
      code: isApp ? err.code : 'INTERNAL_ERROR',
      message: isApp && err.isOperational ? err.message : 'Something went wrong. Please try again.',
      ...(isApp && err.details ? { details: err.details } : {}),
      requestId: req.id,
    },
  });
};

process.on('unhandledRejection', (reason) => { logger.fatal({ reason }, 'unhandledRejection'); throw reason; });
process.on('uncaughtException', (err) => { logger.fatal({ err }, 'uncaughtException'); process.exit(1); });
```

## Result pattern for expected failures
```ts
type Result<T, E = AppError> = { ok: true; value: T } | { ok: false; error: E };
```
Use it for parsing, validation, and external calls where failure is normal.

## Network / external calls
- Always set a timeout (`AbortSignal.timeout(10_000)`).
- Retry only idempotent calls, with exponential backoff + jitter, max 3 attempts.
- Check `response.ok`; `fetch` does not throw on 4xx/5xx.
- Circuit-break or degrade gracefully when a dependency is down.

```ts
export async function fetchJson<T>(url: string, init?: RequestInit): Promise<T> {
  const res = await fetch(url, { ...init, signal: AbortSignal.timeout(10_000) });
  if (!res.ok) {
    throw new AppError(`Request failed: ${res.status}`, 'UPSTREAM_ERROR', 502, true, {
      details: { url, status: res.status },
    });
  }
  return (await res.json()) as T;
}
```

## Database
- Wrap multi-step writes in transactions; roll back on failure.
- Map driver errors to AppErrors (duplicate key → `ConflictError`, invalid ObjectId → `ValidationError`).
- Release connections in `finally`.

## React
- **Error Boundary** per route/section (`react-error-boundary` or Next.js `error.tsx` + `global-error.tsx`).
- Handle async errors in the component (state) — boundaries do not catch event handlers or promises.
- Every data fetch renders loading, error (with retry), and empty states.
- Report to monitoring (Sentry) in boundaries and global handlers.

```tsx
<ErrorBoundary FallbackComponent={ErrorFallback} onError={reportError}>
  <Dashboard />
</ErrorBoundary>
```

## Logging
- Levels: `fatal`, `error` (needs action), `warn` (recoverable), `info` (business events), `debug`.
- Include: requestId, userId, operation, error `cause`. Exclude: passwords, tokens, full PII.
- One log per error at the boundary; do not log and rethrow at every layer.

## Checklist
- [ ] No empty or log-only catch blocks
- [ ] Errors typed and carry `cause`
- [ ] Central handler maps errors to status + safe message
- [ ] Timeouts on all I/O
- [ ] `finally` for cleanup
- [ ] User sees a clear message and next step
- [ ] Failure path has a test
