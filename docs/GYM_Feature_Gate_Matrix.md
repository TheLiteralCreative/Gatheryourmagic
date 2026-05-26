# GatherYourMagic.com
## Feature Gate Matrix & Payment Integration

**Document Type:** Technical Specification
**Audience:** Backend Developers, Product Managers
**Date:** May 25, 2026

---

## 1. Overview

This document defines exactly which features are available to which user tiers and specifies how the payment integration (Stripe) will enforce these gates technically. It bridges the gap between the business model and the backend code.

---

## 2. The Feature Gate Matrix

The following table dictates the authorization logic for every major feature on the platform.

| Feature / Capability | Free ($0) | Premium ($4.99/mo) | Pro ($9.99/mo) | Store ($49.99/mo) |
| :--- | :--- | :--- | :--- | :--- |
| **Card Data Access** |
| Card Search & Viewing | Unlimited | Unlimited | Unlimited | Unlimited |
| Oracle Text & Rulings | Unlimited | Unlimited | Unlimited | Unlimited |
| High-Res Image Viewing | Unlimited | Unlimited | Unlimited | Unlimited |
| **Collection & Inventory** |
| Personal Collection Limit | 2,000 cards | Unlimited | Unlimited | Unlimited |
| Store Inventory Limit | N/A | N/A | N/A | Unlimited |
| Multi-User Inventory Access | No | No | No | Yes (Owner/Manager/Employee) |
| Condition & Location Tracking | No | No | No | Yes |
| Bulk CSV Import/Export | No | Yes | Yes | Yes |
| **Deck Building** |
| Deck Limit | 5 Decks | Unlimited | Unlimited | Unlimited |
| Mana Curve & Stats | Yes | Yes | Yes | Yes |
| **AI Features** |
| The Oracle (Rules Referee) | 5 queries/day | Unlimited | Unlimited | Unlimited |
| The Coach (Deck Optimization) | No | Yes | Yes | Yes |
| Playstyle Profiler | Baseline Only | Full Evolving | Full Evolving | Full Evolving |
| High-Priority AI Processing | No | No | Yes | Yes |
| **Market & Analytics** |
| Current Market Prices | Yes | Yes | Yes | Yes |
| Price Spike Alerts | No | Yes | Yes | Yes |
| Historical Portfolio ROI | No | No | Yes | Yes |
| **Store Specific** |
| Public Storefront URL | No | No | No | Yes |
| In-Store Kiosk Mode | No | No | No | Yes |

---

## 3. Payment Integration Architecture (Stripe)

GatherYourMagic will use **Stripe Checkout** and **Stripe Billing** to handle all subscription management. The platform must never store raw credit card numbers.

### 3.1 Stripe Webhooks
The backend must expose a secure webhook endpoint (e.g., `/api/webhooks/stripe`) to listen for critical events from Stripe:
- `checkout.session.completed`: User successfully upgraded. Update the `users` or `stores` table to the new tier.
- `invoice.payment_succeeded`: Recurring payment cleared. Extend the subscription expiration date.
- `invoice.payment_failed`: Payment failed. Downgrade the user to the Free tier (or trigger a grace period).
- `customer.subscription.deleted`: User canceled. Downgrade to Free tier at the end of the billing cycle.

### 3.2 Enforcing the Gates (Backend Middleware)
Feature gating must be enforced on the backend, not just hidden in the frontend UI. 

**Example Implementation (Node.js/Express Middleware):**
```javascript
const requireTier = (minimumTier) => {
  const tierHierarchy = { 'free': 0, 'premium': 1, 'pro': 2, 'store': 3 };
  
  return (req, res, next) => {
    const userTier = req.user.tier; // Injected by Auth middleware
    
    if (tierHierarchy[userTier] >= tierHierarchy[minimumTier]) {
      next(); // Authorized
    } else {
      res.status(403).json({ 
        error: 'Upgrade required', 
        message: `This feature requires a ${minimumTier} subscription.` 
      });
    }
  };
};

// Route protection example
app.post('/api/ai/coach/optimize', requireTier('premium'), optimizeDeckController);
```

### 3.3 Handling Downgrades (Graceful Degradation)
When a user cancels a Premium subscription and reverts to Free, the platform must handle their data gracefully:
- **Collections over 2,000 cards:** Do not delete the cards. Instead, lock the collection in a "Read-Only" state until they either delete cards to get under the limit or re-subscribe.
- **Decks over 5:** Lock the excess decks (e.g., the most recently created ones) in "Read-Only" mode.

---

## 4. Summary
This matrix provides the exact logic required for developers to implement authorization checks across the platform, ensuring that the business model is technically enforced while maintaining compliance with WotC's requirement that card data remain free.
