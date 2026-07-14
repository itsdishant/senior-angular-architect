# Large Form Performance Example

Use this example when a user asks how to improve a large Angular reactive form with many controls, validators, or calculated values.

## Pattern

- Split the form into focused child components with stable inputs.
- Use typed reactive forms and avoid `any` in form value handling.
- Prefer `updateOn: 'blur'` for expensive or noisy controls.
- Debounce or batch expensive derived calculations.
- Track repeated controls by stable IDs.
- Keep validation rules close to the form model and move business workflows into services.
- Test dynamic controls, async validators, disabled states, error states, and calculated values.

## Architect Guidance

- Avoid rebuilding form arrays during normal value changes.
- Avoid template methods that recompute totals or validation state on every change-detection pass.
- Use async validators with cancellation behavior for server-backed checks.
- Consider progressive disclosure for very large forms: steps, sections, or lazy-rendered panels.

