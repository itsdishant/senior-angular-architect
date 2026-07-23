# RxJS guidance

Use this for Angular async workflows, stream design, operator choice, cancellation, retries, caching, and memory safety.

## Default position

- Use RxJS for events over time, HTTP orchestration, cancellation, retries, and stream composition.
- Use Signals for synchronous local state and derived UI state when they are simpler.
- Prefer composition over nested subscriptions.
- Use `takeUntilDestroyed` or the `async` pipe for subscription cleanup.
- Keep side effects visible with `tap`, `finalize`, or explicit service methods.

## Search flow pattern

This is the pattern for typeahead, search boxes, filters, route reloads, and other "latest request wins" cases.

```typescript
@Injectable({ providedIn: 'root' })
export class ProductSearchFacade {
  private readonly api = inject(ProductApi);
  private readonly destroyRef = inject(DestroyRef);

  private readonly query$ = new Subject<string>();

  readonly loading = signal(false);
  readonly results = signal<Product[]>([]);

  constructor() {
    this.query$
      .pipe(
        map((query) => query.trim()),
        debounceTime(250),
        distinctUntilChanged(),
        filter((query) => query.length >= 3),
        tap(() => this.loading.set(true)),
        switchMap((query) =>
          this.api.searchProducts(query).pipe(
            catchError(() => of<Product[]>([])),
            finalize(() => this.loading.set(false)),
          ),
        ),
        takeUntilDestroyed(this.destroyRef),
      )
      .subscribe((products) => this.results.set(products));
  }

  search(query: string): void {
    this.query$.next(query);
  }
}
```

Why it works:

- `debounceTime` cuts noisy keystroke traffic.
- `distinctUntilChanged` avoids duplicate calls.
- `switchMap` drops stale HTTP requests.
- `catchError` keeps the search stream alive.
- `finalize` resets loading on success, error, or cancellation.

## Subject types

Use Subjects intentionally. In Angular, a Subject is usually an event boundary, not the default state store.

```typescript
export class SubjectTypeExamples {
  private readonly submittedSubject = new Subject<FormValue>();
  readonly submitted$ = this.submittedSubject.asObservable();

  private readonly selectedUserSubject = new BehaviorSubject<User | null>(null);
  readonly selectedUser$ = this.selectedUserSubject.asObservable();

  private readonly recentAuditSubject = new ReplaySubject<AuditEvent>(3);
  readonly recentAudit$ = this.recentAuditSubject.asObservable();

  private readonly exportCompleteSubject = new AsyncSubject<UploadResult>();
  readonly exportComplete$ = this.exportCompleteSubject.asObservable();

  submit(value: FormValue): void {
    this.submittedSubject.next(value);
  }

  selectUser(user: User | null): void {
    this.selectedUserSubject.next(user);
  }

  recordAudit(event: AuditEvent): void {
    this.recentAuditSubject.next(event);
  }

  finishExport(result: UploadResult): void {
    this.exportCompleteSubject.next(result);
    this.exportCompleteSubject.complete();
  }
}
```

Decision rules:

- Use `Subject` for fire-and-forget events.
- Use `BehaviorSubject` only when new subscribers need the current value.
- Use `ReplaySubject` for bounded history; set a buffer size.
- Use `AsyncSubject` for a final result emitted on completion.
- Prefer Signals or a feature store for UI state when stream composition is not required.

## Higher-order operators

Pick operators based on what the workflow needs: cancel, parallelize, queue, or ignore.

```typescript
@Injectable({ providedIn: 'root' })
export class OrderWorkflowService {
  private readonly http = inject(HttpClient);

  searchCustomers(query$: Observable<string>): Observable<Customer[]> {
    return query$.pipe(
      switchMap((query) =>
        this.http.get<Customer[]>('/api/customers', { params: { query } }),
      ),
    );
  }

  uploadAttachments(files: readonly File[]): Observable<UploadReceipt> {
    return from(files).pipe(
      mergeMap((file) => this.http.post<UploadReceipt>('/api/uploads', file), 3),
    );
  }

  submitQueue(drafts: readonly OrderDraft[]): Observable<OrderResult> {
    return from(drafts).pipe(
      concatMap((draft) => this.http.post<OrderResult>('/api/orders', draft)),
    );
  }

  preventDoubleSubmit(
    submitClicks$: Observable<void>,
    payload: Readonly<OrderDraft>,
  ): Observable<OrderResult> {
    return submitClicks$.pipe(
      exhaustMap(() => this.http.post<OrderResult>('/api/orders', payload)),
    );
  }
}
```

Use:

- `switchMap` when newer input should cancel older work.
- `mergeMap` for independent work; cap concurrency for large batches.
- `concatMap` when order matters.
- `exhaustMap` when you want to ignore repeated triggers until the first completes.

## Optimistic update workflow

Use this when the UI should feel fast while keeping data consistent.

```typescript
@Injectable({ providedIn: 'root' })
export class TaskStore {
  private readonly api = inject(TaskApi);

  private readonly tasks = signal<Task[]>([]);
  private readonly pendingIds = signal(new Set<string>());

  readonly all = this.tasks.asReadonly();
  readonly pending = this.pendingIds.asReadonly();

  toggleComplete(taskId: string): Observable<Task> {
    const before = this.tasks();
    const current = before.find((task) => task.id === taskId);

    if (!current) {
      return throwError(() => new Error(`Task not found: ${taskId}`));
    }

    const optimisticTask = {
      ...current,
      completed: !current.completed,
    };

    this.tasks.update((tasks) =>
      tasks.map((task) => (task.id === taskId ? optimisticTask : task)),
    );
    this.pendingIds.update((ids) => new Set(ids).add(taskId));

    return this.api.updateTask(taskId, optimisticTask).pipe(
      tap((savedTask) => {
        this.tasks.update((tasks) =>
          tasks.map((task) => (task.id === taskId ? savedTask : task)),
        );
      }),
      catchError((error) => {
        this.tasks.set(before);
        return throwError(() => error);
      }),
      finalize(() => {
        this.pendingIds.update((ids) => {
          const next = new Set(ids);
          next.delete(taskId);
          return next;
        });
      }),
    );
  }
}
```

What to keep in mind:

- Capture the current state before applying the optimistic update.
- Apply the UI change immediately.
- Mark the entity as pending so the UI can disable repeated actions or show progress.
- Replace the optimistic item with the server response when the request succeeds.
- Roll back to the prior state and surface a clear error if it fails.
- Clear pending state in a final teardown step.
- Use `exhaustMap` for submit actions that must ignore duplicates while one request is active.
- Use `switchMap` only when a newer action should cancel the old one.
- Keep rollback handling in a store or service, not in the component.
- Test optimistic behavior explicitly: success, rollback, pending state, and duplicate triggers.

## Error handling and retry

Handle errors where the workflow can recover. Don’t kill long-lived UI streams.

```typescript
@Injectable({ providedIn: 'root' })
export class AccountDataService {
  private readonly http = inject(HttpClient);

  loadAccount(accountId: string): Observable<Account | null> {
    return this.http.get<Account>(`/api/accounts/${accountId}`).pipe(
      retry({
        count: 3,
        delay: (_error, retryIndex) => timer(500 * retryIndex),
      }),
      catchError((error: HttpErrorResponse) => {
        if (error.status === 404) {
          return of(null);
        }

        return throwError(() => error);
      }),
    );
  }

  saveAccount(account: Account): Observable<Account> {
    return this.http.put<Account>(`/api/accounts/${account.id}`, account).pipe(
      catchError((error: HttpErrorResponse) => {
        const message =
          error.status === 401
            ? 'Session expired while saving account'
            : 'Account save failed';

        return throwError(() => new Error(message, { cause: error }));
      }),
    );
  }
}
```

Review notes:

- Retry only idempotent or safely repeatable operations.
- Keep retry count and delay explicit.
- Return fallback values only when the UI can truthfully continue.
- Rethrow with context when callers need to decide the next action.

## Memory Management

Prefer framework-managed teardown for components and directives.

```typescript
@Component({
  selector: 'app-account-summary',
  standalone: true,
  template: `{{ account()?.name }}`,
})
export class AccountSummaryComponent {
  private readonly route = inject(ActivatedRoute);
  private readonly accounts = inject(AccountDataService);
  private readonly destroyRef = inject(DestroyRef);

  readonly account = signal<Account | null>(null);

  constructor() {
    this.route.paramMap
      .pipe(
        map((params) => params.get('accountId')),
        filter((accountId): accountId is string => accountId !== null),
        switchMap((accountId) => this.accounts.loadAccount(accountId)),
        takeUntilDestroyed(this.destroyRef),
      )
      .subscribe((account) => this.account.set(account));
  }
}
```

Legacy teardown is still valid when `DestroyRef` is unavailable:

```typescript
private readonly destroy$ = new Subject<void>();

ngOnInit(): void {
  this.data$
    .pipe(takeUntil(this.destroy$))
    .subscribe((value) => this.value = value);
}

ngOnDestroy(): void {
  this.destroy$.next();
  this.destroy$.complete();
}
```

Review notes:

- Prefer the `async` pipe when templates can subscribe directly.
- Do not leave intervals, observers, custom subscriptions, or DOM listeners unmanaged.
- Avoid nested subscriptions; they hide teardown and error handling.

## Combining Observables

Use combination operators based on source lifetime.

```typescript
@Injectable({ providedIn: 'root' })
export class DashboardService {
  private readonly http = inject(HttpClient);

  dashboardViewModel(): Observable<DashboardVm> {
    return combineLatest({
      account: this.http.get<Account>('/api/account'),
      permissions: this.http.get<Permission[]>('/api/permissions'),
      preferences: this.http.get<UserPreferences>('/api/preferences'),
    }).pipe(
      map(({ account, permissions, preferences }) => ({
        accountName: account.name,
        canApprove: permissions.includes('approve'),
        theme: preferences.theme,
      })),
    );
  }

  loadReferenceData(): Observable<ReferenceData> {
    return forkJoin({
      countries: this.http.get<Country[]>('/api/countries'),
      currencies: this.http.get<Currency[]>('/api/currencies'),
      statuses: this.http.get<Status[]>('/api/statuses'),
    });
  }

  activityFeed(): Observable<Activity[]> {
    return merge(
      this.http.get<Activity[]>('/api/activity/recent'),
      this.http.get<Activity[]>('/api/activity/assigned'),
    );
  }
}
```

Use:

- `combineLatest` for long-lived sources where any change should recompute the view model.
- `forkJoin` for finite one-shot calls that must all complete.
- `merge` for independent sources that can emit into one output stream.
- `zip` only when emissions must be paired by index.

## Custom Operators

Create small custom operators when the same stream behavior repeats across features.

```typescript
export function logStream<T>(label: string): MonoTypeOperatorFunction<T> {
  return (source) =>
    source.pipe(
      tap({
        next: (value) => console.debug(`${label}: next`, value),
        error: (error) => console.error(`${label}: error`, error),
        complete: () => console.debug(`${label}: complete`),
      }),
    );
}

export function filterPresent<T>(): OperatorFunction<T | null | undefined, T> {
  return (source) =>
    source.pipe(
      filter((value): value is T => value !== null && value !== undefined),
    );
}

this.route.paramMap
  .pipe(
    map((params) => params.get('accountId')),
    filterPresent(),
    logStream('account route'),
    switchMap((accountId) => this.accounts.loadAccount(accountId)),
  )
  .subscribe();
```

Review notes:

- Keep custom operators generic and side-effect-light.
- Use explicit names that describe behavior, not implementation.
- Do not hide business decisions inside generic operators.

## `shareReplay` for Caching

Use `shareReplay` to share a single source subscription. Always configure the cache behavior explicitly.

```typescript
@Injectable({ providedIn: 'root' })
export class RuntimeConfigService {
  private readonly http = inject(HttpClient);

  readonly config$ = defer(() =>
    this.http.get<RuntimeConfig>('/api/runtime-config'),
  ).pipe(
    shareReplay({
      bufferSize: 1,
      refCount: true,
    }),
  );
}
```

Review notes:

- Use `refCount: true` when the source should disconnect after subscribers leave.
- Think carefully before caching user-specific or permission-sensitive data.
- Reset or invalidate the cache when configuration, tenant, user, or locale changes.

## Quick Reference

| Use case | Preferred operator |
| --- | --- |
| Transform values | `map` |
| Filter values | `filter`, `distinctUntilChanged` |
| Wait for typing to pause | `debounceTime` |
| Limit noisy emissions | `throttleTime`, `auditTime` |
| Cancel previous work | `switchMap` |
| Run independent work | `mergeMap` with concurrency |
| Preserve order | `concatMap` |
| Ignore repeated triggers | `exhaustMap` |
| Recompute from latest values | `combineLatest` |
| Wait for finite calls | `forkJoin` |
| Recover or rethrow | `catchError` |
| Retry safely | `retry` |
| Cleanup subscriptions | `takeUntilDestroyed`, `takeUntil` |
| Share one source | `shareReplay` |

## Production Warnings

- Nested subscriptions hide cancellation and error handling.
- Unbounded `mergeMap` can overload APIs.
- `Subject` as global mutable state becomes hard to reason about.
- `shareReplay` without explicit reset/refCount choices can retain stale or sensitive data.
- Catching errors too high in the stream can accidentally terminate user workflows.
