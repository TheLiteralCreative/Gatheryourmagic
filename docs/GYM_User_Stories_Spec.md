# GatherYourMagic.com
## User Stories & Feature Specification

**Document Type:** Product Specification
**Audience:** Frontend Developers, Backend Developers, UI/UX Designers
**Date:** May 25, 2026

---

## 1. Overview

This document translates the strategic vision of GatherYourMagic into concrete, testable User Stories. It serves as the primary blueprint for the development team, defining exactly how the platform must behave from the perspective of its users.

The stories are organized by the platform's core pillars, with a dedicated section for the Store Tier, which serves as the founding use case.

---

## 2. Pillar 1: The Library (Card Data & Search)

The Library is the foundation. It must be fast, accurate, and completely free to access.

### 2.1 Basic Search
- **As a user**, I want to search for a card by its exact name, so that I can quickly view its details.
- **As a user**, I want the search bar to auto-complete card names as I type, so that I don't have to remember exact spellings.
- **As a user**, I want to see the high-resolution image of the card, its Oracle text, and its current market price on the card details page.

### 2.2 Advanced Search
- **As a user**, I want to filter cards by color, mana value, card type, and format legality, so that I can find specific cards for my deck.
- **As a user**, I want to search for cards that contain specific rules text (e.g., "draw a card"), so that I can find mechanical synergies.

---

## 3. Pillar 2: The Collection (Inventory Management)

This pillar handles personal collections for Free, Premium, and Pro users.

### 3.1 Adding Cards
- **As a user**, I want to add a card to my collection directly from the search results or card details page.
- **As a user**, I want to specify the printing (set), condition, and foil status of the card I am adding, so that my collection value is accurate.
- **As a Premium user**, I want to upload a CSV file of my collection, so that I don't have to enter thousands of cards manually.

### 3.2 Viewing and Organizing
- **As a user**, I want to view my collection as a visual grid of card images or as a dense list, depending on my preference.
- **As a user**, I want to sort my collection by total value, color, or set.
- **As a Premium user**, I want to see a dashboard showing the total market value of my collection and how it has changed over the last 30 days.

---

## 4. Pillar 3: The Strategist (Deck Building)

The deck builder integrates closely with the AI Coach.

### 4.1 Deck Creation
- **As a user**, I want to create a new deck and assign it a format (e.g., Commander, Standard).
- **As a user**, I want to designate one or more cards as my Commander, which dictates the color identity of the deck.
- **As a user**, I want the system to warn me if I add a card that is illegal in my chosen format or outside my Commander's color identity.

### 4.2 Deck Analysis
- **As a user**, I want to see a visual breakdown of my deck's mana curve, so that I can ensure I have early-game plays.
- **As a user**, I want to see a breakdown of my deck by card type (Creatures, Instants, Lands).
- **As a user**, I want to see which cards in my deck I already own in my collection, and which ones I need to acquire.

---

## 5. Pillar 4: The Coach & The Oracle (AI Features)

These features represent the primary value-add of the paid tiers.

### 5.1 The Oracle (Rules Referee)
- **As a user**, I want to ask a natural-language question about how two specific cards interact.
- **As a user**, I want the Oracle to cite the specific sections of the Comprehensive Rules it used to answer my question.
- **As a user**, I want the Oracle to explicitly state if it is unsure, rather than guessing a ruling.

### 5.2 The Coach (Deck Optimization)
- **As a Premium user**, I want to ask the Coach to suggest cuts for my deck because I have 105 cards and need to get down to 100.
- **As a Premium user**, I want the Coach to suggest additions to my deck that fit my specific budget constraints.
- **As a Premium user**, I want the Coach's recommendations to be filtered through my Playstyle Profile, so that it suggests cards I will actually enjoy playing.

---

## 6. The Store Tier (LGS Inventory & Kiosk)

This section defines the requirements for the founding comic shop use case.

### 6.1 Store Inventory Management
- **As a Store Manager**, I want to add cards to the store inventory, specifying condition, foil status, and physical location (e.g., "Showcase A").
- **As a Store Manager**, I want to override the default market price with my own custom price.
- **As a Store Employee**, I want to quickly decrement the quantity of a card when it is sold at the register.

### 6.2 The In-Store Kiosk
- **As a Store Owner**, I want to lock a tablet or computer into "Kiosk Mode", so that customers can search the inventory but cannot access the management backend.
- **As a Customer using the Kiosk**, I want to search for a card and immediately see if the store has it in stock, what condition it is in, and its price.
- **As a Customer using the Kiosk**, I want to see the physical location of the card (e.g., "Ask an employee for Binder 3") so that I can easily purchase it.

### 6.3 The Public Storefront
- **As a Store Owner**, I want a public URL (e.g., gatheryourmagic.com/stores/mycomicshop) where local players can browse my inventory from home.
- **As a Local Player**, I want to see a notification on a card's detail page if a local Store Tier subscriber currently has that card in stock.

---

## 7. Acceptance Criteria

A feature is not considered complete until it meets the following criteria:
1. It functions exactly as described in the User Story.
2. It respects the Feature Gate Matrix (i.e., Premium features cannot be accessed by Free users).
3. It performs without making live calls to external APIs in the request path (relying on the local database cache).
4. It is fully responsive and usable on both desktop and mobile devices.
