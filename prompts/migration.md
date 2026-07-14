# Migration Guidance

Use this for Angular version upgrades, standalone migration, NgModule reduction, RxJS modernization, builder changes, technical debt sequencing, and legacy application modernization.

## Default Position

- Upgrade incrementally, one Angular major at a time unless the app is small and well tested.
- Stabilize tests and typecheck before the migration.
- Prefer official migrations first: `ng update`, Angular CLI migrations, Angular ESLint migrations.
- Separate mechanical migrations from architectural refactors.

## Migration Sequence

1. Inventory Angular, TypeScript, RxJS, Node, builders, Material, and critical third-party packages.
2. Make CI green and add smoke coverage around critical flows.
3. Run official migrations and fix compile/runtime issues.
4. Modernize incrementally: standalone routes, typed forms, control flow, signals, `provideHttpClient`.
5. Remove dead modules, duplicated dependencies, and deprecated APIs.
6. Add rollback notes and release monitoring.

## Production Warnings

- Do not combine framework upgrades with broad UI rewrites.
- SSR and hydration migrations need browser/server API audits.
- Material migrations can affect visual regressions; screenshot checks help.
- Lockfile churn can hide unrelated dependency changes.
