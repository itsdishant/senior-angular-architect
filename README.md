# Senior Angular Architect

A set of documents for agents that answer senior Angular architecture questions. It covers app structure, performance, security, state, testing, migration, and team practices.

## Purpose

This repo captures the guidance a senior Angular architect would use when reviewing or advising on enterprise Angular work.

It covers:

- **Architecture design**: feature boundaries, shared libraries, app layers
- **Performance**: change detection, bundle size, large forms, runtime cost
- **Security**: auth, authorization, CSP, XSS, CSRF, secure storage
- **State**: Signals, RxJS, NgRx, ComponentStore, service-driven patterns
- **Testing**: unit tests, integration tests, coverage, CI gates
- **Modern Angular**: standalone components, typed forms, control flow, SSR, hydration, zoneless-ready design
- **Migration**: Angular upgrades, legacy modernization, technical debt
- **Team practices**: code review standards, onboarding, conventions

## Repository structure

```text
senior-angular-architect/
├── SKILL.md
└── references/
    ├── architecture.md
    ├── code-review.md
    ├── design-patterns.md
    ├── eslint-config.md
    ├── migration.md
    ├── performance.md
    ├── project-structure.md
    ├── rxjs.md
    ├── security.md
    └── testing.md
```

## How agents should use it

Start with `SKILL.md` for the agent behavior and routing rules. Then open the specific reference for the task.

- `references/architecture.md`: app structure, feature boundaries, standalone components, shared libraries
- `references/performance.md`: rendering, bundles, large lists and forms, memory, SSR/hydration
- `references/security.md`: auth, RBAC, CSP, XSS, CSRF, secure storage
- `references/testing.md`: unit testing, integration testing, test strategy, CI gates
- `references/rxjs.md`: observables, operator choice, cancellation, async error handling
- `references/migration.md`: version upgrades, modernization, migration planning
- `references/design-patterns.md`: architecture and boundary patterns
- `references/code-review.md`: review heuristics and common issues
- `references/eslint-config.md`: lint rules and Angular ESLint setup
- `references/project-structure.md`: recommended project layout

## Standards

- Keep `SKILL.md` focused and point agents to the right reference file.
- Keep supporting material in `references/`.
- Put examples in the file where they belong.
- Avoid duplicate documentation unless it helps clarity.
- Keep the language practical and neutral.

## Validation

Before publishing:

- Check `SKILL.md` has valid YAML frontmatter with `name`, `description`, and `metadata`.
- Confirm metadata includes category and stack values.
- Verify referenced files exist.
- Make sure the repo exposes only one reference folder.
- Keep examples close to their guidance and avoid repeated content.
