# 8. Presentation Guide (For Your Professor)

This guide tells you **how to present the project** in a clear, structured way.

---

## 8.1 Suggested Demo Flow (5–7 minutes)

1. **Home page**
   - Show the main product grid.
   - Click on a product → open the product details.

2. **Product details + favorites**
   - Explain the `ProductService` briefly.
   - Toggle the favorite heart.
   - Go to `/favorites` and show that the product appears there.

3. **Cart & gift card**
   - Add a product to the cart.
   - Open the cart page.
   - Show how the total is computed.
   - (Optional) Apply a gift card code and show the discount.

4. **Checkout**
   - Open checkout from the cart.
   - Fill the shipping form (mention validation).
   - Submit and show the payment success page.

5. **Find Us**
   - Navigate to the Find Us page.
   - Show the map and mention Leaflet.

---

## 8.2 How to Explain the Architecture

Use simple sentences like:

- "I followed a **feature-based** folder structure: cart, checkout, product, etc."
- "All business logic lives in **services** under `core/services`. Components stay mostly focused on the view."
- "I use **RxJS Observables** and `BehaviorSubject` to share state like products, favorites, and checkout across components."
- "I store cart and favorites in `localStorage` so they survive page refreshes."

Point to these docs when discussing:

- Overall: `docs/01-project-overview.md`
- Navigation: `docs/02-routing-and-layout.md`
- Data flow: `docs/03-products-and-home.md` + `docs/04-cart-and-checkout.md` + `docs/05-gift-cards.md`.

---

## 8.3 Talking About Your Learning

You can say things like:

- "I learned how to use **standalone components** and the new Angular 19 router setup."
- "I practiced structuring an app using **services** and **observables** instead of putting all logic inside components."
- "I implemented a small **state management pattern** using `BehaviorSubject` for products, favorites, and checkout state."
- "I integrated **Tailwind CSS** and **Flowbite** for fast and consistent UI styling."

---

## 8.4 Handling Questions

Common questions your professor might ask:

1. **Why use services instead of putting everything in components?**
   - Answer: "Services make the logic reusable and testable. Components focus on UI."

2. **How do you share data between components?**
   - Answer: "Through services that expose `Observable` streams, like `products$` in `ProductService` or the checkout state in `CheckoutService`."

3. **How is the cart persisted?**
   - Answer: "I serialize the cart to `localStorage` under the key `cart-products`, and I load it on init."

4. **How are gift cards validated?**
   - Answer: "GiftCardService stores purchased cards. CheckoutService uses it to check code validity, marks cards as used, and calculates the discount."

5. **What would you improve next?**
   - Example answers:
     - Real backend instead of localStorage.
     - Authentication + protected routes.
     - More robust input validation and error handling.

Use these answers as a base, and adapt them to your own words.
