# GatherYourMagic.com
## Open Decisions Log

**Document Type:** Project Management
**Audience:** Project Owner, Tech Lead
**Last Updated:** May 25, 2026

---

## 1. Overview

This document tracks the critical architectural and business decisions that have not yet been finalized. A decision must be recorded here, debated, and resolved before development can proceed on the affected feature. Once a decision is made, it is moved to the "Resolved Decisions" section.

---

## 2. Pending Decisions

*All previously pending decisions have been resolved. No items are currently pending.*

---

## 3. Resolved Decisions

| Decision | Resolution | Rationale | Date Resolved |
| :--- | :--- | :--- | :--- |
| **Data Architecture** | Local-First Sync | To ensure speed, reduce API costs, and prevent rate-limiting by upstream providers. | May 25, 2026 |
| **Monetization Model** | Freemium (Card Data Free) | To comply with WotC Fan Content Policy while gating value-added AI features. | May 25, 2026 |
| **Language Support at Launch** | English-only at launch. Build the database schema with a `card_translations` table to support future localization without a full rewrite. | Reduces initial complexity while keeping the door open for international expansion. | May 25, 2026 |
| **Mobile Strategy** | Responsive PWA (Progressive Web App). No native iOS/Android app for Phase 1. | A PWA is significantly cheaper to develop and maintain. Native app can be evaluated in Phase 2 based on user demand. | May 25, 2026 |
| **Card Scanning (Computer Vision)** | Out of scope for Phase 1. Rely on fast auto-completing text search and bulk CSV import. | Building or licensing a CV model is a major undertaking and would introduce a live API dependency, violating the local-first data architecture pillar. | May 25, 2026 |
| **Multi-Game Data Schema** | Build an abstract, game-agnostic schema from day one, using Magic: The Gathering as the primary case study to validate the model. | Hardcoding MTG-specific fields would require a total database rewrite when Pokémon, Yu-Gi-Oh!, and Lorcana are added. Abstract from the start. | May 25, 2026 |

---

## 4. Architectural Implications of Resolved Decisions

The four decisions made on May 25, 2026 have the following direct impact on the development plan:

**Language Support:** The `cards` table must **not** store Oracle text directly as a column. Instead, all localized text must live in a `card_localizations` table with a `language_code` column. The application queries this table with `language_code = 'en'` at launch. Adding a new language later requires only a data import, not a schema change.

**PWA:** The frontend must be built **mobile-first**. All layouts, touch targets, and navigation patterns must be designed for a phone screen first, then scaled up to desktop. The application must register a Service Worker to enable offline access to a user's collection.

**No Card Scanning:** The card search experience must be **exceptionally fast and forgiving**. Auto-complete must trigger on the first keystroke, tolerate minor misspellings (fuzzy search), and return results in under 100ms. This is the primary card-entry mechanism and must be treated as a first-class feature.

**Abstract Multi-Game Schema:** The core data model must use a `games` table as the top-level entity. Cards, sets, formats, and mechanics must all be scoped to a `game_id`. MTG is `game_id = 1`. All queries must be written to include `game_id` as a filter from the first line of code.
