# 6. Favorites (Wishlist) & Navbar Integration

This part describes how the favorites (wishlist) feature works and how it is displayed in the navbar.

---

## 6.1 Favorites Service

**File:** `src/app/core/services/favorites.service.ts`

This service manages a list of favorite product IDs.

Key ideas:

- Uses `localStorage` key `favorite-products`.
- Exposes a `BehaviorSubject<string[]>` of favorite IDs.
- Provides helper methods such as:
  - `isFavorite(id: string): boolean`
  - `toggleFavorite(id: string): boolean`
  - `getFavoriteCount(): number`

You can say:

> "FavoritesService keeps track of product IDs that the user liked. It syncs them with localStorage so they persist across refreshes."

---

## 6.2 Product Page – Heart Toggle

**File:** `src/app/product/product.component.ts`

The product detail component:

- Injects `FavoritesService`.
- On init, checks if the current product ID is in favorites.
- Provides a method to toggle favorite when the heart icon is clicked.

In the template, the heart icon changes style depending on `isFavorite`.

Talking point:

> "The favorite button is just a projection of the FavoritesService state. The service is the single source of truth."

---

## 6.3 Favorites Page

**File:** `src/app/favorites/favorites.component.ts`

Responsibilities:

- Read the list of favorite IDs from `FavoritesService`.
- Ask `ProductService` for all products.
- Filter products whose IDs are in favorites.
- Display them in a grid, similar to the home page.

You can describe it as:

> "The favorites page reuses the same product cards as the home page, but only for products the user has marked as favorite."

---

## 6.4 Navbar Badge

**File:** `src/app/shared/components/navbar/navbar.component.ts`

The navbar shows:

- **Cart item count** (from a cart or checkout related service).
- **Favorites count** (from `FavoritesService.getFavoriteCount()` or observable).

This gives quick feedback to the user.

Example talking point:

> "Because favorites and cart counts come from services, multiple components stay in sync automatically when the user adds or removes items."
