# Angular Migration Checklist

## Before

- Record current Angular, TypeScript, RxJS, Node, CLI, Material, and builder versions.
- Make lint, typecheck, unit tests, and focused integration tests green.
- Identify deprecated APIs and high-risk packages.
- Create rollback and release monitoring notes.

## During

- Upgrade one Angular major at a time for large apps.
- Run official migrations before custom refactors.
- Keep lockfile changes reviewable.
- Fix compile errors before broad modernization.

## After

- Verify lazy routes, guards, interceptors, forms, SSR, and critical user journeys.
- Remove migration shims and dead compatibility code.
- Update team conventions and templates.
- Track runtime errors and performance after release.
