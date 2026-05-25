# GatherYourMagic.com
## Technical Brief: Architecture, Stack & Design System

**Audience:** Developers & Designers
**Status:** Pre-Development — Planning Phase
**Last Updated:** May 25, 2026

---

## 1. Platform Overview

GatherYourMagic is a full-stack web application delivering four integrated product surfaces: a card collection manager, an AI-powered deck-building coach, a real-time rules referee, and a metagame analytics dashboard. The platform is designed **API-first**, **multi-game from day one**, and **AI-native** — meaning the intelligence layer is not a bolt-on feature but a core architectural concern.

The primary game is **Magic: The Gathering**. The data model, API integrations, and AI context must be designed to accommodate MTG's extraordinary complexity (30,000+ unique cards, 200+ keyword abilities, 8+ active formats, hundreds of printing variants per card) while remaining extensible to Pokémon TCG, Yu-Gi-Oh!, Disney Lorcana, and others.

---

## 2. System Architecture

The platform follows a **modular monolith** pattern at launch, with clear service boundaries that allow extraction into microservices as traffic demands. The core services are:

```
┌─────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                         │
│         React + TypeScript + TailwindCSS (Vite)             │
│         Mobile-responsive PWA — no native app at launch     │
└────────────────────────┬────────────────────────────────────┘
                         │ REST + WebSocket
┌────────────────────────▼────────────────────────────────────┐
│                        API GATEWAY                          │
│              Node.js / FastAPI (Python)                     │
│         Auth (OAuth2 + JWT) · Rate Limiting · Logging       │
└──────┬──────────────┬──────────────┬───────────────┬────────┘
       │              │              │               │
┌──────▼──────┐ ┌─────▼──────┐ ┌────▼──────┐ ┌─────▼──────┐
│  Collection │ │    Deck     │ │  AI Coach │ │  Metagame  │
│   Service   │ │   Service   │ │  Service  │ │  Service   │
└──────┬──────┘ └─────┬──────┘ └────┬──────┘ └─────┬──────┘
       │              │              │               │
┌──────▼──────────────▼──────────────▼───────────────▼───────┐
│                      DATA LAYER                             │
│   PostgreSQL (relational) · Redis (cache) · Pinecone (vec)  │
└─────────────────────────────────────────────────────────────┘
```

### Service Responsibilities

| Service | Responsibility |
| :--- | :--- |
| **Collection Service** | CRUD for user card collections; variant and condition tracking; portfolio valuation via pricing API |
| **Deck Service** | Deck CRUD; format legality validation; mana curve and statistical analysis; hand simulator |
| **AI Coach Service** | LLM orchestration; deck analysis prompts; rules Q&A (Oracle); RAG pipeline management |
| **Metagame Service** | Tournament data ingestion; format tier lists; matchup win-rate modeling |

---

## 3. External API Integrations

All card data is sourced from established, well-maintained public APIs. No manual card cataloging is required at launch.

| API | Game | Data Provided | Notes |
| :--- | :--- | :--- | :--- |
| **Scryfall API** | MTG | Cards, Oracle text, rulings, images, legalities, sets | Free; gold standard for MTG data; bulk JSON download available |
| **MTG JSON** | MTG | Complete card data in bulk JSON | Ideal for seeding the local database; updated with each set release |
| **Pokémon TCG API (Scrydex)** | Pokémon | Cards, sets, images | Free tier available; JSON format |
| **YGOPRODeck API v7** | Yu-Gi-Oh! | Cards, archetypes, ban lists, prices | Free; rate limit 20 req/sec; download and cache locally |
| **JustTCG API** | MTG, Pokémon, YGO, Lorcana, One Piece | Condition-specific pricing, variant tracking | Paid tiers; 229K+ cards indexed; 6-hour price refresh |
| **TCGplayer API** | All major TCGs | Marketplace pricing, buy/sell integration | Requires partner agreement for transactional use |

**Caching Strategy:** All card data from external APIs should be ingested into the local PostgreSQL database on a scheduled sync (nightly for prices, per-set for card data). The application should never make live API calls to Scryfall or YGOPRODeck in the request path — only to the local cache. This ensures sub-100ms card lookup response times and protects against upstream rate limits.

---

## 4. Data Model (Core Entities)

```
Card
├── id (UUID)
├── game (enum: mtg | pokemon | yugioh | lorcana | ...)
├── oracle_id (string — canonical card identity, game-specific)
├── name (string)
├── type_line (string)
├── mana_cost (string)
├── mana_value (integer)
├── colors (string[])
├── color_identity (string[])
├── oracle_text (text)
├── power / toughness (string, nullable)
├── keywords (string[])
├── set_code (string)
├── collector_number (string)
├── rarity (enum)
├── image_uris (jsonb)
├── legalities (jsonb)
└── prices (jsonb — refreshed nightly)

CardPrinting (one Card → many Printings)
├── id (UUID)
├── card_id (FK → Card)
├── set_code / set_name
├── finish (enum: nonfoil | foil | etched | ...)
├── frame_treatment (enum: normal | borderless | showcase | ...)
└── price_usd / price_usd_foil

User
├── id (UUID)
├── email / username
├── playstyle_profile (jsonb — Color Pie identity, format preferences)
└── subscription_tier (enum: free | enthusiast | champion | store)

CollectionEntry (User owns CardPrinting)
├── user_id (FK)
├── printing_id (FK)
├── quantity
└── condition (enum: M | NM | LP | MP | HP | DMG)

Deck
├── id (UUID)
├── user_id (FK)
├── name / description
├── format (enum: commander | standard | modern | ...)
├── commander_id (FK → Card, nullable)
└── is_public (boolean)

DeckCard (Deck contains Cards)
├── deck_id (FK)
├── card_id (FK)
├── quantity
└── board (enum: main | side | maybe | commander)
```

---

## 5. AI Architecture: The Coach & Oracle

The AI layer is the platform's primary differentiator. It must be **accurate**, **fast**, and **grounded in official data** — general-purpose LLMs hallucinate MTG rules with alarming frequency. The solution is Retrieval-Augmented Generation (RAG).

### 5.1 RAG Pipeline

```
User Query
    │
    ▼
Query Embedding (text-embedding-3-small)
    │
    ▼
Vector Search → Pinecone
    │  (retrieve top-k relevant chunks)
    ▼
Context Assembly
    ├── Relevant card Oracle texts
    ├── Relevant Comprehensive Rules sections
    └── Relevant rulings from Scryfall
    │
    ▼
LLM Prompt (GPT-4o / Claude 3.5 Sonnet)
    │
    ▼
Grounded Response → User
```

### 5.2 Vector Database Content

The Pinecone index should contain embeddings for:

- All MTG card Oracle texts (chunked by card)
- All sections of the MTG Comprehensive Rules (chunked by rule number)
- All official card rulings from Scryfall
- EDHREC-style synergy relationships (pre-computed card-to-card similarity scores)
- Format ban lists and legality notes

### 5.3 Prompt Architecture

Two distinct system prompts govern the two AI modes:

**The Coach** receives: the user's full decklist (as structured JSON), the user's playstyle profile, the target format, and the user's stated goal. It is instructed to evaluate the deck against the structural framework for that format (mana curve, ramp count, draw count, interaction count, win condition count) and return a prioritized list of specific, actionable recommendations — always checking the user's collection first before suggesting purchases.

**The Oracle** receives: the user's plain-English rules question, the relevant card Oracle texts (retrieved via RAG), and the relevant Comprehensive Rules sections. It is instructed to cite its sources and express uncertainty when a ruling is ambiguous, rather than fabricating a confident answer.

### 5.4 Model Selection

| Use Case | Recommended Model | Rationale |
| :--- | :--- | :--- |
| Coach (deck analysis) | GPT-4o or Claude 3.5 Sonnet | Complex reasoning over long context (full decklists) |
| Oracle (rules Q&A) | GPT-4o-mini or Gemini 2.5 Flash | High-frequency, lower-latency queries; cost efficiency |
| Card embeddings | text-embedding-3-small | Fast, cheap, high-quality semantic search |

---

## 6. Frontend Architecture & Design System

### 6.1 Tech Stack

- **Framework:** React 18 + TypeScript
- **Build Tool:** Vite
- **Styling:** TailwindCSS with a custom design token system
- **State Management:** Zustand (lightweight, no boilerplate) + React Query (server state)
- **Routing:** React Router v6
- **Card Images:** Lazy-loaded from Scryfall CDN with local fallback

### 6.2 Design Language

The visual identity should balance the **mystical, high-fantasy aesthetic** of MTG with the **clean, data-dense utility** of a professional tool. Reference points: the dark UI of MTGGoldfish combined with the card-forward visual warmth of Moxfield.

**Color Palette (Design Tokens):**

| Token | Value | Usage |
| :--- | :--- | :--- |
| `--color-bg-base` | `#0f0f13` | Page background — deep near-black |
| `--color-bg-surface` | `#1a1a24` | Cards, panels, modals |
| `--color-bg-elevated` | `#242433` | Hover states, active elements |
| `--color-accent-gold` | `#c9a84c` | Primary CTA, headings, highlights |
| `--color-accent-blue` | `#4a9eff` | Links, interactive elements |
| `--color-text-primary` | `#f0ede8` | Body text — warm off-white |
| `--color-text-muted` | `#8a8799` | Secondary text, labels |
| `--color-mana-white` | `#f9faf4` | MTG White mana symbol |
| `--color-mana-blue` | `#0e68ab` | MTG Blue mana symbol |
| `--color-mana-black` | `#150b00` | MTG Black mana symbol |
| `--color-mana-red` | `#d3202a` | MTG Red mana symbol |
| `--color-mana-green` | `#00733e` | MTG Green mana symbol |

**Typography:** A serif display font (e.g., *Cinzel* or *Playfair Display*) for headings to evoke the fantasy setting, paired with a clean sans-serif (e.g., *Inter* or *DM Sans*) for all data-dense UI elements.

### 6.3 Key UI Components

| Component | Description |
| :--- | :--- |
| `<CardTile>` | The atomic unit. Displays card image, name, mana cost, and price. Hover reveals full Oracle text. |
| `<DeckGrid>` | Visual deck builder. Cards arranged by type/CMC. Drag-and-drop reordering. |
| `<ManaCurveChart>` | Bar chart showing spell count by mana value. Built with Chart.js or Recharts. |
| `<ColorPieChart>` | Donut chart showing color distribution of the deck's mana symbols. |
| `<CoachPanel>` | Sliding right-side panel that displays the AI Coach's analysis and recommendations. |
| `<OracleChat>` | Floating chat interface for real-time rules Q&A. Persists across navigation. |
| `<CollectionSearch>` | Full-featured card search with advanced filter sidebar (type, color, CMC, set, rarity, legality). |
| `<PortfolioSummary>` | Dashboard widget showing total collection value, recent price movements, and top valued cards. |

---

## 7. Development Phases

| Phase | Scope | Target |
| :--- | :--- | :--- |
| **Phase 1 — Foundation** | Database setup, Scryfall/API ingestion pipeline, card search, basic collection CRUD, user auth | Month 1–2 |
| **Phase 2 — Deck Builder** | Visual deck builder, mana curve analysis, format legality, hand simulator | Month 2–3 |
| **Phase 3 — AI Layer** | RAG pipeline, vector DB setup, Coach prompt engineering, Oracle chat interface | Month 3–5 |
| **Phase 4 — Metagame & Social** | Tournament data ingestion, matchup simulator, public deck sharing, trade tools | Month 5–7 |
| **Phase 5 — Multi-Game Expansion** | Pokémon TCG and Yu-Gi-Oh! data integration, game-specific UI adaptations | Month 7–9 |

---

## 8. Non-Functional Requirements

- **Performance:** Card search results < 200ms. AI Coach response < 5 seconds. Page load (LCP) < 2.5 seconds.
- **Scalability:** Architecture must support horizontal scaling of the AI Coach Service independently of the Collection and Deck services.
- **Accessibility:** WCAG 2.1 AA compliance. Mana symbols must include text alternatives for screen readers.
- **Security:** All user collection data encrypted at rest. OAuth2 for authentication. No card pricing data stored beyond 90-day rolling window.
- **Offline Support:** Core collection browsing and deck viewing should function offline via PWA service worker caching.

---

*For the strategic and market context behind these decisions, see:*
[GatherYourMagic Strategic Vision & Research Report](GatherYourMagic_Strategic_Report.md)
*For the investor-facing summary, see:*
[GatherYourMagic Executive Summary](GYM_Executive_Summary.md)
