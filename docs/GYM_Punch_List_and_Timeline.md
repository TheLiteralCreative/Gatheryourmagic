# GatherYourMagic.com
## Development Punch List & Timeline

**Document Type:** Project Management
**Audience:** Project Owner, Tech Lead, Development Team
**Last Updated:** May 25, 2026

---

## Overview

This document translates the full pre-development specification suite into a concrete, sequenced punch list organized by development phase. Each item is a discrete, completable unit of work. Items are ordered by dependency — nothing on the list requires completing a task that hasn't been defined above it.

The timeline assumes a **single full-stack developer** working at a sustainable pace. If a team is assembled, phases can be parallelized and the timeline compresses significantly.

---

## Phase 0: Infrastructure & Environment Setup
**Estimated Duration: 1 Week**
**Goal:** A working local development environment and a deployed skeleton application.

- [ ] Register domain: `gatheryourmagic.com`
- [ ] Set up cloud hosting environment (recommended: Railway, Render, or AWS)
- [ ] Initialize Git repository structure with `/frontend`, `/backend`, `/docs` directories
- [ ] Set up PostgreSQL database instance (local dev + cloud staging)
- [ ] Set up Redis instance for caching layer
- [ ] Initialize backend project (Node.js + Express or equivalent)
- [ ] Initialize frontend project (React + TypeScript + TailwindCSS as PWA)
- [ ] Configure environment variable management (`.env` files, secrets manager)
- [ ] Set up CI/CD pipeline (GitHub Actions: lint, test, deploy on push to `main`)
- [ ] Deploy "Hello World" to production URL — confirm the pipeline works end to end

---

## Phase 1: The Database Foundation
**Estimated Duration: 2 Weeks**
**Goal:** A fully structured, populated local card database. This is the bedrock of everything else.

### 1A: Abstract Multi-Game Schema
- [ ] Create `games` table (top-level entity: MTG, Pokémon, Yu-Gi-Oh!, etc.)
- [ ] Create `sets` table scoped to `game_id`
- [ ] Create `cards` table with abstract core fields (`name`, `game_id`, `set_id`, `image_url`, `rarity`)
- [ ] Create `card_attributes` JSONB column for game-specific fields (MTG: `mana_cost`, `power`, `toughness`; Pokémon: `hp`, `stage`, etc.)
- [ ] Create `card_localizations` table for future multi-language support (`card_id`, `language_code`, `oracle_text`, `flavor_text`)
- [ ] Create `card_legalities` table (card + format + legal status)
- [ ] Create `card_prices` table (card + source + price + timestamp)
- [ ] Write and run database migrations

### 1B: Scryfall Data Sync Pipeline
- [ ] Write the initial bulk data importer: download Scryfall's `default-cards.json` bulk file
- [ ] Parse and map Scryfall fields to the abstract schema
- [ ] Populate `games` table with MTG as `game_id = 1`
- [ ] Populate `sets` table from Scryfall set data
- [ ] Populate `cards` table (all ~30,000 MTG cards)
- [ ] Populate `card_localizations` table (English only at launch)
- [ ] Populate `card_legalities` table from Scryfall format legality data
- [ ] Download and store all card images to local file store (AWS S3 or equivalent)
- [ ] Write the **scheduled sync job**: nightly price updates, weekly legality/ban list checks, triggered sync on new set release
- [ ] Write the **delta-sync logic**: compare checksums, only download changed records
- [ ] Add a `data_sync_log` table to track sync health (last run, status, records updated)

---

## Phase 2: Core API & Authentication
**Estimated Duration: 2 Weeks**
**Goal:** A secure, working backend API with full user authentication and account tiers.

### 2A: Authentication
- [ ] Integrate identity provider (Supabase Auth or Auth0)
- [ ] Implement Email/Password registration and login
- [ ] Implement Google OAuth login
- [ ] Implement Discord OAuth login
- [ ] Issue JWT access tokens with `user_id`, `tier`, and `role` in payload
- [ ] Implement refresh token rotation
- [ ] Write `requireAuth` middleware (protects all private routes)
- [ ] Write `requireTier(minimumTier)` middleware (enforces feature gates)

### 2B: User & Account API
- [ ] `POST /api/auth/register` — Create new user
- [ ] `POST /api/auth/login` — Authenticate user
- [ ] `GET /api/users/me` — Return current user profile and tier
- [ ] `PUT /api/users/me` — Update username, preferences
- [ ] `DELETE /api/users/me` — GDPR-compliant account deletion (cascades to collection, decks)

### 2C: Store Account API
- [ ] `POST /api/stores` — Create a new store (Store Tier only)
- [ ] `GET /api/stores/:slug` — Public storefront data
- [ ] `POST /api/stores/:id/employees` — Add employee to store
- [ ] `DELETE /api/stores/:id/employees/:userId` — Remove employee
- [ ] Implement Kiosk Mode: a session token that grants read-only access to a specific store's inventory

### 2D: Stripe Payment Integration
- [ ] Create Stripe products and prices for Premium, Pro, and Store tiers
- [ ] `POST /api/billing/checkout` — Create Stripe Checkout session
- [ ] `POST /api/billing/portal` — Create Stripe Customer Portal session (for managing subscriptions)
- [ ] `POST /api/webhooks/stripe` — Handle `checkout.session.completed`, `invoice.payment_failed`, `customer.subscription.deleted`
- [ ] Implement graceful downgrade logic (lock collections/decks to read-only, do not delete)

---

## Phase 3: The Library (Card Search & Data)
**Estimated Duration: 1.5 Weeks**
**Goal:** A fast, powerful, fully functional card search experience.

### 3A: Card Search API
- [ ] `GET /api/cards/search?q=` — Full-text search with fuzzy matching (tolerate misspellings)
- [ ] `GET /api/cards/search?color=&type=&cmc=&format=` — Advanced filtered search
- [ ] `GET /api/cards/:id` — Full card detail (text, image, rulings, prices, legalities)
- [ ] Implement autocomplete endpoint (returns card names on first keystroke, <100ms)
- [ ] Implement search result pagination

### 3B: Card Search Frontend
- [ ] Build the global search bar component with live autocomplete
- [ ] Build the Card Detail page (image, Oracle text, rulings, prices, format legality table)
- [ ] Build the Advanced Search filter panel
- [ ] Build the Search Results grid/list view with toggle
- [ ] Ensure all pages are fully responsive (mobile-first)

---

## Phase 4: The Collection (Personal Inventory)
**Estimated Duration: 2 Weeks**
**Goal:** Users can track, manage, and value their personal card collections.

### 4A: Collection API
- [ ] Create `collections` table (`user_id`, `card_id`, `set_id`, `quantity`, `condition`, `is_foil`, `acquired_price`)
- [ ] `POST /api/collection` — Add card to collection
- [ ] `PUT /api/collection/:entryId` — Update quantity, condition, foil status
- [ ] `DELETE /api/collection/:entryId` — Remove card from collection
- [ ] `GET /api/collection` — Return user's full collection with current market values
- [ ] `POST /api/collection/import` — Bulk CSV import (Premium+)
- [ ] `GET /api/collection/export` — Bulk CSV export (Premium+)
- [ ] Enforce 2,000-card limit for Free tier

### 4B: Collection Frontend
- [ ] Build the Collection dashboard (total value, value change over 30 days)
- [ ] Build the Collection grid/list view with sort and filter
- [ ] Build the "Add to Collection" modal (triggered from card detail page)
- [ ] Build the CSV import/export interface
- [ ] Build the "Read-Only" locked state UI for downgraded users

---

## Phase 5: The Strategist (Deck Builder)
**Estimated Duration: 2 Weeks**
**Goal:** Users can build, analyze, and manage decks with real-time format validation.

### 5A: Deck Builder API
- [ ] Create `decks` table (`user_id`, `name`, `format`, `description`)
- [ ] Create `deck_cards` table (`deck_id`, `card_id`, `quantity`, `is_commander`)
- [ ] `POST /api/decks` — Create new deck
- [ ] `PUT /api/decks/:id` — Update deck metadata
- [ ] `DELETE /api/decks/:id` — Delete deck
- [ ] `POST /api/decks/:id/cards` — Add card to deck
- [ ] `DELETE /api/decks/:id/cards/:cardId` — Remove card from deck
- [ ] `GET /api/decks/:id/validate` — Validate deck against format rules (color identity, card limits, banned list)
- [ ] `GET /api/decks/:id/stats` — Return mana curve, type breakdown, owned vs. needed cards
- [ ] Enforce 5-deck limit for Free tier

### 5B: Deck Builder Frontend
- [ ] Build the Deck Builder interface (card search panel + deck list panel, side by side)
- [ ] Build the Mana Curve visualization (bar chart)
- [ ] Build the Card Type breakdown (pie chart)
- [ ] Build the "Owned vs. Needed" checklist
- [ ] Build real-time format violation warnings (red highlight on illegal cards)
- [ ] Build the "Share Deck" public URL feature

---

## Phase 6: The Store Tier (LGS Inventory & Kiosk)
**Estimated Duration: 2 Weeks**
**Goal:** The founding comic shop use case is fully operational.

### 6A: Store Inventory API
- [ ] Create `store_inventory` table (`store_id`, `card_id`, `quantity`, `condition`, `is_foil`, `price`, `physical_location`)
- [ ] `POST /api/stores/:id/inventory` — Add card to store inventory
- [ ] `PUT /api/stores/:id/inventory/:entryId` — Update quantity, price, location
- [ ] `DELETE /api/stores/:id/inventory/:entryId` — Remove card from inventory
- [ ] `GET /api/stores/:id/inventory/search?q=` — Search store's specific inventory
- [ ] `POST /api/stores/:id/inventory/import` — Bulk CSV import for store inventory
- [ ] `GET /api/stores/:id/inventory/export` — Bulk CSV export

### 6B: Kiosk Mode & Public Storefront
- [ ] Implement Kiosk Mode session (read-only, scoped to one store's inventory)
- [ ] Build the Kiosk Mode UI (simplified search-only interface, optimized for touchscreen)
- [ ] Build the Public Storefront page (`/stores/:slug`)
- [ ] Build the "Available Near You" feature on Card Detail pages (cross-reference user location with local stores)

---

## Phase 7: The AI Features (Oracle & Coach)
**Estimated Duration: 3 Weeks**
**Goal:** The platform's primary differentiators are live and functional.

### 7A: AI Infrastructure
- [ ] Set up vector database (Pinecone or pgvector extension in PostgreSQL)
- [ ] Embed all MTG Comprehensive Rules sections as vectors
- [ ] Embed all card Oracle texts as vectors
- [ ] Embed all format rules and ban lists as vectors
- [ ] Write the RAG retrieval function (takes a user query, returns the top-N most relevant rule/card chunks)

### 7B: The Oracle (Rules Referee)
- [ ] `POST /api/oracle/query` — Accept natural language rules question, run RAG pipeline, return answer with cited sources
- [ ] Implement the "I don't know" fallback (if confidence is below threshold, say so)
- [ ] Implement the 5-query-per-day rate limit for Free tier
- [ ] Build the Oracle chat interface on the frontend
- [ ] Display cited rules sections alongside each answer

### 7C: The Coach (Deck Optimizer)
- [ ] `POST /api/coach/optimize` — Accept deck ID + user request, run RAG + Playstyle Profile context, return suggestions
- [ ] Implement collection-awareness (Coach only suggests cards the user owns, unless they ask for acquisition targets)
- [ ] Implement budget filter (Coach respects user-defined budget constraints)
- [ ] Build the Coach interface in the Deck Builder (side panel with suggestions)
- [ ] Build the "Spike Mode" toggle (override Playstyle Profile for competitive advice)

### 7D: The Playstyle Profiler
- [ ] Create `user_profiles` table (psychographic scores, color affinity, archetype preferences, complexity tolerance, budget sensitivity)
- [ ] Build the onboarding assessment (the "Sorting Hat" — 8–10 scenario-based questions)
- [ ] Write the profile scoring algorithm (maps answers to the Timmy/Johnny/Spike + Color Pie model)
- [ ] Implement behavioral tracking (update profile based on cards added, decks built, queries made)
- [ ] Embed user profiles as vectors for similarity matching
- [ ] Build the Profile page (show user their current profile, allow manual adjustments)

---

## Phase 8: Polish, Testing & Launch Prep
**Estimated Duration: 2 Weeks**
**Goal:** The platform is production-ready and the comic shop beta is live.

- [ ] Write end-to-end tests for all critical user flows (registration, collection add, deck build, Oracle query)
- [ ] Write unit tests for all API endpoints
- [ ] Conduct security audit (SQL injection, XSS, CSRF, rate limiting on all public endpoints)
- [ ] Conduct performance audit (all search queries return in <100ms, AI responses in <3s)
- [ ] Add WotC attribution notice to the footer of every page (required by Fan Content Policy)
- [ ] Add AI disclaimer to the Oracle interface ("AI rulings are for reference only. Always verify with the official Comprehensive Rules.")
- [ ] Finalize Terms of Service and Privacy Policy (requires attorney review)
- [ ] Set up error monitoring (Sentry or equivalent)
- [ ] Set up uptime monitoring
- [ ] Onboard the commissioning comic shop as the first Store Tier beta client
- [ ] Conduct a full user acceptance test with the comic shop staff and customers
- [ ] Resolve all issues surfaced during beta
- [ ] **Launch**

---

## Summary Timeline

| Phase | Description | Duration | Cumulative |
| :--- | :--- | :--- | :--- |
| **Phase 0** | Infrastructure & Environment | 1 week | Week 1 |
| **Phase 1** | Database Foundation & Data Sync | 2 weeks | Week 3 |
| **Phase 2** | Core API & Authentication | 2 weeks | Week 5 |
| **Phase 3** | The Library (Card Search) | 1.5 weeks | Week 6.5 |
| **Phase 4** | The Collection (Personal Inventory) | 2 weeks | Week 8.5 |
| **Phase 5** | The Strategist (Deck Builder) | 2 weeks | Week 10.5 |
| **Phase 6** | The Store Tier (LGS & Kiosk) | 2 weeks | Week 12.5 |
| **Phase 7** | AI Features (Oracle, Coach, Profiler) | 3 weeks | Week 15.5 |
| **Phase 8** | Polish, Testing & Launch | 2 weeks | **Week 17.5** |

**Total Estimated Timeline: ~18 weeks (4.5 months) for a single developer.**
With a two-person team (one frontend, one backend), this compresses to approximately **10–12 weeks**.

---

## Minimum Viable Product (MVP) Definition

If a faster path to the comic shop client is needed, the following subset constitutes a shippable MVP that fulfills the original commission:

| Phase | Included in MVP |
| :--- | :--- |
| Phase 0 | Yes — required |
| Phase 1 | Yes — required |
| Phase 2 | Yes — Auth required; Stripe can be deferred |
| Phase 3 | Yes — Card search is core |
| Phase 4 | Partial — Store inventory only; personal collection can follow |
| Phase 5 | No — Deck builder is Phase 2 |
| Phase 6 | Yes — This is the commission |
| Phase 7 | No — AI features are Phase 2 |
| Phase 8 | Partial — Testing and launch required; full polish can follow |

**MVP Timeline: ~8 weeks.** This delivers the searchable store inventory, the Kiosk Mode, and the Public Storefront — everything the comic shop asked for — while the full platform is built out behind it.
