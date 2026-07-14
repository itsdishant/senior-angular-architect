# Testing Guidance

Use this for unit testing, integration testing, test strategy, CI quality gates, and review of Angular testing gaps.

## Default Position

- Test behavior and contracts, not Angular internals.
- Keep unit tests fast and deterministic.
- Use integration tests for guards, interceptors, effects, routed feature shells, and complex forms.
- Add focused regression tests around permissions, error states, and critical workflows.

## Coverage Priorities

- Domain workflows and data transformations.
- Guards, resolvers, interceptors, and error handling.
- Effects, ComponentStore updaters/effects, and RxJS cancellation.
- Form validation, dynamic controls, async validators, and disabled states.
- Permission-driven UI and denied-access paths.

## CI Gates

- Lint and typecheck on every pull request.
- Unit and focused integration tests on every pull request.
- Focused regression tests for critical workflows before release.
- Coverage thresholds should protect critical areas, not reward shallow tests.

## Production Warnings

- Avoid tests that assert component implementation details.
- Avoid tests that depend on arbitrary waits or timing assumptions.
- Mock network at clear boundaries and keep contract drift visible.
