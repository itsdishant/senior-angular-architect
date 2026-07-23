# Angular Design Patterns

Use this guide when a feature needs a cleaner boundary between a component and several related services. The Facade pattern is useful when a component would otherwise manage multiple service calls itself.

## Facade pattern in Angular

A facade hides the details of a complex subsystem behind one service. The component asks the facade for a view model instead of calling several services directly.

### Why use it

- Keep components focused on UI and user interaction.
- Move service coordination, HTTP sequencing, and data mapping into one place.
- Reduce coupling to the subsystem implementation.
- Let the facade change without forcing updates in every component.

### Typical shape

- Client: the component or feature that needs data.
- Facade: a service that exposes a small API.
- Subsystem: several services such as user, product, and category services.

### Example: product catalog facade

```ts
// user.service.ts
@Injectable({ providedIn: 'root' })
export class UserService {
  getCurrentUser(): Observable<User> {
    return of({ id: 1, name: 'Jane Doe' });
  }
}

// categories.service.ts
@Injectable({ providedIn: 'root' })
export class CategoriesService {
  getCategories(): Observable<Category[]> {
    return of([
      { id: 1, name: 'Electronics' },
      { id: 2, name: 'Home' },
    ]);
  }
}

// products.service.ts
@Injectable({ providedIn: 'root' })
export class ProductsService {
  getProducts(): Observable<Product[]> {
    return of([
      { id: 1, name: 'Laptop', categoryId: 1, price: 999 },
      { id: 2, name: 'Chair', categoryId: 2, price: 120 },
    ]);
  }
}
```

```ts
// user-category-products.facade.ts
@Injectable({ providedIn: 'root' })
export class UserCategoryProductsFacadeService {
  constructor(
    private productsService: ProductsService,
    private categoriesService: CategoriesService,
    private userService: UserService,
  ) {}

  getProductCategoriesUser(): Observable<UserCategoryProduct> {
    return forkJoin({
      products: this.productsService.getProducts(),
      categories: this.categoriesService.getCategories(),
      user: this.userService.getCurrentUser(),
    });
  }
}
```

```ts
// facade.component.ts
@Component({
  selector: 'app-facade',
  standalone: true,
  imports: [AsyncPipe],
  template: `
    @if (viewModel$ | async; as viewModel) {
      <h2>Hello {{ viewModel.user.name }}</h2>
      <h3>Categories</h3>
      <ul>
        @for (category of viewModel.categories; track category.id) {
          <li>{{ category.name }}</li>
        }
      </ul>

      <h3>Products</h3>
      <ul>
        @for (product of viewModel.products; track product.id) {
          <li>{{ product.name }} - ${{ product.price }}</li>
        }
      </ul>
    }
  `,
})
export class FacadeComponent {
  readonly viewModel$ = this.facade.getProductCategoriesUser();

  constructor(private readonly facade: UserCategoryProductsFacadeService) {}
}
```

### Benefits in practice

- The component does not need to know how the underlying services work.
- The facade can add caching, retry logic, or mapping without changing the component.
- The subsystem can evolve while the facade API stays stable.

### Guidance for teams

- Use a facade when a feature depends on multiple services and the flow belongs to one workflow.
- Keep the facade focused; don’t let it become a grab bag of unrelated operations.
- Prefer a small, explicit API over a generic service.
- Keep business orchestration in service code and leave components to handle UI.

### When not to use it

- For simple screens that only need one service.
- When the abstraction hides too much and makes the code harder to understand.
- When the logic belongs in a dedicated domain service or use-case service.
