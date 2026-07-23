# Angular migration guidance and checklist

Use this when you are upgrading Angular, moving to standalone components, reducing NgModules, modernizing RxJS, or cleaning legacy app debt.

## Default position

- Upgrade one Angular major at a time unless the app is small and well tested.
- Stabilize tests and typechecking before migration.
- Prefer official migrations like `ng update`, CLI migrations, and Angular ESLint migrations.
- Separate mechanical changes from architecture refactors.

## Migration sequence

1. Inventory Angular, TypeScript, RxJS, Node, builders, Material, and critical third-party packages.
2. Keep CI green and add smoke coverage for core flows.
3. Run official migrations and fix compile/runtime issues.
4. Modernize incrementally: standalone routes, typed forms, control flow, signals, `provideHttpClient`.
5. Remove dead modules, duplicate dependencies, and deprecated APIs.
6. Add rollback notes and release monitoring.

## Migration checklist

### Before

- Record current Angular, TypeScript, RxJS, Node, CLI, Material, and builder versions.
- Make lint, typecheck, unit tests, and focused integration tests green.
- Identify deprecated APIs and risky packages.
- Create rollback and release monitoring notes.

### During

- Upgrade one major at a time for large apps.
- Run official migrations before custom refactors.
- Keep lockfile changes reviewable.
- Fix compile errors before broad modernization.

### After

- Verify lazy routes, guards, interceptors, forms, SSR, and critical user journeys.
- Remove migration shims and dead compatibility code.
- Update team conventions and templates.
- Track runtime errors and performance after release.

## Production warnings

- Don’t combine framework upgrades with broad UI rewrites.
- SSR and hydration migrations need browser/server API checks.
- Material migrations can change appearance; screenshot validation helps.
- Lockfile churn can hide unrelated changes.
