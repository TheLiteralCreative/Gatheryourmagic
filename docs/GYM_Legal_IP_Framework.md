# GatherYourMagic.com
## Legal & Intellectual Property Framework

**Document Type:** Foundational Legal Reference
**Audience:** Project Owner, Developers, Legal Counsel
**Status:** Pre-Development — Must Be Reviewed by a Licensed Attorney Before Launch
**Date:** May 25, 2026

> **Disclaimer:** This document is an informed research summary prepared to identify legal considerations and guide strategic decisions. It is not legal advice. Before launching GatherYourMagic.com as a commercial platform, all sections of this document must be reviewed and validated by a licensed intellectual property and internet law attorney.

---

## Overview

GatherYourMagic.com operates at the intersection of three distinct legal territories: **intellectual property law** (specifically the IP of Wizards of the Coast, The Pokémon Company, Konami, and other card game publishers), **data and privacy law** (GDPR, CCPA, and related regulations), and **platform liability law** (terms of service, user-generated content, and subscription contract law). Each territory has specific requirements and risks that must be understood before a single line of production code is written.

The good news is that dozens of platforms have successfully navigated this landscape before GatherYourMagic. The bad news is that some have not — and the ones that failed did so because they did not understand where the lines were drawn. This document draws those lines clearly.

---

## Part 1: Wizards of the Coast Intellectual Property

### 1.1 What WotC Owns

Wizards of the Coast (WotC), a subsidiary of Hasbro, holds intellectual property rights over virtually every element of Magic: The Gathering. This includes:

- **Card artwork** — Every piece of art on every card is either owned by WotC outright (as a work-for-hire) or licensed from the original artist. WotC controls reproduction rights.
- **Card names and Oracle text** — The specific wording of card abilities is copyrightable as literary expression. The *name* "Lightning Bolt" is also a registered trademark.
- **Mana symbols** — The five mana symbols (and all variant symbols) are registered trademarks of WotC.
- **The MTG logo and wordmark** — "Magic: The Gathering," the Planeswalker symbol, and all set logos are registered trademarks.
- **Game mechanics** — WotC held a patent on the "tapping" mechanic (rotating a card to indicate it has been used) until it expired. Game mechanics themselves are generally not copyrightable, but the *specific expression* of rules text is.
- **Lore, characters, and world-building** — The Multiverse, all Planeswalker characters, plane names, and narrative content are protected creative works.

### 1.2 The Fan Content Policy — What It Permits

WotC published its **Fan Content Policy** (last updated November 15, 2017) specifically to govern how community members and developers can use MTG IP. The full policy is available at https://company.wizards.com/en/legal/fancontentpolicy. The key permissions and restrictions are as follows.

**What the Fan Content Policy PERMITS:**

- Creating fan websites, apps, tools, and community content using WotC IP.
- Displaying card images sourced from Scryfall (which itself operates under the Fan Content Policy).
- Reproducing Oracle text for reference and educational purposes.
- Earning **ad revenue, sponsorships, and donations** to subsidize the cost of creating fan content.
- Requiring a free account login to access the platform (this is explicitly confirmed in the FAQ).

**What the Fan Content Policy PROHIBITS:**

- **Charging money for access to WotC IP.** This is the most critical restriction. Users cannot be required to pay, subscribe, or purchase anything in order to access card data, card images, or Oracle text. The policy states: *"You can't require payments, surveys, downloads, subscriptions, or email registration to access your Fan Content."*
- **Using WotC logos and trademarks** without prior written permission. This includes the MTG logo, set logos, and mana symbols used as standalone design elements (as opposed to appearing on card images).
- **Selling or licensing fan content** to third parties for compensation.
- **Using WotC IP in other games** — the platform cannot use MTG card data to power a different card game.
- **Using WotC video or music content.**

### 1.3 The Critical Tension: Monetization vs. the Fan Content Policy

This is the single most important legal issue the platform must resolve. The Fan Content Policy, read strictly, appears to prohibit charging a subscription fee for a platform that displays card images and Oracle text. However, the reality of the existing platform ecosystem tells a more nuanced story.

**How Existing Platforms Handle This:**

Moxfield, one of the most prominent MTG deck-building platforms, includes the following notice in its footer:

> *"Moxfield LLC may use the trademarks and other intellectual property of Wizards of the Coast LLC, which is permitted under Wizards' Fan Site Policy."*

Moxfield operates a freemium model with paid subscription tiers. TCGplayer, which is now a WotC **preferred partner** (as of November 2025), uses card images and data commercially. EDHREC, MTGGoldfish, and Archidekt all monetize in various ways while displaying card images.

The key distinction these platforms appear to draw is:

> **The card data itself is free to access. The subscription pays for the platform's tools, features, and services — not for access to the card data.**

In other words, the free tier of GatherYourMagic must allow any user to search for cards and view card data without paying. What the subscription gates is access to *the platform's own value-added features* — the AI Coach, the advanced deck builder, the portfolio analytics. The card data is always free. The intelligence built on top of it is the paid product.

This is a meaningful and defensible distinction, but it must be implemented carefully and reviewed by legal counsel before launch.

### 1.4 The mtgimage.com Precedent — WotC Enforcement History

In 2015, WotC sent a cease-and-desist letter to mtgimage.com, a developer-focused service that provided high-resolution card images via API. The site was shut down. This is the most significant enforcement action WotC has taken against a community tool, and it has two important lessons:

1. **WotC does enforce its IP** when it perceives a threat to its control over card images, particularly when a service is providing images in a way that could enable counterfeiting (high-resolution, uncropped images).
2. **The enforcement was targeted** — WotC did not go after Scryfall, MTGGoldfish, or other platforms that display card images in context. The distinction appears to be that mtgimage.com was providing raw image assets to third parties, while other platforms display images as part of a user interface.

The practical implication for GatherYourMagic: **always display card images in context, never expose raw image URLs as a downloadable API, and always preserve the copyright notice and artist credit on every card image.** Scryfall's own usage guidelines explicitly require this.

### 1.5 The Path to a Commercial License

For a platform that intends to grow into a significant commercial product, the Fan Content Policy is a ceiling, not a foundation. The appropriate long-term path is to seek a **direct commercial license from WotC**.

WotC has granted commercial licenses to select partners. TCGplayer has a formal partnership agreement. ChannelFireball operates as a WotC Authorized Internet Retailer. These agreements grant expanded rights — including the right to use trademarks in marketing materials — in exchange for compliance with WotC's commercial partner standards.

The process for seeking a commercial license is:
1. Contact WotC through their official support system at https://support.wizards.com.
2. Describe the platform, its commercial model, and the specific IP usage requested.
3. Negotiate terms. WotC has historically been willing to work with platforms that demonstrate community value and compliance.

**Recommendation:** Launch under the Fan Content Policy with a carefully designed free tier that keeps card data accessible without payment. Simultaneously, initiate contact with WotC's licensing team to begin the commercial license conversation. Do not wait until after launch to have this conversation.

---

## Part 2: Other TCG Publishers

### 2.1 The Pokémon Company

The Pokémon TCG API (pokemontcg.io) provides card data and images for developer use. The API's terms of service permit use in community tools but prohibit commercial exploitation of the data itself. The same free-tier-for-card-data principle applies.

The Pokémon Company is generally less aggressive in enforcement than WotC but has taken action against counterfeit card sellers and unauthorized merchandise. A platform that displays card data in context, credits The Pokémon Company, and does not charge for access to the card data is operating in a safe zone.

### 2.2 Konami (Yu-Gi-Oh!)

YGOPRODeck provides a free API for Yu-Gi-Oh! card data. Konami's IP enforcement posture is similar to WotC's — they tolerate community tools that operate under fan content principles but have pursued counterfeit sellers and unauthorized game implementations aggressively.

### 2.3 Disney Lorcana (Ravensburger)

Lorcana is a newer game with less established community tool precedent. Ravensburger has not published a formal fan content policy equivalent to WotC's. This means the platform should proceed conservatively with Lorcana content — displaying card images in context, crediting Disney and Ravensburger, and avoiding any use that could be construed as commercial exploitation of Disney IP (which is among the most aggressively protected IP in the world).

### 2.4 The Universal Principle Across All Publishers

Regardless of the specific publisher, the following principles apply universally and should be encoded into the platform's design:

- Card data is always accessible for free.
- Card images are always displayed in context, never as raw downloadable assets.
- Artist credits and copyright notices are always preserved and visible.
- The platform is always clearly identified as unofficial and not endorsed by any publisher.
- The platform never uses publisher logos or trademarks as standalone design elements.

---

## Part 3: Platform Legal Documents

These are the legal documents the platform must have in place before accepting users or payments. Each one serves a specific protective function.

### 3.1 Terms of Service (ToS)

**What it does:** Defines the rules of the platform, limits the platform's liability, establishes dispute resolution procedures, and sets the terms of the subscription contract.

**Critical clauses for GatherYourMagic:**

- **Intellectual Property Acknowledgment** — Explicitly states that all card data, images, and game content belong to their respective publishers and that the platform operates under their fan content policies.
- **Limitation of Liability** — Caps the platform's liability to users at the amount paid in the last 12 months. This is standard and essential.
- **AI Disclaimer** — Explicitly states that The Coach and The Oracle are AI tools that may make errors, that their output is not a substitute for official rulings, and that the platform is not liable for decisions made based on AI recommendations. This is particularly important given the Oracle's rules-referee function.
- **Subscription Terms** — Defines billing cycles, cancellation policy, refund policy, and what happens to user data upon account cancellation.
- **User-Generated Content License** — If users can share decks publicly, the ToS must grant the platform a license to display that content and define what users can and cannot post.
- **Arbitration Clause** — Requires disputes to be resolved through arbitration rather than class-action lawsuits. Standard for subscription platforms.
- **Governing Law** — Specifies the jurisdiction whose laws govern the agreement.

### 3.2 Privacy Policy

**What it does:** Discloses what data the platform collects, how it is used, who it is shared with, and what rights users have over their data.

**Legal requirements by jurisdiction:**

| Regulation | Jurisdiction | Key Requirements |
| :--- | :--- | :--- |
| **GDPR** | European Union | Explicit consent for data collection; right to access, correct, and delete personal data; data breach notification within 72 hours; Data Protection Officer required if processing at scale |
| **CCPA / CPRA** | California, USA | Right to know what data is collected; right to opt out of data sale; right to delete data; updated 2026 rules add consent requirements for sensitive data |
| **COPPA** | USA | If any users are under 13, parental consent is required before collecting any personal data. The platform should require users to confirm they are 13+ at registration. |
| **PIPEDA** | Canada | Consent required for data collection; users can withdraw consent; data must be protected with appropriate security |

**Data GatherYourMagic will collect and must disclose:**
- Account information (email, username)
- Collection data (cards owned, quantities, conditions)
- Deck data (card lists, formats, notes)
- Usage data (features used, session duration, search queries)
- AI interaction data (Coach queries, Oracle questions — used to improve the AI)
- Payment information (processed by Stripe; the platform itself should never store raw card numbers)
- Optional: win/loss records, playstyle profile responses

**Recommendation:** Use a GDPR-compliant consent management platform (such as Cookieyes or Osano) from day one, even if the initial user base is primarily North American. It is far easier to build privacy compliance in from the start than to retrofit it after launch.

### 3.3 Cookie Policy

Required under GDPR and increasingly expected by users globally. Must disclose what cookies are used, their purpose (functional, analytics, advertising), and provide a mechanism to accept or reject non-essential cookies.

### 3.4 Acceptable Use Policy (AUP)

Defines what users cannot do on the platform. Key prohibitions for GatherYourMagic:
- No uploading counterfeit card images.
- No using the platform to facilitate fraud (e.g., misrepresenting card conditions in trades).
- No automated scraping of card data from the platform.
- No sharing account credentials.
- No using the AI features to generate content that violates WotC's Fan Content Policy.

---

## Part 4: Scryfall API — Specific Terms

Because Scryfall is the primary data source for MTG card data, its specific usage terms deserve explicit attention. Scryfall states the following at https://scryfall.com/docs/api:

> *"You may not 'paywall' access to Scryfall data. You may not require anyone to make payments, take surveys, agree to subscriptions, rate your content, join chat servers, or follow channels in exchange for access to Scryfall data. If you have an account system, end-users should be able to access card data anonymously or with free accounts."*

Additionally, when using Scryfall images:
- Do not cover, crop, or clip off the copyright or artist name.
- Do not distort, skew, or stretch card images.
- Do not add watermarks, stamps, or logos to card images.
- Do not imply that anyone other than WotC created the card.

**The direct implication:** Every card search result, every card displayed in a deck, every card shown in a collection view must be accessible to free-tier users. The subscription cannot gate card search or card display. It can gate the AI analysis, the advanced deck tools, and the portfolio analytics — but not the card data itself.

---

## Part 5: Liability Reduction — Specific Recommendations

The following are concrete, actionable steps to reduce legal exposure, ordered by priority.

### Priority 1: Engage an IP Attorney Before Launch

This is non-negotiable. The specific question of whether GatherYourMagic's subscription model is compatible with the Fan Content Policy must be answered by a licensed attorney, not by a research document. The cost of a legal review ($500–$2,000 for a focused consultation) is trivially small compared to the cost of a cease-and-desist response or a forced pivot after launch.

**Specific questions to bring to counsel:**
1. Does our freemium model — where card data is always free but AI features are paid — comply with the WotC Fan Content Policy and Scryfall's API terms?
2. What is the appropriate process for initiating a commercial license conversation with WotC?
3. What disclaimers and attribution language must appear on every page of the platform?
4. Are there any GDPR or CCPA requirements that affect our AI data processing (specifically, the use of user queries to improve the AI model)?

### Priority 2: Design the Free Tier to Be Genuinely Free

The free tier must provide real, meaningful access to card data. A free tier that is so limited as to be unusable is not a good-faith implementation of the Fan Content Policy. The recommended free tier (as defined in the Executive Summary) — core card search and basic collection tracking — satisfies this requirement. Any future changes to the free tier must be reviewed against the Fan Content Policy before implementation.

### Priority 3: Implement Required Attribution on Every Page

Every page of the platform that displays MTG content must include the following notice, as specified by the Fan Content Policy:

> *"GatherYourMagic.com is unofficial Fan Content permitted under the Fan Content Policy. Not approved/endorsed by Wizards. Portions of the materials used are property of Wizards of the Coast. ©Wizards of the Coast LLC."*

This notice should appear in the site footer on every page, not buried in a terms of service document.

For Pokémon content:
> *"Pokémon and Pokémon character names are trademarks of Nintendo. Card images and data sourced from the Pokémon TCG API."*

For Yu-Gi-Oh! content:
> *"Yu-Gi-Oh! and all related names are trademarks of Konami. Card data sourced from YGOPRODeck."*

### Priority 4: Draft an AI Disclaimer for The Coach and The Oracle

The Oracle in particular carries liability risk. If a player makes a tournament decision based on an incorrect Oracle ruling and suffers a competitive consequence, the platform must be protected. The disclaimer should appear:
- In the Terms of Service.
- As a persistent notice in the Oracle chat interface.
- As a tooltip on every Oracle response.

Recommended language:

> *"The Oracle is an AI assistant and may make errors. Its responses are not official rulings and are not a substitute for a certified judge's decision. For official rulings, consult the Magic: The Gathering Comprehensive Rules at magic.wizards.com or a certified Head Judge at sanctioned events."*

### Priority 5: Use Stripe for All Payment Processing

Never handle raw payment card data. Stripe is the industry standard for subscription platforms, is PCI-DSS compliant, and handles all payment data security on the platform's behalf. The platform's database should store only a Stripe customer ID, never a credit card number. This eliminates the most severe category of financial data liability.

### Priority 6: Implement a GDPR-Compliant Data Deletion Flow

Under GDPR (and increasingly under CCPA), users have the right to request deletion of all their personal data. The platform must be able to honor this request completely — deleting the user's account, collection data, deck data, and AI interaction history — within 30 days of the request. This must be built into the platform architecture from day one, not added as an afterthought.

### Priority 7: Contact WotC Proactively

Do not wait for a cease-and-desist. Reach out to WotC's licensing team before launch to introduce the platform, describe its model, and ask for guidance. This serves two purposes: it demonstrates good faith, and it opens the door to a formal commercial license relationship. WotC has been receptive to platforms that approach them proactively and transparently.

Contact: https://support.wizards.com

---

## Part 6: The Risk Matrix

| Risk | Likelihood | Severity | Mitigation |
| :--- | :--- | :--- | :--- |
| WotC cease-and-desist for paywalling card data | Medium — if free tier is inadequate | High — could force platform redesign | Design free tier to be genuinely free; proactively contact WotC |
| WotC cease-and-desist for trademark misuse | Low — if attribution is correct | High | Implement required attribution on every page; do not use WotC logos as design elements |
| Scryfall API access revoked | Low — if terms are followed | High — platform loses primary data source | Follow Scryfall terms strictly; maintain local database so revocation does not cause immediate outage |
| GDPR enforcement action | Low at launch (small user base) | Medium — fines up to 4% of global revenue | Implement GDPR-compliant consent and data deletion from day one |
| User sues over incorrect Oracle ruling | Very low | Low — if disclaimer is in place | Implement AI disclaimer in ToS and in the Oracle interface |
| Payment data breach | Very low — if using Stripe | Very high | Use Stripe; never store raw payment data |
| Copyright claim from card artist | Very low | Medium | Always preserve artist credit on card images; never crop or modify images |

---

## Summary: The Three Non-Negotiables

Before GatherYourMagic.com accepts its first paying subscriber, three things must be true:

1. **Card data is free.** Any user, with or without a paid account, can search for cards and view card data including images and Oracle text. The subscription pays for the platform's intelligence and tools, not for access to the underlying card information.

2. **Attribution is everywhere.** The WotC Fan Content Policy notice appears in the footer of every page. Artist credits are visible on every card image. The platform is clearly identified as unofficial.

3. **An attorney has reviewed it.** A licensed IP attorney has confirmed that the platform's specific implementation of the freemium model is compatible with the Fan Content Policy and Scryfall's API terms, and has drafted or reviewed the Terms of Service, Privacy Policy, and AI disclaimer.

Everything else in this document is important. These three are essential.

---

### References
[1] Wizards of the Coast Fan Content Policy. https://company.wizards.com/en/legal/fancontentpolicy
[2] Wizards of the Coast General Terms of Use. https://company.wizards.com/en/legal/terms
[3] Scryfall API Documentation — Use of Data and Images. https://scryfall.com/docs/api
[4] Rokhlin, S. (2021). "28th Anniversary of Magic: The Gathering — A Discussion of IP Considerations, Part Two." Patent Arcade. https://www.patentarcade.com/2021/08/28th-anniversary-of-magic-the-gathering-a-discussion-of-ip-considerations-part-two.html
[5] INFORMS (2025). "Study Finds that News Content is a Driver for Traffic to Non-news Content on Aggregator Sites." Marketing Science. https://pubsonline.informs.org/stoken/default+domain/PR-6-2025/full/10.1287/mksc.2024.0993
[6] ICv2 (2025). "Wizards of the Coast Expands Partnership with TCGplayer." https://icv2.com/articles/news/view/61017/wizards-coast-further-expands-partnership-tcgplayer
[7] Reddit r/magicTCG (2015). "mtgimage.com has been shut down by Wizards." https://www.reddit.com/r/magicTCG/comments/2yl94g/
[8] California Attorney General. California Consumer Privacy Act (CCPA). https://oag.ca.gov/privacy/ccpa
