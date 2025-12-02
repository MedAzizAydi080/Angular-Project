# 2. Routing & Layout Shell

This file explains how the application navigates between pages and how the global layout (navbar + router + footer) is built.

---

## 2.1 Route Definitions

**File:** `src/app/app.routes.ts`

```ts
import { Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { CartComponent } from './cart/cart.component';
import { ProductComponent } from './product/product.component';
import { PaymentSuccessComponent } from './payment/payment-success/payment-success.component';
import { CheckoutComponent } from './checkout/checkout.component';
import { GiftCardsComponent } from './gift-cards/gift-cards.component';
import { SignInComponent } from './auth/sign-in/sign-in.component';
import { SignUpComponent } from './auth/sign-up/sign-up.component';
import { FavoritesComponent } from './favorites/favorites.component';
import { FindUsComponent } from './find-us/find-us.component';

export const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'cart', component: CartComponent },
  { path: 'products/:id', component: ProductComponent },
  { path: 'checkout', component: CheckoutComponent },
  { path: 'gift-cards', component: GiftCardsComponent },
  { path: 'favorites', component: FavoritesComponent },
  { path: 'PaymentSuccess', component: PaymentSuccessComponent },
  { path: 'sign-in', component: SignInComponent },
  { path: 'sign-up', component: SignUpComponent },
  { path: 'find-us', component: FindUsComponent },
];
```

How to explain:

- The app uses **path-based routing**.
- `/` → `HomeComponent` (home page with product list and offers).
- `/products/:id` → `ProductComponent` for a specific product.
- `/cart` → `CartComponent` (cart page).
- `/checkout` → `CheckoutComponent` (shipping + payment).
- `/gift-cards` → `GiftCardsComponent` (buy gift cards).
- `/favorites` → `FavoritesComponent` (wishlist page).
- `/PaymentSuccess` → `PaymentSuccessComponent` after checkout.
- `/sign-in` & `/sign-up` → auth forms.
- `/find-us` → map and contact section.

You can say:
> "Routing is centralized in `app.routes.ts`. Each path points to a standalone component which renders that page."

---

## 2.2 Root Shell – AppComponent

**Files:**
- `src/app/app.component.ts`
- `src/app/app.component.html`

The root component defines the main shell:

```html
<!-- app.component.html -->
<app-navbar></app-navbar>

<main class="bg-white min-h-screen">
  <router-outlet></router-outlet>
</main>

<app-footer></app-footer>
```

Key idea:

- `app-navbar` and `app-footer` are always visible.
- `<router-outlet>` is where the active route component is rendered.

In TypeScript (simplified):

```ts
@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, NavbarComponent, FooterComponent],
  templateUrl: './app.component.html',
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    initFlowbite(); // enables Flowbite UI behavior
  }
}
```

Talking point:

> "The root component is very thin. It just wires the layout and initializes Flowbite for the UI components. All the real logic happens in feature components and services."

---

## 2.3 Navbar & Navigation

**File:** `src/app/shared/components/navbar/navbar.component.ts`

This component:

- Shows the logo and navigation links.
- Shows **cart item count** and **favorites count**.
- Contains the **search input** that filters products.

Look at the imports (simplified):

```ts
@Component({
  selector: 'app-navbar',
  standalone: true,
  imports: [CommonModule, RouterLink, FormsModule, TndCurrencyPipe],
  templateUrl: './navbar.component.html',
})
export class NavbarComponent implements OnInit {
  // subscribes to favorites + cart services
}
```

When presenting, highlight:

- Navbar is reusable and standalone.
- It interacts with services to show live counts.
- It uses Angular Router links (`routerLink`) to navigate.

---

## 2.4 Footer

**File:** `src/app/shared/components/footer/footer.component.html`

The footer contains:

- Short description of the store.
- Information about **shipping in Tunisia**.
- Social / contact placeholders.

You can mention it briefly as part of the layout and focus more on the functional features in later sections.
