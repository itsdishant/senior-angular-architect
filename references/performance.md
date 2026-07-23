# Angular performance guidance and audit checklist

Use this when you are dealing with slow rendering, large lists, bundle size, forms, SSR/hydration, memory leaks, or change detection.

## Default position

- Design components to work with `ChangeDetectionStrategy.OnPush`.
- Prefer immutable input updates and stable references.
- Use route-level lazy loading and `@defer` for non-critical UI.
- Measure before and after with Angular DevTools, Chrome Performance, Lighthouse, bundle analyzer, and heap snapshots.

## Common fixes

- Large lists: CDK virtual scroll, pagination, `trackBy` or `@for (...; track ...)`, stable item view models.
- Large forms: nested components, typed reactive forms, `updateOn: 'blur'`, debounced expensive validation, async validator cancellation.
- Bundle size: lazy routes, remove duplicate libraries, replace Moment.js, use tree-shakable imports.
- Memory leaks: use `async` pipe, `takeUntilDestroyed`, teardown intervals, and inspect detached DOM nodes.

## Large reactive form pattern

Use this when a feature asks how to make a large reactive form faster or less noisy.

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

Guidance:

- Split the form into focused child components with stable inputs.
- Use typed reactive forms and avoid `any` in form values.
- Prefer `updateOn: 'blur'` for expensive or noisy controls.
- Debounce or batch expensive derived calculations.
- Track repeated controls by stable IDs.
- Keep validation rules close to the form model and move workflow logic into services.
- Test dynamic controls, async validators, disabled states, error states, and calculated values.
- Don’t rebuild form arrays during normal changes.
- Avoid template methods that recompute totals or validation on every check.
- Use async validators with cancellation for server-backed checks.
- Consider progressive disclosure for very large forms: steps, sections, or lazy panels.

## Performance audit checklist

### Measure

- Capture baseline data with Lighthouse, Angular DevTools, Chrome Performance, and bundle analyzer.
- Identify whether the bottleneck is network, parse/execute, rendering, change detection, memory, or API latency.
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

### Runtime and memory

- Tear down subscriptions, intervals, observers, and other browser resources.
- Throttle or buffer high-frequency streams.
- Guard browser APIs in SSR/hydrated code.
- Compare heap snapshots for detached DOM nodes.

## Production warnings

- Template functions and getters can become hot paths.
- Impure pipes should be avoided.
- `shareReplay` without reset/refCount can keep stale data.
- SSR code must guard browser-only APIs like `window`, `document`, and storage.
