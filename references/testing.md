# Testing guidance

Use this for unit testing, integration testing, test strategy, CI gates, and reviewing Angular testing gaps.

## Default position

- Test behavior and contracts, not Angular internals.
- Keep unit tests fast and deterministic.
- Use integration tests for guards, interceptors, effects, routed feature shells, and complex forms.
- Add focused regression tests around permissions, error states, and critical workflows.

## Coverage priorities

- Domain workflows and data transformations.
- Guards, resolvers, interceptors, and error handling.
- Effects, ComponentStore updaters/effects, and RxJS cancellation.
- Form validation, dynamic controls, async validators, and disabled states.
- Permission-driven UI and denied-access paths.

## CI gates

- Run lint and typecheck on every pull request.
- Run unit and focused integration tests on every pull request.
- Add focused regression tests for critical workflows before release.
- Use coverage thresholds to protect important areas, not reward shallow tests.

## Production warnings

- Avoid tests that assert implementation details.
- Avoid tests that rely on arbitrary waits or timing.
- Mock network at clear boundaries and keep contract drift visible.
