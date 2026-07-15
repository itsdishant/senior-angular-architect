# Angular Migration Guidance and Checklist

Use this for Angular version upgrades, standalone migration, NgModule reduction, RxJS modernization, builder changes, technical debt sequencing, and legacy application modernization.

## Default Position

- Upgrade incrementally, one Angular major at a time unless the app is small and well tested.
- Stabilize tests and typecheck before the migration.
- Prefer official migrations first: `ng update`, Angular CLI migrations, and Angular ESLint migrations.
- Separate mechanical migrations from architectural refactors.

## Migration Sequence

1. Inventory Angular, TypeScript, RxJS, Node, builders, Material, and critical third-party packages.
2. Make CI green and add smoke coverage around critical flows.
3. Run official migrations and fix compile/runtime issues.
4. Modernize incrementally: standalone routes, typed forms, control flow, signals, and `provideHttpClient`.
5. Remove dead modules, duplicated dependencies, and deprecated APIs.
6. Add rollback notes and release monitoring.

## Migration Checklist

### Before

- Record current Angular, TypeScript, RxJS, Node, CLI, Material, and builder versions.
- Make lint, typecheck, unit tests, and focused integration tests green.
- Identify deprecated APIs and high-risk packages.
- Create rollback and release monitoring notes.

### During

- Upgrade one Angular major at a time for large apps.
- Run official migrations before custom refactors.
- Keep lockfile changes reviewable.
- Fix compile errors before broad modernization.

### After

- Verify lazy routes, guards, interceptors, forms, SSR, and critical user journeys.
- Remove migration shims and dead compatibility code.
- Update team conventions and templates.
- Track runtime errors and performance after release.

## Production Warnings

- Do not combine framework upgrades with broad UI rewrites.
- SSR and hydration migrations need browser/server API audits.
- Material migrations can affect visual regressions; screenshot checks help.
- Lockfile churn can hide unrelated dependency changes.
