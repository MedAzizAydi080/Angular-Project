# 4. Cart & Checkout Flow

This document explains how the cart works, how totals are calculated, and how the checkout form + gift card integration are implemented.

---

## 4.1 Cart Data Model

**File:** `src/app/shared/models/cart-product.ts`

```ts
export interface CartProduct {
  product: Product;
  quantity: number;
}
```

- The cart is simply a list of `CartProduct` objects.
- Each entry stores:
  - The `product` itself
  - The selected `quantity`

The cart is stored in `localStorage` under the key **`cart-products`**.

---

## 4.2 CartComponent – totals & gift card

**File:** `src/app/cart/cart.component.ts`

```ts
@Component({
  selector: 'app-cart',
  imports: [CartProductComponent, TndCurrencyPipe, RouterLink, FormsModule],
  templateUrl: './cart.component.html',
})
export class CartComponent implements OnInit {
  private checkoutService = inject(CheckoutService);

  cartProducts: CartProduct[] = [];
  total = 0;

  giftCardCode = '';
  isApplyingGiftCard = false;
  giftCardMessage: { type: 'success' | 'error'; text: string } | null = null;
  appliedGiftCard: AppliedGiftCard | null = null;
  giftCardDiscount = 0;
  finalTotal = 0;

  ngOnInit(): void {
    this.updateCart();
    this.loadAppliedGiftCard();
  }

  private loadAppliedGiftCard(): void {
    this.appliedGiftCard = this.checkoutService.getAppliedGiftCard();
    this.calculateFinalTotal();
  }

  private calculateFinalTotal(): void {
    if (this.appliedGiftCard) {
      this.giftCardDiscount = Math.min(this.appliedGiftCard.amount, this.total);
      this.finalTotal = Math.max(0, this.total - this.giftCardDiscount);
    } else {
      this.giftCardDiscount = 0;
      this.finalTotal = this.total;
    }
  }

  updateCart() {
    const storagedProducts: CartProduct[] =
      JSON.parse(localStorage.getItem('cart-products') as string) || [];

    this.cartProducts = storagedProducts;

    if (this.cartProducts.length > 0) {
      this.total = this.cartProducts.reduce(
        (acc, val) => acc + val.product.price * val.quantity,
        0
      );
    } else {
      this.total = 0;
    }

    this.calculateFinalTotal();
  }

  applyGiftCard(): void {
    if (!this.giftCardCode.trim()) return;

    this.isApplyingGiftCard = true;
    this.giftCardMessage = null;

    this.checkoutService.applyGiftCard(this.giftCardCode.trim()).subscribe({
      next: (result) => {
        this.isApplyingGiftCard = false;
        this.giftCardMessage = {
          type: result.success ? 'success' : 'error',
          text: result.message,
        };
        if (result.success) {
          this.giftCardCode = '';
          this.loadAppliedGiftCard();
        }
      },
      error: () => {
        this.isApplyingGiftCard = false;
        this.giftCardMessage = {
          type: 'error',
          text: 'Failed to apply gift card. Please try again.',
        };
      },
    });
  }

  removeGiftCard(): void {
    this.checkoutService.removeGiftCard();
    this.appliedGiftCard = null;
    this.giftCardMessage = null;
    this.calculateFinalTotal();
  }
}
```

Explain in simple words:

- On init, it **loads the cart from localStorage** and calculates the `total`.
- It also checks if there is an already applied gift card.
- `calculateFinalTotal()` adjusts the final total using the gift card amount.
- `applyGiftCard()` calls `CheckoutService.applyGiftCard(code)` which returns an observable with success/error and message.
- `removeGiftCard()` clears the gift card from checkout state.

---

## 4.3 Checkout Service (state & payment)

**File:** `src/app/core/services/checkout.service.ts`

Focus on these ideas when you read the file:

- It holds a `CheckoutState` with:
  - `cartProducts`
  - `shippingInfo`
  - `appliedGiftCard`
  - `totals` (subtotal, discount, final total, etc.)
- It exposes `state$` as an `Observable<CheckoutState>`.
- It knows how to:
  - Load cart products from `localStorage`.
  - Apply/remove a gift card by talking to `GiftCardService`.
  - Recalculate totals.
  - Simulate payment and then navigate to `PaymentSuccess`.

You can describe it as:

> "CheckoutService is a central piece that connects the cart, gift cards, and payment success. It holds all checkout state in one place."

---

## 4.4 CheckoutComponent – reactive form

**File:** `src/app/checkout/checkout.component.ts`

```ts
@Component({
  selector: 'app-checkout',
  imports: [CommonModule, ReactiveFormsModule, FormsModule, TndCurrencyPipe, RouterLink],
  templateUrl: './checkout.component.html',
})
export class CheckoutComponent implements OnInit {
  private fb = inject(FormBuilder);
  private router = inject(Router);
  checkoutService = inject(CheckoutService);

  state$: Observable<CheckoutState> = this.checkoutService.state$;
  shippingForm!: FormGroup;
  currentStep = 1;

  giftCardCode = '';
  isApplyingGiftCard = false;
  giftCardMessage: { type: 'success' | 'error'; text: string } | null = null;

  ngOnInit(): void {
    this.checkoutService.loadCartProducts();
    this.initShippingForm();

    const state = this.checkoutService.getState();
    if (state.cartProducts.length === 0) {
      this.router.navigate(['/cart']);
    }
  }

  private initShippingForm(): void {
    this.shippingForm = this.fb.group({
      fullName: ['', [Validators.required, Validators.minLength(2)]],
      email: ['', [Validators.required, Validators.email]],
      phone: ['', [Validators.required, Validators.pattern(/^[\d\s\-+()]+$/)]],
      address: ['', [Validators.required, Validators.minLength(5)]],
      city: ['', [Validators.required]],
      state: ['', [Validators.required]],
      zipCode: ['', [Validators.required, Validators.pattern(/^\d{5}(-\d{4})?$/)]],
      country: ['United States', [Validators.required]],
    });
  }

  onSubmitShipping(): void {
    if (this.shippingForm.valid) {
      const shippingInfo: ShippingInfo = this.shippingForm.value;
      this.checkoutService.setShippingInfo(shippingInfo);
      this.currentStep = 2;
    } else {
      this.markFormGroupTouched(this.shippingForm);
    }
  }

  processPayment(): void {
    this.checkoutService.processPayment().subscribe();
  }

  applyGiftCard(): void {
    // very similar to CartComponent.applyGiftCard()
  }
}
```

Talking points:

- Uses **Reactive Forms** via `FormBuilder` and `FormGroup`.
- Input validation: required fields, email pattern, phone pattern, ZIP code pattern.
- Two steps: shipping info (step 1) and payment (step 2).
- Before showing checkout, it checks that the cart is not empty and redirects if needed.

Explain it like this:

> "CheckoutComponent is responsible for the UI of the shipping form and payment step. It delegates all business logic to `CheckoutService` so the component code stays clean."
