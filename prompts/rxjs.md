# RxJS Guidance

Use this for Angular async orchestration, stream design, operator choice, cancellation, retry behavior, and memory safety.

## Default Position

- Keep RxJS for events over time, cancellation, retries, and orchestration.
- Use Signals for synchronous local state and derived UI state when simpler.
- Prefer stream composition over nested subscriptions.
- Prefer `takeUntilDestroyed` or `async` pipe for subscription lifecycle.

## Operator Choices

- `switchMap`: cancel stale requests, search, route param reloads.
- `concatMap`: preserve order, queue saves or writes.
- `mergeMap`: parallel independent work with bounded concurrency.
- `exhaustMap`: ignore repeats while active, submit buttons, login, payment.
- `combineLatest`: derive from long-lived state sources.
- `forkJoin`: wait for finite one-shot calls.

## Error Handling

- Catch errors at the level where recovery is possible.
- Keep outer streams alive for UI workflows.
- Use retry with limits, delay, and idempotency awareness.
- Surface user-readable errors and preserve diagnostic detail for logs.

## Production Warnings

- Nested subscriptions hide cancellation and error handling.
- Unbounded `mergeMap` can overload APIs.
- `Subject` as global mutable state becomes hard to reason about.
