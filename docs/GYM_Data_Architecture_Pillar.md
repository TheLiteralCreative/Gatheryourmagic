# GatherYourMagic.com
## Foundational Pillar: Local-First Data Architecture & Sync Strategy

**Document Type:** Architectural Principle — Non-Negotiable
**Audience:** All stakeholders — developers, designers, product owners, investors
**Status:** Ratified
**Date:** May 25, 2026

---

## Statement of Principle

> **GatherYourMagic owns its data. It does not rent it.**

Every piece of card data, every image, every rule, and every pricing record that the platform serves to its users is stored in GatherYourMagic's own infrastructure. External services are treated as **upstream data sources**, not as live dependencies. The platform synchronizes with those sources on a defined schedule. It never calls them in real time during a user's session.

This is not a performance optimization. It is a foundational architectural decision that determines the platform's speed, resilience, cost structure, independence, and long-term viability. It is established here, at the beginning of the project, so that every subsequent technical decision is made in alignment with it.

---

## Why This Principle Exists

### The Problem with Live API Dependency

A platform that calls external APIs in real time during a user's session is, by definition, only as reliable as the least reliable service it depends on. Consider what happens when GatherYourMagic makes a live call to Scryfall every time a user searches for a card:

- If Scryfall is slow, GatherYourMagic is slow.
- If Scryfall is down, GatherYourMagic is down.
- If Scryfall changes its API terms, rate limits, or pricing, GatherYourMagic must adapt immediately or break.
- If GatherYourMagic grows rapidly and sends high traffic to Scryfall, Scryfall may throttle or block the platform's IP address.
- Every single card search costs a network round-trip, adding 100–500ms of latency that the user experiences as sluggishness.

None of these are hypothetical risks. They are the documented, lived experience of every platform that has built on live API dependency. Scryfall itself explicitly warns developers in its API documentation: *"We ask that you cache aggressively."* [[1]](#ref-1) This is not a suggestion — it is an acknowledgment that Scryfall cannot and should not be treated as a real-time database for third-party applications.

### The Strategic Case for Data Ownership

Beyond the technical risks, there is a business and strategic case for data ownership that must be stated plainly:

**Dependency is leverage in the wrong direction.** If GatherYourMagic's core functionality depends on Scryfall's continued free access, then Scryfall holds leverage over this platform. If Scryfall were acquired, shut down, or decided to monetize API access, GatherYourMagic would face an existential crisis. Data ownership eliminates that leverage entirely.

**Speed is a product feature.** A card search that returns in 20 milliseconds feels magical. A card search that returns in 400 milliseconds because it is waiting for a network response feels broken. Users do not think about API latency — they think "this app is slow." Local data makes the platform feel fast in a way that no amount of UI polish can replicate.

**The data is not proprietary in a way that prevents this.** Scryfall provides bulk data downloads specifically for this purpose [[1]](#ref-1). The MTG Comprehensive Rules are published publicly by Wizards of the Coast [[2]](#ref-2). YGOPRODeck explicitly asks developers to download and store data locally rather than making repeated API calls [[3]](#ref-3). The upstream sources are not only permitting this approach — they are actively recommending it.

---

## The Data Taxonomy

Not all data is the same. The sync strategy must reflect the actual change frequency of each data type. Treating all data as equally volatile (and therefore syncing everything constantly) wastes resources. Treating all data as equally static (and therefore never syncing) leads to stale information. The correct approach is to categorize data by its natural rate of change and sync accordingly.

| Data Category | Examples | Change Frequency | Sync Strategy | Source |
| :--- | :--- | :--- | :--- | :--- |
| **Card Identity** | Name, Oracle text, card type, mana cost, power/toughness, keywords | Rare — only on errata, roughly a few times per year across all cards | Triggered on set release + monthly integrity check | Scryfall Bulk JSON |
| **Card Images** | Card artwork, frame, set symbol | Essentially never — a printed card's image is permanent | One-time download per printing; stored permanently in local file store | Scryfall Image CDN |
| **Set & Printing Data** | Set name, collector number, rarity, finish (foil/nonfoil), frame treatment | New sets release approximately every 3 months | Triggered sync on set release announcement | Scryfall Bulk JSON |
| **Format Legality** | Standard/Modern/Commander legal status, ban list status | Occasional — ban announcements occur a few times per year | Weekly lightweight check + event-triggered on known announcement dates | Scryfall Legalities endpoint |
| **Market Prices** | TCGplayer price, Cardmarket price, condition-specific values | Daily — market prices fluctuate continuously | Nightly scheduled sync, every 24 hours | JustTCG API / TCGplayer |
| **Official Rules** | MTG Comprehensive Rules, errata rulings | Rare — updated with each set release | Triggered sync on set release | Wizards of the Coast PDF |
| **Community Strategy Data** | Popular Commander synergies, metagame tier lists, tournament results | Continuous — the metagame shifts weekly | Weekly sync; displayed with a "freshness" timestamp | EDHREC, MTGGoldfish |
| **Pokémon TCG Card Data** | Card text, HP, attacks, energy types | Per-set release | Triggered on set release | Pokémon TCG API (Scrydex) |
| **Yu-Gi-Oh! Card Data** | Card text, ATK/DEF, archetypes, ban list | Per-set release + ban list updates | Triggered on set release; weekly ban list check | YGOPRODeck API |

---

## The Sync Architecture

The sync system is a background infrastructure concern, entirely invisible to users. It consists of three components:

### Component 1: The Scheduler

A job scheduler (implemented using a tool such as Celery with Redis, or a cloud-native equivalent like AWS EventBridge) manages the timing of all sync operations. Each sync job is registered with a schedule:

```
SYNC JOBS
│
├── nightly_price_sync         → runs every night at 2:00 AM UTC
├── weekly_legality_check      → runs every Monday at 3:00 AM UTC
├── weekly_strategy_sync       → runs every Sunday at 4:00 AM UTC
├── set_release_sync           → triggered manually by admin on set release day
└── monthly_integrity_check    → runs on the 1st of each month, compares
                                  local card checksums against Scryfall bulk data
```

### Component 2: The Delta Engine

The monthly integrity check and set release sync do not re-download the entire Scryfall database. They use a **delta strategy**:

1. Download the Scryfall bulk JSON (approximately 300MB for all MTG cards).
2. Compare each card's `updated_at` timestamp against the locally stored version.
3. Update only the records that have changed.
4. Log all changes for audit purposes (e.g., "Card X received an errata on this date").

This means that after the initial setup, most monthly integrity checks will result in zero or very few updates — confirming that the local database is accurate without wasting bandwidth or processing time.

### Component 3: The Health Monitor

Every sync job must report its outcome. A dedicated monitoring dashboard (visible to platform administrators) displays:

- The last successful run time for each sync job.
- The number of records updated in the last run.
- Any errors or failures, with alerts sent to the development team.
- A "Data Freshness" indicator visible to users on the platform (e.g., "Prices last updated: 6 hours ago").

**Critically:** If a sync job fails silently — completes without error but produces incorrect data — the platform must have a way to detect this. The monthly integrity check against Scryfall's checksums serves as the safety net.

---

## Storage Architecture

### Card Data (PostgreSQL)
All card metadata — names, Oracle text, types, mana costs, legalities, and pricing — lives in the local PostgreSQL database. This is the platform's primary data store and the source of truth for all application logic.

### Card Images (Object Storage / CDN)
Card images are downloaded once and stored in an object storage service (AWS S3, Cloudflare R2, or equivalent). They are served to users via a CDN (Content Delivery Network), which caches them geographically close to the user for maximum speed.

**Storage estimate for MTG:** Scryfall hosts approximately 70,000+ unique card printings. At an average compressed size of approximately 100KB per image in a web-optimized format, the total image storage requirement is approximately **7GB**. At current AWS S3 pricing of $0.023 per GB per month, this costs approximately **$0.16 per month** — a negligible infrastructure cost [[4]](#ref-4).

Images are downloaded once per printing. They are never re-downloaded unless a corruption is detected during an integrity check.

### Rules Text (Vector Database + PostgreSQL)
The MTG Comprehensive Rules and all card Oracle texts are stored in two places simultaneously:
1. **PostgreSQL** — for direct retrieval by card name or rule number.
2. **Pinecone (Vector Database)** — as semantic embeddings, enabling the AI Oracle to search for relevant rules by meaning rather than exact keyword match.

---

## The Initial Setup Process

The first time the platform is deployed, the following one-time operations must be completed before the platform is opened to users:

1. **Bulk card data import:** Download the Scryfall bulk JSON and ingest all card records into PostgreSQL. Estimated time: 15–30 minutes.
2. **Image download:** Download all card images from Scryfall's CDN and upload them to the platform's object storage. Estimated time: 2–6 hours depending on network speed and parallelization.
3. **Rules ingestion:** Download the MTG Comprehensive Rules PDF, parse it into sections, and generate vector embeddings for each section. Estimated time: 30–60 minutes.
4. **Initial price sync:** Run the first nightly price sync to populate all pricing data. Estimated time: 10–20 minutes.
5. **Scheduler activation:** Enable all scheduled sync jobs.

After these steps are complete, the platform is fully operational and self-sustaining. No manual intervention is required for routine data maintenance.

---

## Limitations and Honest Trade-offs

Adopting this architecture means accepting certain constraints. These are known and acceptable.

**Community strategy data will always have a freshness window.** The metagame shifts continuously. A weekly sync of EDHREC and MTGGoldfish data means the platform's strategy recommendations may be up to 7 days behind the live sites. For casual players, this is irrelevant. For competitive players tracking a rapidly shifting metagame, the platform must display the data's last-updated timestamp prominently and recommend that they cross-reference live sources for the most current tournament results. This is not a failure — it is honest communication.

**New card data requires a manual trigger on set release day.** The scheduler cannot automatically know when a new MTG set has been released. A platform administrator must trigger the set release sync on release day. This is a deliberate choice: automated detection of set releases is possible but adds complexity, and a manual trigger ensures the sync is reviewed before it goes live. The process takes approximately 15 minutes and should be documented as a standard operating procedure.

**The initial setup is a significant infrastructure task.** Downloading and processing several gigabytes of data is not a one-click operation. It requires a properly provisioned server environment and should be performed by a developer, not an end user. This is a one-time cost and does not affect ongoing operations.

---

## What This Pillar Enables

By establishing local-first data ownership as a non-negotiable principle, every other part of the platform benefits:

- **The AI Coach and Oracle** can query card text and rules at database speed (milliseconds) rather than API speed (hundreds of milliseconds), making the AI responses feel faster.
- **Card search** returns results in under 50ms regardless of external service availability.
- **Portfolio valuation** is calculated from locally cached prices, meaning users can check their collection value even if the pricing API is temporarily unavailable (displaying the last known price with a timestamp).
- **The platform can be deployed in environments with restricted internet access** — such as a Local Game Store's internal network — because it does not require a live internet connection to function for most features.
- **The development team can build and test against a local copy of the database** without consuming API rate limits or requiring internet access.

---

## Summary

This document establishes the following as a non-negotiable architectural principle of GatherYourMagic:

1. All card data, images, rules, and pricing are stored locally in GatherYourMagic's own infrastructure.
2. External APIs are upstream data sources, synchronized on a defined schedule — never called in real time during a user session.
3. Different data types are synced on different schedules that reflect their actual rate of change.
4. All sync jobs are monitored, logged, and reported. Silent failures are not acceptable.
5. Data freshness is communicated transparently to users where relevant.

Any future architectural decision that would introduce a live, real-time dependency on an external API during a user session must be explicitly reviewed against this principle and approved as a documented exception with a stated rationale.

---

### References
[1] Scryfall Bulk Data API Documentation. https://scryfall.com/docs/api/bulk-data
[2] Magic: The Gathering Comprehensive Rules. Wizards of the Coast. https://magic.wizards.com/en/rules
[3] YGOPRODeck API Guide — Developer Notes. https://ygoprodeck.com/api-guide/
[4] AWS S3 Pricing. https://aws.amazon.com/s3/pricing/
