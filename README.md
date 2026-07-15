# Senior Angular Architect

An AI-agent skill for senior-level Angular architecture guidance. It helps agents give production-focused recommendations for Angular application design, performance, security, state management, testing strategy, migration planning, and team governance.

## Purpose

This skill represents a senior Angular architect with deep hands-on experience building enterprise Angular applications. It is designed for agent use in architecture reviews, technical planning, modernization work, production readiness reviews, and team-standard guidance.

The skill emphasizes:

- **Architecture Design**: Feature structure, module boundaries, shared library ownership, and application layering.
- **Performance Optimization**: Change detection, bundle optimization, large lists, large forms, runtime performance, and memory safety.
- **Security**: XSS and CSRF prevention, authentication, authorization, CSP, secure storage, and safe data flow.
- **State Management**: Signals, RxJS, NgRx, ComponentStore, and service-based state patterns.
- **Testing Strategy**: Unit testing, integration testing, coverage standards, and CI quality gates.
- **RxJS Mastery**: Operator choice, cancellation, retry behavior, error handling, and async orchestration.
- **Modern Angular**: Standalone APIs, typed forms, control flow, deferred views, SSR, hydration, and zoneless-ready design.
- **Migration & Upgrades**: Angular version upgrades, legacy modernization, technical debt sequencing, and rollout planning.
- **Team Leadership**: Code quality, review standards, onboarding, conventions, and architectural governance.

## Repository Structure

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

## How Agents Should Use It

Start with `SKILL.md`. It defines the role, response style, architectural defaults, review heuristics, usage guidelines, and routing rules.

Load only the focused resource needed for the task:

- Use `references/architecture.md` for feature boundaries, standalone design, shared libraries, and application structure.
- Use `references/performance.md` for rendering, bundle size, forms, memory, SSR, hydration, and change-detection concerns.
- Use `references/security.md` for auth, authorization, CSP, XSS, CSRF, secure storage, and sensitive data handling.
- Use `references/testing.md` for unit testing, integration testing, coverage standards, and CI gates.
- Use `references/rxjs.md` for stream design, operator choice, cancellation, retry behavior, and async orchestration.
- Use `references/migration.md` for upgrades, modernization, deprecated APIs, and rollout planning.
- Use `references/design-patterns.md` for structural patterns such as the Facade pattern in Angular.
- Use `references/code-review.md` for review tasks and `references/eslint-config.md` for linting guidance.
- Use `references/project-structure.md` for project structure guidance.

## Production Standards

- Keep `SKILL.md` concise and route deeper material to focused Markdown files.
- Keep all supporting resources inside the single `references/` folder.
- Keep implementation examples inside their relevant reference files so agents get the pattern, trade-offs, and review intent in one place.
- Do not duplicate raw source content across the bundle unless it improves agent behavior.
- Keep the language agent-neutral so the skill can be used across AI agent platforms.

## Validation

Before publishing, validate that:

- `SKILL.md` has valid YAML frontmatter with `name`, `description`, and `metadata` fields.
- The metadata includes the expected category and stack values.
- Resource paths referenced by `SKILL.md` exist.
- The root has only one resource subfolder: `references/`.
- Implementation examples remain embedded in their relevant reference files.
- Production-facing documentation remains agent-neutral.
