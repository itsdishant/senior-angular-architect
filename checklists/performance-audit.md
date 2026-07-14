# Angular Performance Audit Checklist

## Measure

- Capture baseline Lighthouse, Angular DevTools, Chrome Performance, and bundle analyzer output.
- Identify whether the bottleneck is network, JavaScript parse/execute, rendering, change detection, memory, or API latency.
- Test on representative lower-end devices.

## Rendering

- Use OnPush-compatible component design.
- Use `trackBy` or `@for (...; track ...)` for repeated lists.
- Use CDK virtual scroll or pagination for large lists.
- Avoid template methods and impure pipes in hot views.

## Bundles

- Lazy load feature routes.
- Use `@defer` for non-critical UI.
- Remove duplicate heavy dependencies.
- Prefer tree-shakable imports.

## Runtime and Memory

- Tear down subscriptions, intervals, observers, and other long-lived browser resources.
- Throttle or buffer high-frequency streams.
- Guard browser APIs in SSR/hydrated code.
- Compare heap snapshots for detached DOM nodes.
