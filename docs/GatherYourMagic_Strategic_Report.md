# GatherYourMagic.com
## Strategic Vision & Research Report
### A Next-Generation TCG Platform for Collectors, Coaches, and Champions

**Prepared by:** Manus AI
**Date:** May 25, 2026
**Status:** Pre-Development Research Brief

---

## Executive Summary

The Trading Card Game (TCG) market is experiencing a golden age. The global market is valued between $7.8 billion and $13.28 billion in 2025 (estimates vary by methodology), with projections reaching $11.8–$24.36 billion by 2030, driven by a compound annual growth rate between 7.9% and 10.98% [[1]](#ref-1) [[2]](#ref-2). *Magic: The Gathering* (MTG), now in its 33rd year, remains the genre's most strategically complex and culturally rich title, with a player base spanning casual kitchen-table games to internationally broadcast competitive tournaments.

The digital tools that serve this community — Moxfield, Archidekt, EDHREC, MTGGoldfish — are excellent databases and deck builders. What they are not, and what the market urgently needs, is a platform that **understands the game**. A platform that can look at a decklist and say not just "your mana curve peaks at three" but "your strategy is undermined by a lack of late-game card velocity — here are five cards from your collection that would fix it." A platform that can answer a rules question mid-game in plain English. A platform that can coach a beginner through their first Commander game while simultaneously helping a seasoned player optimize for a competitive metagame.

**GatherYourMagic.com** is that platform. This report provides the foundational research required to build it: the mechanics of the game, the philosophy behind the cards, the competitive landscape, the technical infrastructure, and a strategic roadmap for market entry and dominance.

---

## Part I: Understanding Magic: The Gathering

### 1.1 The Game's Origin and Cultural Significance

Magic: The Gathering was created by mathematician Richard Garfield and published by Wizards of the Coast in 1993. It is widely credited as the first collectible card game (CCG) and has since produced over 30,000 unique cards across hundreds of expansion sets [[3]](#ref-3). The game is played in more than 70 countries, has been translated into multiple languages, and maintains one of the most dedicated and intellectually engaged player communities in all of gaming.

What makes MTG uniquely enduring is its layered complexity. At its surface, it is a game of resource management and combat. At its depth, it is a combinatorial puzzle of nearly infinite complexity, where the interaction of thousands of unique cards creates emergent strategies that no single human mind can fully map. This depth is precisely why a data-driven, AI-assisted platform has such transformative potential.

### 1.2 The Anatomy of a Magic Card

Every MTG card is a structured data object with clearly defined fields. A platform must model each of these precisely [[4]](#ref-4):

| Field | Description | Example |
| :--- | :--- | :--- |
| **Card Name** | The unique identifier of the card | *Lightning Bolt* |
| **Mana Cost** | The colored and colorless mana required to cast it | `{R}` (one Red mana) |
| **Mana Value (MV)** | The total numerical cost, formerly "Converted Mana Cost" | 1 |
| **Card Type** | The primary classification (Creature, Instant, Sorcery, Enchantment, Artifact, Land, Planeswalker, Battle) | Instant |
| **Subtypes** | Secondary classifications (e.g., creature types like Elf, Wizard) | — |
| **Rarity** | Common, Uncommon, Rare, Mythic Rare | Common |
| **Rules Text (Oracle Text)** | The official, errata-corrected card text | "Lightning Bolt deals 3 damage to any target." |
| **Power / Toughness** | For creatures: attack strength / damage it can absorb | 2/2 |
| **Loyalty** | For Planeswalkers: starting loyalty counter value | 4 |
| **Set & Collector Number** | The expansion set and card number within it | M10 #149 |
| **Artist** | The illustrator of the card art | Christopher Moeller |
| **Flavor Text** | Non-mechanical narrative text | *"Zap."* |
| **Format Legality** | Whether the card is legal in each format | Standard: No; Modern: Yes |

Beyond these standard fields, a competitive-grade platform must also track **printing variants** (foil, alternate art, borderless, extended art, showcase frames, promo versions), **condition grades** (Mint, Near Mint, Lightly Played, Moderately Played, Heavily Played, Damaged), and **market price** across multiple vendors.

### 1.3 The Color Pie: The Soul of the Game

The most philosophically distinctive element of MTG is its five-color system, known as the "Color Pie" or "Color Wheel." Devised by Richard Garfield and developed extensively by head designer Mark Rosewater, the Color Pie is not merely a mechanical constraint — it is a complete ideological framework that governs the personality, aesthetics, and gameplay identity of every card ever printed [[5]](#ref-5).

The five colors are arranged in a circle (WUBRG: White, Blue, Black, Red, Green), where adjacent colors are philosophical allies and non-adjacent colors are enemies. This arrangement creates ten two-color pairs, ten three-color combinations, five four-color combinations, and one five-color identity, each with its own distinct character.

**White** seeks *peace through order*. It believes that the collective good outweighs individual desire. Mechanically, White excels at creating armies of small creatures, protecting them with enchantments, imposing rules that restrict opponents, and using mass removal (board wipes) to reset the battlefield. White's weaknesses are card draw and direct damage.

**Blue** seeks *omniscience through knowledge*. It believes that perfect information leads to perfect outcomes. Mechanically, Blue excels at drawing cards, countering spells, returning permanents to hand, and winning through incremental advantage. Blue's weakness is that it rarely acts proactively — it is the color of reaction and control.

**Black** seeks *omnipotence through ruthlessness*. It believes that power is the only true currency, and any cost is worth paying for it. Mechanically, Black excels at destroying creatures, draining life, reanimating cards from graveyards, and drawing cards at the cost of life points. Black's weakness is that it cannot deal with artifacts or enchantments directly.

**Red** seeks *freedom through action*. It lives in the moment, values emotion over reason, and acts before thinking. Mechanically, Red excels at dealing direct damage (burn), deploying fast, aggressive creatures with haste, and destroying artifacts. Red's weakness is card advantage — it burns through its hand quickly and struggles in long games.

**Green** seeks *acceptance through harmony*. It believes in the natural order of things — that the biggest, strongest creature should win. Mechanically, Green excels at accelerating mana production (ramp), deploying large creatures, and destroying enchantments and artifacts. Green's weakness is flying creatures and direct interaction with other players.

> **Platform Implication:** GatherYourMagic's AI must internalize the Color Pie at a deep level. When a user says "I want to build a deck that controls the game," the AI should understand that Blue and White are the natural homes for that strategy, and that adding Black opens up powerful removal at the cost of some consistency. This is not data retrieval — it is genuine game understanding.

### 1.4 Card Types and the Stack

MTG features seven primary card types, each with distinct rules governing when and how they can be played [[4]](#ref-4):

**Lands** are the game's resource engine. Players may play one land per turn, and tapping a land produces mana to cast spells. The five basic land types (Plains, Island, Swamp, Mountain, Forest) produce their respective colors of mana. Non-basic lands often produce multiple colors or have additional abilities.

**Creatures** are the primary combat units. They have Power (attack) and Toughness (defense) values. Creatures typically enter the battlefield with "summoning sickness" — they cannot attack or use tap abilities until the player's next turn. Creatures are the most complex card type, featuring hundreds of keyword abilities.

**Instants** are spells that can be cast at any time, including during the opponent's turn and in response to other spells. This creates the game's most strategically rich element: the **stack**, where multiple spells and abilities can be layered and resolved in last-in, first-out order.

**Sorceries** are spells that can only be cast during the player's own main phase when the stack is empty. They are typically more powerful than instants for their cost.

**Enchantments** are persistent magical effects that remain on the battlefield. They can affect the global game state, attach to creatures (Auras), or create ongoing engines.

**Artifacts** are colorless permanents representing magical objects. They can be cast with any color of mana, making them highly flexible tools.

**Planeswalkers** represent powerful allies with loyalty counters. They have multiple activated abilities (+/- loyalty) and can be attacked directly by the opponent.

### 1.5 Turn Structure and Priority

A complete understanding of MTG's turn structure is essential for the Referee feature [[6]](#ref-6):

1. **Beginning Phase:** Untap (untap all permanents), Upkeep (triggered abilities), Draw (draw a card).
2. **Main Phase 1:** Cast spells, play a land.
3. **Combat Phase:** Declare attackers, declare blockers, deal combat damage.
4. **Main Phase 2:** Cast additional spells.
5. **Ending Phase:** End step (triggered abilities), Cleanup (discard to hand size).

**Priority** is the right to take an action. After each player takes an action, priority passes to the opponent. When both players pass priority in succession, the top item on the stack resolves. This creates the complex timing windows that define competitive play.

### 1.6 Evergreen Keywords

MTG has over 200 distinct keyword abilities, but a core set of "evergreen" keywords appears across virtually every set [[7]](#ref-7):

| Keyword | Effect |
| :--- | :--- |
| **Flying** | Can only be blocked by creatures with Flying or Reach |
| **Trample** | Excess combat damage carries over to the player |
| **Haste** | Can attack and use tap abilities the turn it enters |
| **Deathtouch** | Any damage it deals destroys the creature |
| **Lifelink** | Damage dealt also gains that much life |
| **Vigilance** | Does not tap when attacking |
| **Flash** | Can be cast at any time (like an Instant) |
| **Hexproof** | Cannot be targeted by opponents' spells or abilities |
| **Indestructible** | Cannot be destroyed by damage or effects that say "destroy" |
| **First Strike / Double Strike** | Deals combat damage before normal creatures / twice |

---

## Part II: Formats, Strategy, and Deck Archetypes

### 2.1 Format Overview

MTG's format system is what creates the game's extraordinary longevity. Rather than a single game, MTG is a family of games sharing the same cards but with radically different rules, power levels, and social contracts [[8]](#ref-8):

| Format | Card Pool | Deck Size | Key Characteristic |
| :--- | :--- | :--- | :--- |
| **Commander (EDH)** | All non-banned cards | 100 (singleton) | Multiplayer, led by a Legendary Creature |
| **Standard** | Last ~2-3 years of sets | 60 min | Rotating; most accessible for new players |
| **Pioneer** | Sets from *Return to Ravnica* (2012) onward | 60 min | Non-rotating; bridge between Standard and Modern |
| **Modern** | Sets from *8th Edition* (2003) onward | 60 min | Highly competitive; fast games |
| **Legacy** | All sets (with ban list) | 60 min | Eternal format; very high power level |
| **Vintage** | All sets (with restricted list) | 60 min | The most powerful format; includes the "Power 9" |
| **Pauper** | Common-rarity cards only | 60 min | Budget-friendly; surprisingly deep |
| **Draft / Sealed** | Cards from opened packs | 40 min | Limited format; no pre-built deck |

Commander is the most popular format in the game by a significant margin, and GatherYourMagic should treat it as the primary use case for the coaching and deck-building features.

### 2.2 The Four Deck Archetypes

All competitive decks in MTG can be broadly categorized into four strategic archetypes [[9]](#ref-9):

**Aggro (Aggressive):** Wins by deploying cheap, efficient threats early and overwhelming the opponent before they can stabilize. Aggro decks typically run 18-20 lands, 25-30 creatures, and minimal non-creature spells. The goal is to reduce the opponent's life total from 20 to 0 before turn 5-6. The AI coach should recognize an aggro deck by its low mana curve and high creature density, and flag any cards that slow the strategy down.

**Control:** Wins by preventing the opponent from executing their strategy, then winning with a small number of powerful threats. Control decks run 26-28 lands, heavy removal suites, counterspells, and card draw engines. They typically win in the late game (turns 8-12). The AI coach should recognize control by its high land count and interaction-heavy spell suite, and suggest improvements to the "win condition" package if it is too thin.

**Midrange:** The "fair" deck. Midrange plays efficient threats that are individually powerful, generating value through card quality rather than speed or disruption. It is flexible enough to play aggressively against slow decks and defensively against fast ones. The AI coach should identify midrange by its balanced curve and suggest whether to "lean in" to aggression or add more interaction based on the current metagame.

**Combo:** Wins by assembling a specific combination of cards that creates an infinite loop or an instant-win condition. Combo decks are highly consistent (using tutors to find key pieces) but fragile (disrupting one piece can collapse the strategy). The AI coach must be able to identify combo pieces within a decklist, map the full combo chain, and suggest redundancy or protection.

### 2.3 Commander-Specific Strategy

Commander (EDH) deserves special attention as the platform's primary format. A well-built Commander deck follows a specific structural framework [[10]](#ref-10):

| Category | Recommended Count | Purpose |
| :--- | :--- | :--- |
| **Win Conditions** | 7+ | Cards that can end the game |
| **Mana Acceleration (Ramp)** | 8-12 | Mana rocks, dorks, land fetchers |
| **Card Velocity (Draw)** | 8-10 | Card draw, impulse draw, looting |
| **Interaction (Removal)** | 8-10 | Point removal, board wipes, counterspells |
| **Lands** | 36-38 | Mana base |
| **Synergy Package** | Remaining | Cards that support the commander's theme |

The AI coach should evaluate every Commander decklist against this framework and provide specific, actionable feedback. For example: "Your deck has 12 win conditions but only 4 pieces of ramp. You will consistently run out of mana before you can execute your strategy. Consider cutting 3 win conditions and adding *Sol Ring*, *Arcane Signet*, and *Cultivate*."

### 2.4 The Metagame

The "metagame" (or "meta") refers to the ecosystem of decks currently being played in a given format. Understanding the meta is essential for competitive play:

* **Tier 1:** The most powerful and popular decks. Playing against these is expected.
* **Tier 2:** Strong decks that can beat Tier 1 under the right conditions.
* **Tier 3:** Fringe decks that are inconsistent but can surprise opponents.

GatherYourMagic should integrate with tournament results (from sources like MTGGoldfish and MTGTop8) to provide live metagame data, allowing users to see how their deck performs against the current field.

---

## Part III: The Competitive Landscape

### 3.1 Platform Analysis

The current market is served by a collection of specialized tools, each excelling in a narrow domain. No single platform offers the full-stack experience that GatherYourMagic envisions.

**Moxfield** (moxfield.com) is the current community favorite for deck building and sharing. Its interface is clean and modern, its deck editor is fast, and its community features (comments, likes, clones) are well-developed. However, it offers no AI coaching, no active recommendations, and no rules adjudication. It is a word processor for decklists, not a chess coach.

**Archidekt** (archidekt.com) is Moxfield's primary competitor, distinguished by its highly visual drag-and-drop interface and strong statistical breakdown tools (color distribution, type breakdown, mana curve visualization). It is favored by visual thinkers and players who want to see their deck as a grid of card images. Like Moxfield, it lacks any AI layer.

**EDHREC** (edhrec.com) is the most powerful data tool in the Commander space. It aggregates thousands of decklists from across the internet and provides statistical recommendations: "83% of decks running Commander X also run Card Y." This is enormously useful for finding synergistic cards. However, EDHREC's recommendations are purely statistical — they reflect what is popular, not what is optimal for a given player's specific goals, budget, or playstyle. Its recommendations tend to produce homogenized, "average" decks.

**MTGGoldfish** (mtggoldfish.com) is the premier destination for competitive metagame analysis and financial tracking. It provides tournament results, metagame share percentages, price history graphs, and a "SuperBrew" premium feature that suggests decks based on cards you own. Its weakness is its complexity and its heavy focus on the competitive and financial aspects of the game, which alienates casual players.

**Scryfall** (scryfall.com) is the definitive card search engine. Its advanced syntax allows for extraordinarily precise queries (e.g., "find all creatures with power greater than 5 that cost less than 3 mana and have Flying"). It is the backbone of the MTG data ecosystem and the source of the Scryfall API.

### 3.2 The Opportunity Gap

The competitive analysis reveals a clear and exploitable gap in the market:

> **No existing platform combines (1) comprehensive collection management, (2) intelligent deck-building assistance, (3) real-time rules adjudication, and (4) multi-game TCG support in a single, unified, AI-powered experience.**

GatherYourMagic's competitive advantage is not any single feature — it is the integration of all of them into a coherent platform that understands the *game*, not just the *cards*.

---

## Part IV: The Broader TCG Ecosystem

### 4.1 Pokémon TCG

The Pokémon Trading Card Game was first published in Japan in 1996 and has since produced over 75 billion cards worldwide [[11]](#ref-11). It is currently the highest-grossing TCG in Japan, earning a record $857 million in 2023, outpacing MTG and Yu-Gi-Oh! combined in that market [[12]](#ref-12).

**Gameplay Mechanics:** Players build 60-card decks consisting of Pokémon cards (the combat units), Energy cards (the resource system), and Trainer cards (utility spells). The win condition is to collect all six Prize cards, which are set aside at the start of the game and claimed by knocking out the opponent's Pokémon. The game features an Evolution system (Basic → Stage 1 → Stage 2) that adds strategic depth to deck construction.

**Collector Dimension:** The Pokémon TCG has an enormous collector base that is often distinct from the competitive player base. Rare variants — Full Art cards, Secret Rares, Illustration Rares, and Special Art Rares — command significant premiums. A robust variant-tracking system is essential for this audience.

### 4.2 Yu-Gi-Oh!

Yu-Gi-Oh! is published by Konami and features one of the most complex rule systems in the TCG genre. Unlike MTG and Pokémon, Yu-Gi-Oh! has no traditional resource system — players can play as many cards as they can chain together in a single turn, leading to "combo turns" that can last several minutes and involve dozens of card interactions.

**Gameplay Mechanics:** Decks consist of 40-60 cards in the Main Deck and up to 15 cards in the Extra Deck (which contains special "summoned" monsters including Synchro, Xyz, Fusion, and Link Monsters). The game features a complex "chain" system analogous to MTG's stack. The AI coach for Yu-Gi-Oh! must be capable of mapping multi-step combo chains.

### 4.3 Emerging TCGs

The TCG market is experiencing a wave of new entrants, several of which have achieved rapid commercial success:

**Disney Lorcana** (Ravensburger, 2023) uses an "Ink" system with six colors and a "Lore" win condition (first to 20 Lore wins). Its Disney IP gives it enormous crossover appeal with collectors who may not be traditional TCG players. It is currently ranked in the top 10 bestselling TCGs globally.

**One Piece TCG** (Bandai, 2022) has surged dramatically in popularity, with some market analysts projecting it could challenge MTG for market share. Its anime IP drives both collector and competitive interest.

**Flesh and Blood** (Legend Story Studios, 2019) is a technically demanding game focused on hero-vs-hero combat with a unique "pitch" resource system. It has developed a dedicated competitive following and is notable for its anti-scalping policies (printing to demand for most sets).

---

## Part V: Technical Architecture

### 5.1 Data Sources and APIs

GatherYourMagic should be built on a foundation of existing, high-quality data sources rather than attempting to manually catalog cards:

| API / Source | Coverage | Use Case |
| :--- | :--- | :--- |
| **Scryfall API** | All MTG cards, rulings, legalities | Primary MTG card data, images, Oracle text [[13]](#ref-13) |
| **MTG JSON** | All MTG cards in bulk JSON | Offline database, bulk import |
| **Pokémon TCG API (Scrydex)** | All Pokémon TCG cards and sets | Pokémon card data and images [[14]](#ref-14) |
| **YGOPRODeck API** | All Yu-Gi-Oh! cards | Yu-Gi-Oh! card data, ban lists, prices [[15]](#ref-15) |
| **JustTCG API** | MTG, Pokémon, Yu-Gi-Oh!, Lorcana, One Piece | Cross-game pricing, condition-specific values [[16]](#ref-16) |
| **TCGplayer API** | All major TCGs | Marketplace integration, buy/sell |
| **Cardmarket API** | European market | European pricing data |

### 5.2 The AI Architecture

The "Coach" and "Referee" features require a carefully designed AI stack:

**Vector Database (Pinecone, Weaviate, or Milvus):** All card Oracle text, rulings, and synergy relationships should be stored as vector embeddings. This enables semantic search — the AI can find cards that "protect creatures from board wipes" without relying on exact keyword matching. This is the foundation of the recommendation engine.

**Retrieval-Augmented Generation (RAG):** The LLM must be grounded in factual data. When a user asks "Can I cast this spell in response to a counterspell?", the AI should retrieve the relevant sections of the Comprehensive Rules and the specific card's Oracle text before generating its answer. This prevents the LLM from "hallucinating" incorrect rules.

**LLM Integration (OpenAI GPT-4o, Anthropic Claude, or Gemini):** The natural language engine that interprets user queries and generates conversational responses. The LLM should be fine-tuned or prompted with MTG-specific context to ensure it speaks the language of the game.

**Deck Analysis Engine:** A deterministic (non-AI) rules engine that calculates objective deck statistics: mana curve, color distribution, type breakdown, average mana value, land count, and format legality. This provides the factual foundation upon which the AI's qualitative advice is built.

### 5.3 Platform Architecture Recommendations

Given the user's stated goals (personal tool + client-commissioned product, multi-user), the following architecture is recommended:

* **Frontend:** React + TypeScript with TailwindCSS for a modern, responsive web interface. Mobile-responsive design is essential for in-store use.
* **Backend:** Node.js or Python FastAPI for the API layer. The AI coaching features should be implemented as dedicated microservices.
* **Database:** PostgreSQL for structured collection and deck data; a vector database (Pinecone) for AI embeddings; Redis for caching frequently accessed card data.
* **Authentication:** OAuth2-based user authentication to support multi-user collections.
* **Card Scanning:** Integration with a camera-based card recognition API (e.g., Card Conjurer's recognition API or a custom model built on TensorFlow) for rapid physical collection entry.

---

## Part VI: The GatherYourMagic Feature Blueprint

### 6.1 Core Features (Minimum Viable Product)

The platform must be competitive with existing tools before it can differentiate. The MVP must include:

1. **Universal Card Search:** Powered by Scryfall's advanced syntax for MTG, with equivalent search for Pokémon and Yu-Gi-Oh!. Filters should include name, type, color, mana value, set, rarity, format legality, artist, and rules text keyword.
2. **Collection Management:** Add cards to a personal collection with quantity, condition, and variant tracking. Support bulk import via CSV or text list.
3. **Portfolio Valuation:** Real-time collection value calculated from JustTCG/TCGplayer pricing data.
4. **Deck Builder:** Visual deck builder with drag-and-drop card placement, mana curve visualization, and format legality checking.
5. **Deck Statistics:** Automated breakdown of color distribution, type ratios, average mana value, and land count.

### 6.2 Differentiating Features (The Competitive Moat)

These features are what will make GatherYourMagic the best platform in the market:

**The Oracle (AI Referee):** A conversational AI assistant that can answer rules questions in plain English. A player mid-game can type: "My opponent has a creature with Indestructible. I cast Swords to Plowshares targeting it. Does it work?" The Oracle consults the official Comprehensive Rules and responds: "Yes. Swords to Plowshares exiles the creature rather than destroying it. Indestructible only prevents destruction and lethal damage, not exile effects."

**The Coach (AI Deck Advisor):** After a user builds or imports a deck, the Coach analyzes it against the structural framework for its format and provides a written assessment. For a Commander deck: "Your deck has strong synergy around the Elf tribal theme, but I notice you only have 5 pieces of ramp in a deck with an average mana value of 3.8. I recommend adding *Selvala, Heart of the Wilds* and *Priest of Titania* to ensure you can cast your spells on curve. I also see you own both of these cards in your collection."

**Collection-Aware Recommendations:** Unlike EDHREC (which recommends popular cards regardless of ownership), GatherYourMagic's recommendations will always prioritize cards the user already owns, then suggest affordable acquisitions to fill gaps.

**Multi-Format Legality Advisor:** When a user views any card, the platform displays its legality across all formats and flags any upcoming rotation dates for Standard-legal cards.

**Trade Assistant:** A "Haves and Wants" matching system that connects users with compatible trade partners in their local area or online.

### 6.3 The Innovative Edge: Beyond the Existing Paradigm

To be truly innovative, GatherYourMagic should introduce features that no existing platform offers:

**Playstyle Profiling:** Through a brief onboarding questionnaire and analysis of the user's existing decks, the platform builds a "Playstyle Profile" — a Color Pie-inspired assessment of the user's preferences (e.g., "You prefer proactive strategies, value card advantage, and enjoy complex decision trees — you are a Blue/Black player at heart"). All recommendations are then filtered through this profile.

**The Matchup Simulator:** Using metagame data, the platform can simulate how a user's deck would perform against the current top decks in their format, providing a win-rate estimate and specific sideboard recommendations.

**Lore Integration:** For each card, the platform provides not just mechanical data but narrative context — the story behind the card, the plane it comes from, and its place in the MTG multiverse. This transforms the platform from a database into an encyclopedia.

**The Commander Bracket System:** Aligned with the official Commander Bracket system (1-5, from Casual to cEDH), the platform automatically assesses a deck's power level and suggests appropriate playgroups.

---

## Part VII: Business Model and Go-to-Market Strategy

### 7.1 Revenue Model

A freemium model is recommended, mirroring the successful approach of MTGGoldfish and Moxfield:

| Tier | Price | Features |
| :--- | :--- | :--- |
| **Free** | $0/month | Core card search, basic collection tracking (up to 500 cards), basic deck builder, community features |
| **Enthusiast** | $4.99/month | Unlimited collection, portfolio valuation, AI deck statistics, format legality checking |
| **Champion** | $9.99/month | Full AI Coach access, Oracle (rules referee), matchup simulator, trade assistant, priority support |
| **Store/LGS** | $29.99/month | Multi-user management, event tools, inventory management, bulk pricing |

### 7.2 Go-to-Market Strategy

The MTG community is highly concentrated in specific online spaces. An effective launch strategy targets these communities directly:

1. **Content Creator Partnerships:** Partner with MTG YouTube channels (The Command Zone, Tolarian Community College, MTGGoldfish) and Twitch streamers for sponsored content and early access.
2. **Local Game Store (LGS) Program:** Offer free Store tier accounts to LGS owners in exchange for in-store promotion and QR code placement.
3. **Reddit and Discord:** Active presence in r/magicTCG, r/EDH, r/Magicdeckbuilding, and major MTG Discord servers.
4. **Scryfall Integration:** Develop a browser extension that adds a "Send to GatherYourMagic" button on Scryfall card pages.

---

## Conclusion: The Vision Realized

GatherYourMagic.com is not just another card database. It is the realization of a vision that the MTG community has been waiting for without knowing it: a platform that is as passionate about the game as the players who love it.

The technology is ready. The market is growing. The competition is strong but not unbeatable — because none of them have built a coach. None of them have built a referee. None of them have built a friend who knows every card ever printed, understands every rule ever written, and can look at your deck and tell you exactly how to make it better.

That is what GatherYourMagic will be. And when it is, the tabletop gaming world will gather around it, one deck at a time.

---

## References

[1] <a name="ref-1"></a>Trading Card Games Analysis Report 2025: A Global $11.8 Billion Market. Yahoo Finance. https://finance.yahoo.com/news/trading-card-games-analysis-report-114800642.html

[2] <a name="ref-2"></a>Trading Card Game Market Size, Share, Trends & Industry Growth. Mordor Intelligence. https://www.mordorintelligence.com/industry-reports/trading-card-game-market

[3] <a name="ref-3"></a>Magic: The Gathering. Wikipedia. https://en.wikipedia.org/wiki/Magic:_The_Gathering

[4] <a name="ref-4"></a>Anatomy of a Magic Card. Wizards of the Coast. https://magic.wizards.com/en/news/feature/anatomy-magic-card-2006-10-21

[5] <a name="ref-5"></a>Color. MTG Wiki — Fandom. https://mtg.fandom.com/wiki/Color

[6] <a name="ref-6"></a>Turn Structure. MTG Wiki — Fandom. https://mtg.fandom.com/wiki/Turn_structure

[7] <a name="ref-7"></a>MTG Evergreen Keywords. Draftsim. https://draftsim.com/mtg-evergreen/

[8] <a name="ref-8"></a>Magic: The Gathering Formats. Wikipedia. https://en.wikipedia.org/wiki/Magic:_The_Gathering_formats

[9] <a name="ref-9"></a>The Ultimate Introduction to Deckbuilding in Magic. Draftsim. https://draftsim.com/build-mtg-deck/

[10] <a name="ref-10"></a>How to Build a Commander Deck. EDHREC. https://edhrec.com/articles/how-to-build-a-commander-deck

[11] <a name="ref-11"></a>Pokémon Trading Card Game. Wikipedia. https://en.wikipedia.org/wiki/Pok%C3%A9mon_Trading_Card_Game

[12] <a name="ref-12"></a>Pokemon TCG Earned Record $857 Million in Japan for 2023. Pokebeach. https://www.pokebeach.com/forums/threads/pokemon-tcg-earned-record-857-million-in-japan-for-2023-more-than-yu-gi-oh-magic-and-others-combined.154869/

[13] <a name="ref-13"></a>Scryfall API Documentation. https://scryfall.com/docs/api

[14] <a name="ref-14"></a>Pokémon TCG API (Scrydex). https://pokemontcg.io/

[15] <a name="ref-15"></a>Yu-Gi-Oh! API Guide — YGOPRODeck. https://ygoprodeck.com/api-guide/

[16] <a name="ref-16"></a>JustTCG Pricing API. https://justtcg.com/
