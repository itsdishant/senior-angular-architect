# Angular Design Patterns

Use this reference when a feature needs a cleaner boundary between Angular components and a set of related services, APIs, or domain operations. The Facade pattern is especially useful when a component would otherwise need to coordinate several services directly.

## Facade Pattern in Angular

The Facade pattern provides a simplified interface over a complex subsystem. Instead of letting a component call multiple services and stitch responses together itself, a facade service owns that orchestration.

### Why use it

- Keep components focused on presentation and user interaction.
- Hide service coordination, HTTP sequencing, and data shaping behind a single API.
- Reduce coupling between the component and the internal implementation of the subsystem.
- Make it easier to change underlying services without updating every consumer.

### Typical shape

- Client: the component or feature that needs data.
- Facade: a service that exposes a simple method for the client.
- Subsystem: multiple services such as user, product, and category services.

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

- The component does not need to know how products, categories, and users are loaded.
- The facade can later add caching, retry logic, or mapping without changing the component.
- Subsystems can evolve independently as long as the facade contract remains stable.

### Guidance for Angular teams

- Use a facade when a component or feature depends on multiple services that are conceptually part of one workflow.
- Keep the facade focused on one feature area; avoid turning it into a dumping ground for unrelated logic.
- Prefer a small, explicit API over a large generic service.
- Keep business orchestration in a service layer, while components remain UI-oriented.

### When not to use it

- For trivial screens that only need one service.
- When the abstraction hides too much and makes the code harder to reason about.
- When the logic is better expressed as a dedicated domain service or use case service.

## Recommended reading

- Use this pattern when a feature view needs a clean, high-level API over several collaborators.
- Pair it with domain services, RxJS streams, and well-defined interfaces for large Angular applications.
