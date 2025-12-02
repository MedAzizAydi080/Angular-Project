# 5. Gift Cards System

Gift cards are one of the most interesting features in this project. This document explains how they are modeled, stored, and used during checkout.

---

## 5.1 Gift Card Model

**File:** `src/app/shared/models/gift-card.ts`

```ts
export interface GiftCard {
  code: string;
  amount: number;
  used: boolean;
  createdAt: string;
}
```

Explain:

- `code`: unique string the user enters at checkout.
- `amount`: value of the card in TND.
- `used`: whether the card has been redeemed.
- `createdAt`: ISO date string when it was created.

Gift cards are stored in `localStorage` under a key like `purchased-gift-cards`.

---

## 5.2 GiftCardService – managing cards

**File:** `src/app/core/services/gift-card.service.ts`

This service handles **creation** and **lookup** of gift cards. Key responsibilities:

- Load existing cards from `localStorage`.
- Add a new card when the user buys one.
- Find a card by `code`.
- Mark a card as `used`.

You can describe it as:

> "GiftCardService abstracts all operations related to gift cards. Components dont touch localStorage directly; they just call this service."

---

## 5.3 Buying Gift Cards

**File:** `src/app/gift-cards/gift-cards.component.ts`

This component lets the user:

- Select or enter an amount.
- Click a button to "purchase" a gift card.
- See a list of previously purchased cards.

Talking points:

- Uses a small form or input for the amount.
- Calls `GiftCardService` to create and persist the card.
- Optionally shows a success message with the new code.

You can say:

> "The gift cards page simulates buying a voucher. It just generates a random code, stores it in localStorage, and shows it to the user."

---

## 5.4 Applying Gift Cards in Checkout

**File:** `src/app/core/services/checkout.service.ts`

CheckoutService exposes a method like:

```ts
applyGiftCard(code: string): Observable<{ success: boolean; message: string }>
```

Internally it:

1. Asks `GiftCardService` for a card matching the code.
2. Checks if it exists and is not `used`.
3. Calculates how much can be applied (cap at current total).
4. Saves the applied card to its internal `CheckoutState` and to `localStorage`.
5. Returns a message that the UI shows.

The cart and checkout components both call this method to apply or remove a gift card.

Example from `CartComponent`:

```ts
this.checkoutService.applyGiftCard(this.giftCardCode.trim()).subscribe({
  next: (result) => {
    this.giftCardMessage = {
      type: result.success ? 'success' : 'error',
      text: result.message,
    };
    if (result.success) {
      this.giftCardCode = '';
      this.loadAppliedGiftCard();
    }
  },
});
```

---

## 5.5 One-Time Use and Persistence

The design ensures that:

- A gift card **cannot be reused** once marked as `used`.
- Applied gift card survives page refresh because it is stored in `localStorage` (e.g., key `applied-gift-card`).
- When payment is processed, `CheckoutService` can mark the card as used and clear checkout state.

You can summarize:

> "Gift cards are modeled as simple objects and persisted in localStorage. The CheckoutService coordinates applying them, validating the code, and ensuring they are one-time use."
