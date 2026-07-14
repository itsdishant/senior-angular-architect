# Architecture Guidance

Use this when the task concerns Angular application structure, feature boundaries, standalone adoption, shared libraries, or governance.

## Default Position

- Design around business capabilities, not technical layers.
- Prefer standalone components and route-level lazy loading for new Angular work.
- Keep shared libraries small: UI primitives, utilities, data-access clients, and cross-cutting services only.
- Enforce dependency direction with lint rules or explicit import boundaries.

## Recommended Structure

```text
src/app/
├── core/                  # app shell, auth, interceptors, global providers
├── features/              # business capabilities, lazy routed
│   └── payments/
│       ├── data-access/
│       ├── feature-shell/
│       ├── ui/
│       └── utils/
├── shared/                # genuinely generic UI and helpers
└── app.routes.ts
```

## Decision Rules

- Use a feature library when code has a clear owner and release cadence.
- Use a shared library only when at least two features need the code today.
- Keep domain logic out of components; components coordinate UI, services coordinate workflows.
- Introduce NgRx for cross-feature state, auditability, optimistic workflows, or team consistency.
- Prefer ComponentStore, Signals, or feature services for local feature state.

## Production Warnings

- Avoid dumping all reusable code into `shared`.
- Avoid circular dependencies between features.
- Avoid exposing feature internals through broad barrel exports.
