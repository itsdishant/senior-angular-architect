# Architecture Guidance

This guide covers Angular app structure, feature boundaries, standalone adoption, shared libraries, and governance.

## Baseline

- Design around business capabilities rather than technical layers.
- Prefer standalone components and route-level lazy loading for new Angular work.
- Keep shared libraries small: UI primitives, utilities, data-access clients, and cross-cutting services only.
- Enforce dependency direction with lint rules or import boundaries.

## Recommended structure

```text
src/app/
├── core/                  # app shell, auth, interceptors, global providers
├── features/              # business capabilities, lazy routed
│   └── payments/
│       ├── data-access/
│       ├── feature-shell/
│       ├── ui/
│       └── utils/
├── shared/                # generic UI and helpers
└── app.routes.ts
```

## Decision rules

- Use a feature library when code has a clear owner and release cadence.
- Use a shared library only when two or more features need the code today.
- Keep domain logic out of components. Components manage UI; services manage workflows.
- Use NgRx for cross-feature state, auditability, optimistic workflows, or consistent team patterns.
- Use ComponentStore, Signals, or feature services for local feature state.

## Production warnings

- Don’t dump reusable code into a generic `shared` folder.
- Avoid circular dependencies between features.
- Don’t expose feature internals through broad barrel exports.
