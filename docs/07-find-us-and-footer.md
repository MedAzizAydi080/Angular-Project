# 7. Find Us Page & Footer

This document covers the informational parts of the app: the map section and the footer.

---

## 7.1 FindUsComponent – Map & Contact

**File:** `src/app/find-us/find-us.component.ts`

This component shows:

- A map centered on a Tunisian location (using Leaflet).
- Basic store contact information and opening hours.

Key ideas to explain:

- The map is initialized in `ngOnInit`.
- It uses the `L.map` and `L.tileLayer` APIs from Leaflet.
- A marker is placed at the store coordinates.

You can summarize:

> "FindUsComponent gives a more realistic feeling to the store by showing a map with our location and contact details."

---

## 7.2 FooterComponent – Layout & Messaging

**File:** `src/app/shared/components/footer/footer.component.html`

The footer:

- Shows a brief description of the shop.
- States that **shipping is available across Tunisia**.
- Contains quick links or placeholders for policies and social media.

This is mostly static content, but it helps you show that you took care of the whole user experience, not only core logic.
