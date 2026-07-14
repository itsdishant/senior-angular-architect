# Optimistic Update Example

Use this example when a user asks how to make Angular UI feel immediate while preserving data consistency.

## Pattern

- Capture the current state before applying a local update.
- Apply the optimistic UI change immediately.
- Mark the affected entity as pending so the UI can disable repeat actions or show progress.
- Send the API request through a service or feature store.
- Replace the optimistic entity with the server response when the request succeeds.
- Roll back to the captured state and surface a user-readable error when the request fails.
- Clear pending state in a final teardown step.

## Architect Guidance

- Use `exhaustMap` for submit-like actions where repeats must be ignored while a request is active.
- Use `switchMap` only when a newer action should cancel an older one.
- Keep rollback logic in a store/service rather than scattering it across components.
- Make optimistic behavior explicit in tests: success, failure rollback, pending state, and duplicate action handling.

