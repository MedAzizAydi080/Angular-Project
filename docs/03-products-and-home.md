# 3. Products & Home Page

This section explains how products are loaded and how the home page displays them.

---

## 3.1 Product Model

**File:** `src/app/shared/models/product.ts`

This interface defines the shape of a product:

```ts
export interface Product {
  id: string;
  name: string;
  description: string;
  price: number;
  image: string;
  category: string;
}
```

You can say:

> "Every product has an `id`, `name`, `description`, `price`, `image` URL, and `category`. This model is reused across services and components."

---

## 3.2 Product Service (data + cache)

**File:** `src/app/core/services/product.service.ts`

```ts
@Injectable({ providedIn: 'root' })
export class ProductService {
  private productApiService = inject(ProductApiService);

  private productsCache: Product[] = [];
  private productsSubject = new BehaviorSubject<Product[]>([]);
  products$ = this.productsSubject.asObservable();

  getAll(): Observable<Product[]> {
    if (this.productsCache.length > 0) {
      return of(this.productsCache);
    }

    return this.productApiService.getProducts().pipe(
      tap((products: Product[]) => {
        this.productsCache = products;
        this.productsSubject.next(products);
      })
    );
  }

  getOffers(): Observable<Product[]> {
    const numberOfOffers = 5;
    return this.getAll().pipe(
      map((products) => products.slice(0, numberOfOffers))
    );
  }

  searchProducts(term: string): Observable<Product[]> {
    const searchTerm = term.toLowerCase().trim();
    if (!searchTerm) {
      return this.getAll();
    }
    return this.getAll().pipe(
      map((products) =>
        products.filter((p) =>
          p.name.toLowerCase().includes(searchTerm) ||
          p.description.toLowerCase().includes(searchTerm)
        )
      )
    );
  }

  getById(id: string): Observable<Product | undefined> {
    if (this.productsCache.length > 0) {
      const product = this.productsCache.find((p) => p.id === id);
      if (product) {
        return of(product);
      }
    }

    return this.productApiService.getProduct(id).pipe(
      tap(() => { /* could update cache here */ })
    );
  }
}
```

Explanation in simple words:

- `ProductService` is responsible for **fetching** and **caching** products.
- `productsCache` avoids calling the API multiple times.
- `products$` is an `Observable` that components can subscribe to.
- `getOffers()` just takes the first 5 products as special offers.
- `searchProducts(term)` filters by name or description.
- `getById(id)` returns a single product by ID (from cache or API).

You can summarize:

> "I separated data access into `ProductService`. It talks to `ProductApiService`, caches the result, and exposes observables for the UI."

---

## 3.3 Home Page Components

**Folder:** `src/app/home/`

- `home.component.ts` / `.html` – container for the home screen.
- `components/home-product/home-product.component.*` – card for a single product.
- `components/home-product-loading/*` – skeleton state while loading.

Typical responsibilities of `HomeComponent`:

- Inject `ProductService`.
- Subscribe to `products$` or call `getAll()` on init.
- Pass each product to `HomeProductComponent` for display.

In the template (conceptually):

```html
<section id="products" class="grid ...">
  <app-home-product
    *ngFor="let product of products"
    [product]="product">
  </app-home-product>
</section>
```

Notes:

- The `id="products"` works with navbar anchor links to scroll to products.
- Tailwind classes handle the responsive grid.

---

## 3.4 HomeProductComponent (product card)

**File:** `src/app/home/components/home-product/home-product.component.ts`

This standalone component typically:

- Receives a `product` as `@Input()`.
- Shows image, name, and price.
- Uses the **TND currency pipe** (`TndCurrencyPipe`).
- Links to the detail page with `routerLink=[ '/products', product.id ]`.

Image handling:

- Uses classes like `object-contain`, `max-w-full`, `max-h-full` so images dont overflow their container.

When presenting, mention:

> "I built reusable product cards as a standalone component. The home page just passes data to it. This keeps the UI consistent across the app."

---

## 3.5 Product Details Page

**File:** `src/app/product/product.component.ts`

Responsibilities:

- Read the `id` from the route (`/products/:id`).
- Use `ProductService.getById(id)` to load the product.
- Allow adding to cart.
- Allow toggling favorite (heart icon) via `FavoritesService`.

Talking points:

- Uses Angular Router to grab the route param.
- Reuses the same `Product` model and `TndCurrencyPipe`.
- The heart icon state is driven by `FavoritesService.isFavorite(id)`.
