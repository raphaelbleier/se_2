# Crypto Barons – Game Overview

## Concept

**Crypto Barons** is a satirical multiplayer card game for 2–6 players that simulates a deregulated
financial market driven by political arbitrariness and social media escapades. The game mechanically
abstracts corruption, insider trading, and market manipulation by political actors.

> "In this market, the rules exist to be bent — and the biggest cheater wins."

---

## Setting & Tone

The game world is a thinly veiled parody of contemporary crypto markets, populist politics, and
financial deregulation. All events, cards, and AI-generated commentary are satirical in nature and
meant to critique real-world phenomena through exaggeration.

---

## Core Currency

| Currency | Description |
|----------|-------------|
| **Freedom Dollars (FD)** | Primary in-game currency. Used for all transactions. |
| **PatriotCoin** | Volatile in-game crypto asset. High risk, high reward. |
| **LibertyDoge** | Meme-based volatile asset. Prone to pump-and-dump cycles. |
| **Custom Tokens** | Player-emitted tokens (via "Rug Pull" card). Worthless by design. |

---

## Win Condition

The first player to accumulate **1,000,000,000 FD (1 Billion Freedom Dollars)** wins the game.

---

## Player Count & Platform

- **Players:** 2–6 per session (multiplayer over network)
- **Platform:** Native Android App (min. SDK 26)
- **Session Type:** Real-time networked multiplayer via WebSockets

---

## Turn Structure Overview

Each game round consists of three sequential phases:

1. **News-Cycle Phase** — Server generates a global market event
2. **Lobbying Phase** — Players play cards to manipulate markets or sabotage opponents
3. **Audit Phase** — Transactions are synchronized; players may initiate investigations

See `game-mechanics.md` for detailed phase rules.

---

## Key Features

- **FreedomFeed — Player-Driven Social Manipulation** — Players post short messages that move asset prices via real-time Gemini sentiment analysis; bluffing, FUD, and coordinated pumps are core strategies
- **AI-Driven Market Commentary** — AI personas react autonomously to game events and player posts
- **Cheat Mechanic** — Hidden two-finger swipe gesture triggers secret fund transfer
- **Hardware Integration** — Biometric fingerprint sensor used for in-game "oath" verification
- **Persistent Leaderboard** — Global rankings stored in PostgreSQL, accessible via Android app and browser
- **Card-Based Market Manipulation** — 5 core card archetypes with cascading effects

---

## Satirical Design Pillars

| Pillar | Mechanic |
|--------|----------|
| Corruption | Cards grant passive income, immunity from regulation |
| Insider Trading | Pre-buying assets before announcing a pump via FreedomFeed |
| Market Manipulation via Social Media | Player posts analyzed by AI; sentiment directly moves prices — just like real-world tweets |
| Coordinated Pump & Dump | Multiple players stacking FreedomFeed posts amplifies price swings |
| Political Immunity | "Presidential Pardon" nullifies punishment |
| AI Amplification | AI personas react to player posts, amplifying or countering the intended effect |
