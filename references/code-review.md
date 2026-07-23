# Angular Code Review Checklist

## Architecture

- Feature boundaries should be clear and imports should not cross into feature internals.
- Components should be small, focused, and free of business workflow logic.
- Shared utilities should be genuinely reusable, not a dumping ground.
- Public APIs should be typed, narrow, and documented by how they are used.

## Angular

- New code should use standalone APIs unless compatibility prevents it.
- Templates should avoid expensive functions and unstable object creation.
- Inputs should remain immutable and work with OnPush.
- Subscriptions should use the `async` pipe, `takeUntilDestroyed`, or explicit teardown.

## State and RxJS

- Operator choice should reflect cancellation and ordering requirements.
- Handle errors where recovery is possible.
- Shared state should have a clear owner.
- Don’t mix Signals and RxJS without a clear boundary.

## Security and quality

- No unsafe DOM writes or unnecessary sanitizer bypasses.
- Authorization should be enforced by APIs, not just route guards.
- Tests should cover critical paths, edge cases, and failure states.
- Avoid `any`; use `unknown` and narrow it before use.

## Anti-patterns to catch

### Subscribing without teardown

```typescript
// Bad
this.service.data$.subscribe((data) => {
  this.data = data;
});

// Good
this.service.data$
  .pipe(takeUntilDestroyed(this.destroyRef))
  .subscribe((data) => {
    this.data = data;
  });
```

Prefer the `async` pipe when the value is only consumed in the template.

### Mutating inputs with OnPush

```typescript
// Bad
this.user.name = 'New Name';

// Good
this.user = { ...this.user, name: 'New Name' };
```

OnPush components should receive new references when data changes.

### Putting business logic in components

```typescript
// Bad
submit() {
  // 100 lines of validation, mapping, API orchestration, and error handling
}

// Good
submit() {
  this.orderService.process(this.form.getRawValue());
}
```

Components should coordinate the UI and delegate workflows to services or stores.

### Using `any` as a default

```typescript
// Bad
data: any;

// Good
data: User | null;
payload: unknown;
```

Use `unknown` for untrusted data and narrow it with type guards.
