# GatherYourMagic.com
## Feature Consideration: The Fifth Pillar — "The Dispatch"
### A Current Events & News Intelligence Division

**Document Type:** Future Feature Consideration — Not In Active Development
**Audience:** Project Stakeholders
**Status:** Exploratory — Held for Phase 2+ Evaluation
**Date:** May 25, 2026

---

## Framing

This document explores the potential addition of a fifth core feature to GatherYourMagic's platform: a dedicated, intelligent news and current events division tentatively named **The Dispatch**. The four confirmed pillars — The Library (collection management), The Coach (deck optimization), The Oracle (rules referee), and the Playstyle Profiler (user identity) — are focused on the player's *internal* world: their cards, their decks, their game. The Dispatch would turn the platform's attention *outward*, toward the living, constantly-evolving world of Magic: The Gathering as a cultural and competitive phenomenon.

This document does not recommend immediate development. It assesses the ROI case, identifies the pros, cons, and critical factors, and provides a clear picture of what this feature would require and what it would return — so that when the time comes to make the decision, the analysis is already done.

---

## What "The Dispatch" Would Do

The Dispatch is an intelligent news aggregation and synthesis engine. It would monitor the MTG information ecosystem continuously, filter it for relevance, and surface the most important updates to each user in a personalized, digestible format. It is not a news website. It is a *news intelligence layer* — one that understands the game well enough to tell a Commander player that a ban announcement matters to them, and to tell a Standard player that the same announcement does not.

The types of content it would track fall into five natural categories:

| News Category | Examples | Update Frequency | Why It Matters |
| :--- | :--- | :--- | :--- |
| **Official Announcements** | New set reveals, ban/unban announcements, rules errata, product releases | Weekly to monthly | Directly affects card legality and collection value |
| **Spoiler Season** | New card previews during the 3–4 weeks before each set release | Daily during spoiler windows | Drives purchasing decisions and deck-building excitement |
| **Competitive Metagame** | Tournament results, top-8 decklists, emerging archetypes, format tier shifts | Weekly | Informs competitive players what is winning and why |
| **Community & Culture** | Creator content, community debates, notable plays, lore updates | Daily | Drives engagement and a sense of belonging |
| **Market Intelligence** | Price spikes, buyouts, reprint announcements, card value movements | Daily | Directly relevant to collectors and traders |

---

## The MTG News Ecosystem: What Already Exists

Before evaluating whether to build this feature, it is important to understand what the platform would be competing with and what it would be synthesizing from.

The MTG news landscape is large, fragmented, and heavily community-driven. The primary sources are:

**Official Sources:**
- **Wizards of the Coast (magic.wizards.com)** — The authoritative source for all official announcements, set previews, and ban list updates. Published on a predictable schedule.
- **MTG Arena and MTGO client announcements** — Digital-specific updates, often separate from the main site.

**Community Aggregators & Publications:**
- **MTGGoldfish** — The dominant platform for metagame data, price tracking, and competitive analysis. Publishes daily articles and a popular podcast.
- **MTGSalvation** — A long-running community wiki and news aggregator. Historically important but has declined in activity since 2019.
- **MagicSpoiler.com** — Dedicated to aggregating card spoilers during preview seasons.
- **Draftsim** — Publishes strategy guides, set reviews, and format analysis.
- **341 Magazine (ThreeForOneTrading)** — European-focused MTG news and event coverage.

**Content Creators (YouTube/Podcast/Streaming):**
- The MTG creator economy is substantial. Feedspot lists over 100 active MTG YouTube channels as of 2026 [[1]](#ref-1). Major channels include Gavin Verhey (Wizards employee), Tolarian Community College, MTGGoldfish, and dozens of format-specific creators.
- Reddit's r/magicTCG (over 500,000 members) functions as the de facto community news aggregator, with ban announcements and spoilers routinely hitting the front page within minutes of release.

**The Gap:** No existing platform synthesizes all of these sources and delivers them in a *personalized, context-aware* format. A Commander player currently has to manually check Wizards' site, r/magicTCG, EDHREC's blog, and their favorite YouTube channels to stay current. The Dispatch would do all of that automatically and filter it through what is relevant to *that specific player*.

---

## The ROI Case

### The Core Argument: News Drives Non-News Engagement

The most important piece of evidence for this feature's ROI comes from a peer-reviewed study published in *Marketing Science* (2025) by researchers at the University of Michigan. The study analyzed what happened when Facebook blocked all news content in Australia in February 2021. The findings were unambiguous:

> *"After Facebook stopped carrying news content, total user engagement with non-news content decreased by 10.7%, and the number of daily posts generated by non-news accounts dropped by 8.8%."* [[2]](#ref-2)

This is the "spillover effect" — news content does not just serve users who want news. It drives traffic and engagement across the entire platform. Users who come to check a ban announcement stay to update their deck. Users who come to read a spoiler stay to search for synergies with the new card. The news feature is a *daily engagement trigger* that activates the platform's core utility.

### The Stickiness Argument

Platform stickiness is measured by the ratio of Daily Active Users (DAU) to Monthly Active Users (MAU). A platform where users check in daily has a high stickiness ratio; a platform where users check in only when they remember to has a low one. The four confirmed pillars of GatherYourMagic are *reactive* features — users engage with them when they have a specific task (building a deck, checking a ruling). The Dispatch is a *proactive* feature — it gives users a reason to open the platform every single day, even when they have no specific task. This is the difference between a tool and a destination.

### The Monetization Argument

A news and content hub creates monetization pathways that the four core pillars do not:

- **Sponsored content / affiliate partnerships** — Card retailers (TCGplayer, CardMarket, Card Kingdom) pay for placement in price-spike alerts and spoiler coverage. When a new card is spoiled and The Dispatch notifies users, a "Buy Now" link to an affiliate partner is a natural, non-intrusive placement.
- **Display advertising** — A news feed with daily traffic supports programmatic advertising. This is a revenue stream that does not exist on a pure utility platform.
- **Premium newsletter tier** — A curated weekly digest of the most important MTG news, delivered via email, is a low-cost, high-value premium feature that justifies a subscription upgrade.
- **Creator partnerships** — MTG content creators are actively looking for distribution. GatherYourMagic could partner with established creators to feature their content, driving traffic to them while keeping users on the platform longer.

### The Authority Argument

The platform that becomes the single source of truth for a community's news becomes the platform that community trusts for everything else. As the content aggregation research notes, this creates brand authority that "outweighs any direct marketing you're doing to recruit members" [[3]](#ref-3). For GatherYourMagic, being the place where players learn about ban announcements, new set spoilers, and tournament results positions the platform as the hub of the MTG community — not just a tool players use occasionally.

---

## The Pros

**1. Daily engagement trigger.** News gives users a reason to open the platform every day, even between deck-building sessions. This is the single most powerful driver of DAU/MAU ratio improvement.

**2. Personalization advantage.** No existing news source filters MTG news by the user's format, playstyle, and collection. A Commander player does not need to know about Standard tournament results. A casual collector does not need to read about Legacy ban lists. GatherYourMagic already knows who each user is — The Dispatch uses that knowledge to deliver a personalized briefing, which no competitor can replicate.

**3. Collection value alerts.** When a card in a user's collection spikes in price due to a tournament result or reprint announcement, The Dispatch can notify them immediately. This is a high-value, highly personal feature that directly connects the news layer to the Library pillar.

**4. Spoiler integration.** During spoiler season (which occurs approximately 4–6 times per year and generates enormous community excitement), The Dispatch can show new cards alongside the user's existing collection, automatically flagging synergies. "This new card works with 3 cards you already own" is a powerful, collection-aware news experience.

**5. New revenue streams.** Affiliate links, display advertising, and sponsored content become viable only when the platform has daily traffic. The Dispatch enables this.

**6. SEO and organic growth.** A regularly updated news and content hub generates search engine traffic. Players searching for "MTG ban announcement May 2026" or "new Duskmourn spoilers" will land on GatherYourMagic, creating a top-of-funnel acquisition channel that does not require paid advertising.

---

## The Cons

**1. Content is a resource sink.** Even if The Dispatch is primarily an aggregator (pulling from existing sources rather than creating original content), it requires ongoing curation, quality control, and maintenance. Automated aggregation without human oversight will surface low-quality, inaccurate, or duplicate content.

**2. It is a different product discipline.** Building a card database and building a news platform require different skills. The development team for GatherYourMagic is focused on data engineering, AI, and game mechanics. Adding a media product introduces editorial judgment, content partnerships, and community management as new competencies.

**3. Copyright and licensing complexity.** Aggregating content from Wizards of the Coast, YouTube creators, and community sites raises questions about what can be reproduced, linked, or summarized. The platform must operate within clear legal boundaries — summarizing and linking is generally safe; reproducing full articles or card images without permission is not.

**4. Noise risk.** MTG generates an enormous volume of community content. An aggregator that surfaces too much, or the wrong things, becomes noise rather than signal. The filtering and personalization logic must be excellent from day one, or users will disable the feature entirely.

**5. Credibility risk.** If The Dispatch surfaces inaccurate information — a misreported ruling, a false spoiler, an incorrect price spike — the platform's credibility as a trusted source is damaged. The Oracle's accuracy standards must extend to the news layer.

---

## Most Important Factors to Consider

When the decision is made to evaluate this feature for active development, the following factors are the most critical:

**Factor 1: Build vs. Aggregate.** The platform should never attempt to *create* original news content. The Dispatch should be a pure aggregator and synthesizer — pulling from official sources (Wizards RSS feeds, Scryfall set release data, MTGGoldfish price data) and surfacing them in a personalized format. Original content creation is a separate business with separate economics.

**Factor 2: Personalization quality is the differentiator.** If The Dispatch shows every user the same news feed, it is indistinguishable from Reddit. The value is entirely in the personalization — filtering by format, by collection, by playstyle. This requires the Playstyle Profiler to be mature and accurate before The Dispatch can deliver on its promise.

**Factor 3: The sync architecture already supports this.** The local-first data architecture pillar already establishes that the platform will sync ban list data, set release data, and pricing data on a schedule. The Dispatch is, in part, a user-facing presentation layer on top of data the platform is already collecting. The marginal infrastructure cost is lower than it appears.

**Factor 4: Start with alerts, not a feed.** The lowest-risk entry point for this feature is not a full news feed — it is a targeted alert system. "A card in your collection spiked in price." "A card in your Commander deck was just banned." "A new set was announced featuring a mechanic you love." These alerts are high-value, low-noise, and directly connected to the user's existing data. They can be built incrementally without committing to a full media product.

**Factor 5: Partnership before production.** Before building any content aggregation infrastructure, the platform should establish relationships with 2–3 major MTG content creators and with the MTGGoldfish and Draftsim editorial teams. A content partnership — where their content is featured on GatherYourMagic with attribution and a traffic link — is mutually beneficial and requires no original content production.

---

## Recommended Scenarios

### Scenario A: The Minimum Viable Dispatch (Phase 3–4)
Implement a targeted alert system only. No news feed, no content aggregation. Users receive push notifications and in-app alerts for:
- Ban/unban announcements affecting their decks.
- Price spikes on cards they own.
- New set releases with cards that synergize with their collection.

**Effort:** Low. This is a presentation layer on data already being collected.
**Risk:** Very low.
**ROI:** Moderate — increases daily engagement and collection value awareness.

### Scenario B: The Personalized Feed (Phase 5+)
A full, personalized news feed aggregating official announcements, price movements, and curated community content, filtered by the user's profile. Includes spoiler season integration and creator partnerships.

**Effort:** High — requires editorial curation logic, content partnerships, and a dedicated product surface.
**Risk:** Medium — copyright, quality control, and resource allocation are real concerns.
**ROI:** High — creates daily engagement, new revenue streams, and platform authority.

### Scenario C: The Media Division (Post-Launch)
A standalone editorial product — original articles, a podcast, a weekly newsletter — built on the GatherYourMagic brand and audience. This is a separate business unit, not a feature.

**Effort:** Very high — requires dedicated editorial staff.
**Risk:** High — this is a media business, not a software feature.
**ROI:** Potentially very high, but only after the core platform has established a large, loyal user base.

---

## Conclusion

The Dispatch is not a distraction from the core platform — it is a natural extension of it. The research is clear that news content drives engagement with non-news content, and the MTG community's hunger for timely, relevant information is well-documented. The personalization advantage GatherYourMagic already has — knowing each user's format, collection, and playstyle — makes its version of this feature categorically superior to anything a general-purpose news site could offer.

The recommendation is to **hold this feature for Phase 3–4 evaluation**, beginning with the Minimum Viable Dispatch (targeted alerts only), and to treat the full personalized feed as a Phase 5+ investment once the core platform has proven its user base and the Playstyle Profiler has matured. The groundwork — the data sync architecture, the user profiles, the collection data — is already being laid. The Dispatch, when the time comes, will have a strong foundation to build on.

---

### References
[1] Feedspot. "100 Magic: The Gathering YouTubers You Must Follow in 2026." https://videos.feedspot.com/magic_the_gathering_youtube_channels/
[2] Song, Y. & Manchanda, P. (2025). "Does Carrying News Increase Engagement with Non-News Content on Social Media Platforms?" *Marketing Science*, INFORMS. https://pubsonline.informs.org/stoken/default+domain/PR-6-2025/full/10.1287/mksc.2024.0993
[3] Hostetter, A. (2025). "5 Smart Ways to Help Determine the ROI of Your Content Aggregation Project." FuseNext. https://www.fusenext.com/thinking-doing/5-smart-ways-to-help-determine-the-roi-of-your-content-aggregation-project
