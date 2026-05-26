# GatherYourMagic.com
## Open Decisions Log

**Document Type:** Project Management
**Audience:** Project Owner, Tech Lead
**Date:** May 25, 2026

---

## 1. Overview

This document tracks the critical architectural and business decisions that have not yet been finalized. A decision must be recorded here, debated, and resolved before development can proceed on the affected feature. Once a decision is made, it is moved to the "Resolved Decisions" section.

---

## 2. Pending Decisions

### Decision 1: Language Support at Launch
- **Context:** MTG has a massive non-English speaking player base (Japan, Germany, Brazil, etc.). Scryfall provides localized card data.
- **The Question:** Do we support multiple languages at launch, or launch English-only and localize later?
- **Impact:** Multi-language support requires a fundamentally different database schema for card text and UI internationalization (i18n) from day one. Retrofitting it later is extremely difficult.
- **Recommendation:** Launch English-only to reduce initial complexity, but build the database schema to support localization (e.g., storing card text in a `card_translations` table rather than directly on the `cards` table).
- **Status:** Pending Owner Approval

### Decision 2: Mobile Strategy
- **Context:** The platform is currently scoped as a responsive web application.
- **The Question:** Do we need a native iOS/Android app, or is a Progressive Web App (PWA) sufficient?
- **Impact:** A native app allows push notifications (good for price alerts) and uses the device camera for card scanning. A PWA is vastly cheaper to develop and maintain.
- **Recommendation:** Build as a mobile-first PWA for Phase 1. Evaluate a native wrapper (e.g., React Native or Capacitor) for Phase 2 if users demand camera scanning.
- **Status:** Pending Owner Approval

### Decision 3: Card Scanning (Computer Vision)
- **Context:** Competitors like TCGplayer have native apps that let users scan cards with their phone camera to add them to their collection.
- **The Question:** Do we build or license a computer vision model for card scanning?
- **Impact:** Building an MTG-specific computer vision model is a massive undertaking. Licensing one (e.g., via an API) is expensive and introduces a live API dependency, violating our local-first data architecture pillar.
- **Recommendation:** Do not include card scanning at launch. Rely on fast, auto-completing text search and bulk CSV uploads for the initial release.
- **Status:** Pending Owner Approval

### Decision 4: Multi-Game Data Schema
- **Context:** The platform intends to support Pokémon, Yu-Gi-Oh!, and Lorcana eventually.
- **The Question:** Do we build the database schema to support multiple games immediately, or build it for MTG and refactor later?
- **Impact:** A multi-game schema is highly abstract (e.g., using a JSONB column for `game_specific_attributes` rather than hardcoding `mana_cost`). It is harder to query but future-proof.
- **Recommendation:** Build the abstract multi-game schema from day one. Hardcoding MTG fields will require a total database rewrite in Phase 3.
- **Status:** Pending Owner Approval

---

## 3. Resolved Decisions

*(This section will be populated as the pending decisions are resolved by the project owner.)*

| Decision | Resolution | Rationale | Date Resolved |
| :--- | :--- | :--- | :--- |
| **Data Architecture** | Local-First Sync | To ensure speed, reduce API costs, and prevent rate-limiting by upstream providers. | May 25, 2026 |
| **Monetization Model** | Freemium (Card Data Free) | To comply with WotC Fan Content Policy while gating value-added AI features. | May 25, 2026 |
