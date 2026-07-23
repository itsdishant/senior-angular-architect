# Recommended Angular project structure

Use this as a starting point for enterprise Angular apps. Rename folders to match your business domains.

```text
src/
├── app/
│   ├── app.config.ts
│   ├── app.routes.ts
│   ├── core/
│   │   ├── auth/
│   │   ├── http/
│   │   ├── layout/
│   │   └── telemetry/
│   ├── features/
│   │   └── orders/
│   │       ├── data-access/
│   │       ├── feature-shell/
│   │       ├── ui/
│   │       └── utils/
│   └── shared/
│       ├── ui/
│       ├── util/
│       └── testing/
├── assets/
└── environments/
```

## Rules

- `core` is app-wide infrastructure and must not depend on feature code.
- `features/<domain>` owns routed screens and domain workflows.
- `data-access` owns API clients, DTO mapping, feature stores, and persistence.
- `ui` owns presentational components with narrow inputs and outputs.
- `shared` is for genuinely generic code used by multiple features today.
- Keep route declarations close to feature shells.
