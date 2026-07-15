# Angular Code Review Checklist

## Architecture

- Feature boundaries are clear and imports do not cross into feature internals.
- Components are small, focused, and free of business workflow logic.
- Shared utilities are genuinely reusable and not a dumping ground.
- Public APIs are typed, narrow, and documented by usage.

## Angular

- New code uses standalone APIs unless there is a compatibility reason not to.
- Templates avoid expensive function calls and unstable object creation.
- Inputs are treated immutably and are OnPush-compatible.
- Subscriptions use `async` pipe, `takeUntilDestroyed`, or explicit teardown.

## State and RxJS

- Operator choice matches cancellation and ordering requirements.
- Errors are handled where recovery is possible.
- Shared state has a clear owner.
- Signals and RxJS are not mixed without a clear boundary.

## Security and Quality

- No unsafe DOM writes or unnecessary sanitizer bypasses.
- Authorization is enforced by APIs, not just route guards.
- Tests cover critical paths, edge cases, and failure states.
- Types avoid `any`; unknown values are narrowed.

## Anti-Patterns to Catch

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

Prefer the `async` pipe when the value is only consumed by the template.

### Mutating inputs with OnPush

```typescript
// Bad
this.user.name = 'New Name';

// Good
this.user = { ...this.user, name: 'New Name' };
```

OnPush-compatible components should receive new object references when data changes.

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

Components should coordinate UI state and delegate business workflows to services or feature stores.

### Using `any` as a default

```typescript
// Bad
data: any;

// Good
data: User | null;
payload: unknown;
```

Use `unknown` for untrusted data and narrow it with type guards before use.
