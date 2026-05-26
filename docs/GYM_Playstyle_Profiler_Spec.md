# GatherYourMagic.com
## Playstyle Profiler Specification

**Document Type:** Feature Specification
**Audience:** AI Engineers, Game Designers, Product Managers
**Date:** May 25, 2026

---

## 1. The Vision

The Playstyle Profiler is the intelligence layer that sets GatherYourMagic apart from every other database on the market. While competitors can tell a user what cards are mathematically optimal for a given commander, they cannot tell a user what cards they will actually *enjoy* playing.

The Profiler maps the user's psychological and tactical preferences to the Magic: The Gathering Color Pie, creating a persistent, evolving profile. This profile then acts as a filter for all recommendations made by the AI Coach.

---

## 2. The Foundation: The Psychographics of Magic

Wizards of the Coast internally categorizes players using three psychographic profiles. The Playstyle Profiler must measure these directly:

1. **Timmy/Tammy:** Plays to experience something. Values big moments, massive creatures, and spectacular interactions over strict win rates.
2. **Johnny/Jenny:** Plays to express something. Values creativity, complex combos, and winning on their own terms.
3. **Spike:** Plays to prove something. Values efficiency, consistency, and winning above all else.

A user's profile is not a single label, but a coordinate on a multi-dimensional matrix.

---

## 3. The Onboarding Assessment (The "Sorting Hat")

When a user creates an account, they are invited to take a short, engaging assessment to establish their baseline profile.

### 3.1 Assessment Design Principles
- **No Jargon:** Questions must be understandable to a new player. Instead of asking "Do you prefer Stax or Aggro?", ask "How do you react when your opponent plays a powerful card?"
- **Scenario-Based:** Place the user in a hypothetical game situation and ask them to choose a resolution.
- **Visual:** Use card art (with attribution) to illustrate the "vibe" of different choices.

### 3.2 Example Questions
**Q: You have 6 mana available. What do you want to do?**
- A) Play one massive, game-ending creature. *(Timmy / Green)*
- B) Keep the mana open to counter whatever my opponent does. *(Spike / Blue)*
- C) Play three small creatures and draw a card. *(Spike / White-Blue)*
- D) Play an enchantment that slowly drains everyone's life, including mine. *(Johnny / Black)*

---

## 4. The Dynamic Profile (Evolving Intelligence)

The onboarding assessment is only the baseline. The true power of the Profiler is that it learns from the user's actual behavior on the platform.

### 4.1 Data Inputs for the Evolving Profile
The system tracks and analyzes:
1. **Cards Added to Collection:** Are they mostly efficient removal spells, or huge dragons?
2. **Decks Built:** What is the average mana curve? What is the ratio of creatures to spells?
3. **Oracle/Coach Queries:** Does the user ask about infinite combos (Johnny), rules interactions (Spike), or tribal synergies (Timmy)?
4. **Win/Loss Tracking (Optional):** If the user logs their games, which decks actually win?

### 4.2 The Data Model

```json
{
  "user_id": "uuid",
  "psychographic_scores": {
    "timmy": 0.25,
    "johnny": 0.60,
    "spike": 0.15
  },
  "color_affinity": {
    "white": 0.10,
    "blue": 0.40,
    "black": 0.30,
    "red": 0.05,
    "green": 0.15
  },
  "archetype_preference": ["Combo", "Control"],
  "complexity_tolerance": 8, // Scale 1-10
  "budget_sensitivity": "high"
}
```

---

## 5. Integration with The AI Coach

The Playstyle Profile is passed as a system prompt parameter every time the user interacts with the AI Coach.

### 5.1 Scenario: Deck Optimization Request
**User Prompt:** "Help me improve my Edgar Markov deck. It feels too slow."

**Without Profiler (Competitor Behavior):**
"You should add *Mana Crypt* ($180) and *Vampiric Tutor* ($40) to speed up your mana and consistency."

**With Profiler (GatherYourMagic Behavior):**
*System context: User is a Johnny/Timmy hybrid, high budget sensitivity, loves tribal synergies.*
"Your deck is a bit slow because your mana curve is heavy at the top. Since you love tribal synergies but want to keep the budget reasonable, let's look at adding *Dark Ritual* alternatives. Have you considered *Cabal Ritual*? It's cheap, fits the dark theme, and gives you the burst mana you need. Also, let's swap out two of your 6-drop vampires for lower-cost utility vampires like *Viscera Seer*."

### 5.2 The "Push Me" Toggle
Users must have the ability to temporarily override their profile. If a Timmy player is entering a competitive tournament, they can toggle the Coach to "Spike Mode" to get ruthless, mathematically optimal advice regardless of their usual preferences.

---

## 6. Technical Implementation & Risks

### 6.1 Vector Embedding of Playstyles
To make this computationally efficient, the user's profile is embedded as a vector. When searching for card recommendations, the system performs a similarity search between the user's vector and the vector embeddings of various cards and archetypes in the local Pinecone database.

### 6.2 The "Pigeonhole" Risk
**Risk:** The system becomes an echo chamber, only recommending cards the user already knows they like, preventing them from discovering new playstyles.
**Mitigation:** The AI Coach must be programmed to occasionally offer "Wildcard" suggestions—cards that fit the deck mathematically but fall slightly outside the user's established psychographic profile, encouraging exploration.

---

## 7. Summary
The Playstyle Profiler transforms GatherYourMagic from a utility into a companion. By understanding *how* and *why* a user plays, the platform delivers personalized value that no static database can match.
