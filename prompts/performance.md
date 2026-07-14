# Performance Guidance

Use this for slow rendering, large lists, bundle size, forms, SSR/hydration, memory leaks, and change-detection issues.

## Default Position

- Design components to work with `ChangeDetectionStrategy.OnPush`.
- Prefer immutable input updates and stable references.
- Use route-level lazy loading and `@defer` for non-critical UI.
- Measure before and after: Angular DevTools, Chrome Performance, Lighthouse, bundle analyzer, and heap snapshots.

## Common Fixes

- Large lists: CDK virtual scrolling, pagination, `trackBy`, and stable item view models.
- Large forms: nested components, typed reactive forms, `updateOn: 'blur'`, debounced expensive validation, and async validator cancellation.
- Real-time updates: buffer or throttle streams, run noisy events outside Angular, and re-enter only for UI updates.
- Bundle size: lazy routes, remove duplicate libraries, replace Moment.js, import from tree-shakable entrypoints.
- Memory leaks: prefer `async` pipe, `takeUntilDestroyed`, teardown intervals, and inspect detached DOM nodes.

## Production Warnings

- Template functions and getters can become hot paths.
- Impure pipes run too often; use pure pipes by default.
- `shareReplay` without reset/refCount strategy can retain stale data.
- SSR code must guard browser-only APIs such as `window`, `document`, and storage.
