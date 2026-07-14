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
├── agents/
│   └── openai.yaml
├── prompts/
│   ├── architecture.md
│   ├── performance.md
│   ├── security.md
│   ├── testing.md
│   ├── rxjs.md
│   └── migration.md
├── examples/
│   ├── form-performance.md
│   └── optimistic-update.md
├── checklists/
│   ├── code-review.md
│   ├── migration-checklist.md
│   ├── performance-audit.md
│   └── security-audit.md
├── templates/
│   ├── eslint-config.md
│   ├── eslint-config.json
│   └── project-structure/
│       └── README.md
└── references/
    └── interview-questions.md
```

## How Agents Should Use It

Start with `SKILL.md`. It defines the role, response style, architectural defaults, review heuristics, usage guidelines, and routing rules.

Load only the focused resource needed for the task:

- Use `prompts/architecture.md` for feature boundaries, standalone design, shared libraries, and application structure.
- Use `prompts/performance.md` for rendering, bundle size, forms, memory, SSR, hydration, and change-detection concerns.
- Use `prompts/security.md` for auth, authorization, CSP, XSS, CSRF, secure storage, and sensitive data handling.
- Use `prompts/testing.md` for unit testing, integration testing, coverage standards, and CI gates.
- Use `prompts/rxjs.md` for stream design, operator choice, cancellation, retry behavior, and async orchestration.
- Use `prompts/migration.md` for upgrades, modernization, deprecated APIs, and rollout planning.
- Use `checklists/` for review and audit tasks.
- Use `examples/` for Markdown examples that explain implementation patterns without storing standalone code files.
- Use `templates/` for project structure and ESLint guidance.
- Use `references/interview-questions.md` only as raw background when the focused resources are not enough.

## Production Standards

- Keep `SKILL.md` concise and route deeper material to focused Markdown files.
- Keep examples in Markdown so agents understand the reasoning, pattern, trade-offs, and review intent.
- Keep reusable machine-readable configuration, such as `templates/eslint-config.json`, alongside explanatory Markdown.
- Do not duplicate the raw reference content across the bundle unless it improves agent behavior.
- Keep the language agent-neutral so the skill can be used across AI agent platforms.

## Validation

Before publishing, validate that:

- `SKILL.md` has valid YAML frontmatter with only `name` and `description`.
- Resource paths referenced by `SKILL.md` exist.
- Markdown examples remain in `examples/`.
- JSON templates parse successfully.
- Production-facing documentation remains agent-neutral.
