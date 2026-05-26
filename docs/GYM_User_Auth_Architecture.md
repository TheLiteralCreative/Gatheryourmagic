# GatherYourMagic.com
## User Auth & Account Architecture

**Document Type:** Technical Specification
**Audience:** Developers, Project Owner, System Architects
**Date:** May 25, 2026

---

## 1. Overview

Authentication and authorization form the backbone of GatherYourMagic.com. Because the platform serves distinct user personas—ranging from casual free-tier players to local game stores (LGS) managing thousands of dollars in inventory—the account architecture must be robust, secure, and highly flexible.

Crucially, the **Store Tier** is the founding use case of this platform. The system must support a Local Game Store (such as the commissioning comic shop) in managing a chaotic, high-volume card collection while simultaneously allowing their customers and employees to search that inventory seamlessly.

This document defines the authentication strategy, the four-tier account structure, Role-Based Access Control (RBAC), and the specific architectural requirements for the Store Tier.

---

## 2. The Four Account Tiers

GatherYourMagic employs a freemium model with four distinct account tiers. Access to card data is universally free (as required by the WotC Fan Content Policy), while advanced features, AI tools, and inventory management are gated by tier.

### 2.1 The Free Tier (The Casual Player)
- **Cost:** $0
- **Features:** 
  - Unlimited card search and viewing (images, Oracle text, rulings).
  - Basic collection tracking (up to 2,000 cards).
  - Basic deck building (up to 5 decks).
  - Limited queries to the AI Oracle (e.g., 5 per day).
- **Purpose:** Onboard users frictionlessly, comply with WotC policies, and build the user base.

### 2.2 The Premium Tier (The Competitive Grinder)
- **Cost:** ~$4.99/month
- **Features:**
  - Unlimited collection tracking and deck building.
  - Full access to the AI Coach (deck optimization, synergy suggestions).
  - Full access to the Playstyle Profiler.
  - Unlimited AI Oracle queries.
  - Market price alerts and portfolio value tracking.

### 2.3 The Pro Tier (The Investor/Content Creator)
- **Cost:** ~$9.99/month
- **Features:**
  - All Premium features.
  - Advanced portfolio analytics (historical price trends, ROI).
  - API access to their own collection data (for streamers/creators).
  - High-priority AI processing.

### 2.4 The Store Tier (Local Game Store / LGS)
- **Cost:** ~$49.99/month (or custom enterprise pricing)
- **Features:**
  - Massive-scale inventory management (hundreds of thousands of cards).
  - Multi-user access (Owner, Manager, Employee roles).
  - **Customer-Facing Kiosk Mode:** A locked-down interface for in-store customers to search the store's specific inventory.
  - **Public Storefront:** A public GatherYourMagic URL (e.g., gatheryourmagic.com/stores/mycomicshop) where local players can browse the store's inventory from home.
  - CSV/Spreadsheet bulk import/export.

---

## 3. Deep Dive: The Store Tier (The Founding Use Case)

The Store Tier is the genesis of GatherYourMagic. A local comic shop needs to tame a chaotic MTG collection and make it searchable for both employees and customers. The architecture must solve this specific problem elegantly.

### 3.1 Multi-User Store Accounts (RBAC)
A "Store" is an organizational entity, not a single user. The system must support Role-Based Access Control within a Store account:
- **Store Owner:** Full administrative access, billing, and user management.
- **Store Manager:** Can add/remove inventory, adjust prices, and override conditions.
- **Employee:** Can search inventory, log sales/removals, and intake new cards, but cannot delete the entire database or change billing.

### 3.2 Inventory Management vs. Personal Collection
While a personal collection just needs `CardID` and `Quantity`, a Store Inventory requires higher fidelity:
- **Condition:** Mint, Near Mint, Lightly Played, Moderately Played, Heavily Played, Damaged.
- **Foil Status:** Non-Foil, Foil, Etched, etc.
- **Store Location:** E.g., "Showcase A", "Binder 4", "Backroom Box 12". (This is critical for employees trying to physically locate a card a customer just searched for).

### 3.3 The Kiosk Mode
Stores need a way to let customers search the inventory without accessing the management backend.
- **Implementation:** A specific login state or URL parameter that locks the UI into "Search Only" mode. 
- **Behavior:** Customers can search the store's inventory by name, color, type, or mechanic. They see if the card is in stock and its price, but they cannot alter the inventory.

### 3.4 The Local Ecosystem
When a standard user (Free/Premium) searches for a card on GatherYourMagic, the platform can cross-reference their location with local Store Tiers. 
- **Feature:** "Available at a store near you: My Comic Shop has 3 copies in NM condition."
- **Benefit:** This drives foot traffic to the LGS, making the $49.99/month subscription a no-brainer for the store owner.

---

## 4. Authentication Strategy

### 4.1 Identity Provider (IdP)
To reduce security liability and development time, GatherYourMagic should not roll its own cryptographic auth. 
- **Recommendation:** Use **Supabase**, **Auth0**, or **Clerk** for identity management.
- **Methods:** Support Email/Password, Google OAuth, and Discord OAuth (Discord is heavily used by the MTG community).

### 4.2 JWT and Session Management
- **Access Tokens:** Short-lived JWTs (JSON Web Tokens) stored in memory or secure HttpOnly cookies.
- **Refresh Tokens:** Long-lived tokens stored securely to keep users logged in across sessions.
- **Payload:** The JWT payload must include the user's `role` (e.g., `user`, `store_admin`, `store_employee`) to allow the frontend to render the correct UI instantly without an extra database round-trip.

---

## 5. Core Database Schema for Auth & Tiers

```sql
-- Users Table
CREATE TABLE users (
    id UUID PRIMARY KEY, -- Managed by Auth provider
    email VARCHAR(255) UNIQUE NOT NULL,
    username VARCHAR(50) UNIQUE NOT NULL,
    tier VARCHAR(20) DEFAULT 'free', -- 'free', 'premium', 'pro', 'store'
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Stores Table (Organizational Entity)
CREATE TABLE stores (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name VARCHAR(255) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL, -- For public storefront URL
    address TEXT,
    subscription_status VARCHAR(50) DEFAULT 'active'
);

-- Store Employees (Junction Table for RBAC)
CREATE TABLE store_employees (
    store_id UUID REFERENCES stores(id),
    user_id UUID REFERENCES users(id),
    role VARCHAR(50) NOT NULL, -- 'owner', 'manager', 'employee'
    PRIMARY KEY (store_id, user_id)
);

-- Store Inventory (Distinct from Personal Collections)
CREATE TABLE store_inventory (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    store_id UUID REFERENCES stores(id),
    card_id UUID REFERENCES cards(id), -- References the global card database
    quantity INT DEFAULT 1,
    condition VARCHAR(20) DEFAULT 'NM',
    is_foil BOOLEAN DEFAULT FALSE,
    price DECIMAL(10, 2), -- Optional override of market price
    physical_location VARCHAR(100) -- e.g., "Binder 3"
);
```

---

## 6. Security & Compliance

- **Data Isolation:** Store inventory queries must always be strictly scoped by `store_id` to prevent data leakage between competing local game stores.
- **Password Policies:** Enforced by the Auth provider (minimum length, complexity).
- **GDPR/CCPA:** The user table must support soft-deletion and hard-deletion workflows, cascading to remove personal collections, while ensuring that if an employee is deleted, the Store's inventory remains intact.

---

## 7. Next Steps for Development
1. Select the Identity Provider (e.g., Supabase Auth).
2. Implement the Store Inventory data model.
3. Build the Kiosk Mode frontend for the commissioning comic shop as the beta test environment.
