# Angular Performance Guidance and Audit Checklist

Use this for slow rendering, large lists, bundle size, forms, SSR/hydration, memory leaks, and change-detection issues.

## Default Position

- Design components to work with `ChangeDetectionStrategy.OnPush`.
- Prefer immutable input updates and stable references.
- Use route-level lazy loading and `@defer` for non-critical UI.
- Measure before and after: Angular DevTools, Chrome Performance, Lighthouse, bundle analyzer, and heap snapshots.

## Common Fixes

- Large lists: CDK virtual scrolling, pagination, `trackBy` or `@for (...; track ...)`, and stable item view models.
- Large forms: nested components, typed reactive forms, `updateOn: 'blur'`, debounced expensive validation, and async validator cancellation.
- Bundle size: lazy routes, remove duplicate libraries, replace Moment.js, and import from tree-shakable entry points.
- Memory leaks: prefer `async` pipe, `takeUntilDestroyed`, teardown intervals, and inspect detached DOM nodes.

## Large Reactive Form Pattern

Use this when a user asks how to improve a large Angular reactive form with many controls, validators, or calculated values.

```typescript
@Component({
  selector: 'app-order-lines-form',
  standalone: true,
  imports: [ReactiveFormsModule],
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <form [formGroup]="form">
      <input formControlName="customerName" />

      <div formArrayName="lines">
        @for (line of lines.controls; track line.controls.id.value) {
          <fieldset [formGroupName]="$index">
            <input formControlName="sku" />
            <input type="number" formControlName="quantity" />
            <input type="number" formControlName="price" />
          </fieldset>
        }
      </div>
    </form>
  `,
})
export class OrderLinesFormComponent {
  private readonly fb = inject(NonNullableFormBuilder);

  readonly form = this.fb.group({
    customerName: this.fb.control('', {
      validators: [Validators.required],
      updateOn: 'blur',
    }),
    lines: this.fb.array(
      Array.from({ length: 50 }, (_, index) =>
        this.fb.group({
          id: this.fb.control(`line-${index}`),
          sku: this.fb.control('', {
            validators: [Validators.required],
            updateOn: 'blur',
          }),
          quantity: this.fb.control(1, {
            validators: [Validators.min(1)],
            updateOn: 'blur',
          }),
          price: this.fb.control(0, {
            validators: [Validators.min(0)],
            updateOn: 'blur',
          }),
        }),
      ),
    ),
  });

  readonly total$ = this.form.controls.lines.valueChanges.pipe(
    auditTime(16),
    map((lines) =>
      lines.reduce((total, line) => total + line.quantity * line.price, 0),
    ),
    distinctUntilChanged(),
  );

  get lines(): FormArray {
    return this.form.controls.lines;
  }
}
```

Architect guidance:

- Split the form into focused child components with stable inputs.
- Use typed reactive forms and avoid `any` in form value handling.
- Prefer `updateOn: 'blur'` for expensive or noisy controls.
- Debounce or batch expensive derived calculations.
- Track repeated controls by stable IDs.
- Keep validation rules close to the form model and move business workflows into services.
- Test dynamic controls, async validators, disabled states, error states, and calculated values.
- Avoid rebuilding form arrays during normal value changes.
- Avoid template methods that recompute totals or validation state on every change-detection pass.
- Use async validators with cancellation behavior for server-backed checks.
- Consider progressive disclosure for very large forms: steps, sections, or lazy-rendered panels.

## Performance Audit Checklist

### Measure

- Capture baseline Lighthouse, Angular DevTools, Chrome Performance, and bundle analyzer output.
- Identify whether the bottleneck is network, JavaScript parse/execute, rendering, change detection, memory, or API latency.
- Test on representative lower-end devices.

### Rendering

- Use OnPush-compatible component design.
- Use `trackBy` or `@for (...; track ...)` for repeated lists.
- Use CDK virtual scroll or pagination for large lists.
- Avoid template methods and impure pipes in hot views.

### Bundles

- Lazy load feature routes.
- Use `@defer` for non-critical UI.
- Remove duplicate heavy dependencies.
- Prefer tree-shakable imports.

### Runtime and Memory

- Tear down subscriptions, intervals, observers, and other long-lived browser resources.
- Throttle or buffer high-frequency streams.
- Guard browser APIs in SSR/hydrated code.
- Compare heap snapshots for detached DOM nodes.

## Production Warnings

- Template functions and getters can become hot paths.
- Impure pipes run too often; use pure pipes by default.
- `shareReplay` without reset/refCount strategy can retain stale data.
- SSR code must guard browser-only APIs such as `window`, `document`, and storage.
