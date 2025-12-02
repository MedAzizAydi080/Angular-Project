# 1. Project Overview – Angular E‑commerce

This project is a modern e‑commerce frontend, inspired by Amazon, built with **Angular 19**, **Tailwind CSS 4**, and **Flowbite**.

You can present it as a **complete shopping flow**:

- Browse products on the home page
- Open a single product page
- Add products to the cart
- Apply a **gift card** (voucher)
- Complete the **checkout** form
- See a **payment success** screen
- Manage a **favorites (wishlist)** list
- View store location on the **Find Us (map)** page

---

## 1.1 Tech Stack

- **Framework:** Angular 19 (standalone components, no NgModules)
- **Language:** TypeScript
- **Styling:** Tailwind CSS 4 + Flowbite
- **Routing:** Angular Router (standalone routes in `app.routes.ts`)
- **HTTP & RxJS:** `HttpClient`, `Observable`, `BehaviorSubject`
- **Persistence:** `localStorage` for cart, favorites, gift cards, and purchases

Important config files:

- `src/app/app.config.ts` – application configuration (router + providers)
- `src/app/app.routes.ts` – route definitions
- `angular.json` – Angular workspace + build configuration

---

## 1.2 High‑Level Folder Structure

Most of the logic lives under `src/app`:

```text
src/app
├── app.component.*        # Root shell (navbar + router + footer)
├── app.config.ts          # Angular application configuration
├── app.routes.ts          # All routes
├── home/                  # Home page + product grid
├── product/               # Product details page
├── cart/                  # Cart page
├── checkout/              # Checkout page (reactive form)
├── gift-cards/            # Gift card purchase screen
├── favorites/             # Wishlist page
├── find-us/               # Map + contact info
├── payment/               # Payment success screen
├── core/services/         # Business logic (products, checkout, gift cards...)
└── shared/                # Navbar, footer, offer cards, pipes, models
```

---

## 1.3 Main Features to Mention

When you present, focus on these core flows:

1. **Product system**
   - `ProductService` in `src/app/core/services/product.service.ts`
   - Loads data from `ProductApiService`
   - Caches products and exposes `products$` as an `Observable`.

2. **Product listing & details**
   - Home grid: `HomeComponent` + `HomeProductComponent`
   - Detail page: `ProductComponent` in `src/app/product/product.component.ts`
   - Uses the **TND currency pipe** and **favorites** heart.

3. **Cart**
   - `CartComponent` in `src/app/cart/cart.component.ts`
   - Uses `localStorage` key `cart-products`.
   - Calculates total, discount from gift card, and final total.

4. **Gift cards**
   - Purchase: `GiftCardsComponent`
   - Logic: `GiftCardService` + `CheckoutService`
   - Gift cards can be bought, stored, and then applied to orders.

5. **Checkout flow**
   - `CheckoutComponent` in `src/app/checkout/checkout.component.ts`
   - Reactive shipping form with validation
   - Uses `CheckoutService` to handle state, totals, and payment.

6. **Favorites (wishlist)**
   - `FavoritesService` + `FavoritesComponent`
   - Uses `localStorage` key `favorite-products`.
   - Navbar shows favorites count.

7. **Find Us (map)**
   - `FindUsComponent` shows a map (Leaflet) of the store location.

Each of the next docs focuses on one set of features:

- `02-routing-and-layout.md` – routing + root layout
- `03-products-and-home.md` – product loading + home grid + search
- `04-cart-and-checkout.md` – cart, checkout, totals, and gift card application
- `05-gift-cards.md` – how gift cards are stored and used
- `06-favorites-and-navbar.md` – wishlist + navbar badges
- `07-find-us-and-footer.md` – map + footer
- `08-presentation-guide.md` – how to explain the project in front of your professor
