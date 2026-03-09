# Game Mechanics – Crypto Barons

## Turn Structure

Each game round consists of three sequential phases executed for all players:

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  1. NEWS-CYCLE   │────►│  2. LOBBYING     │────►│  3. AUDIT        │
│     PHASE        │     │     PHASE        │     │     PHASE        │
│  (Server-side)   │     │  (Action Phase)  │     │  (Sync + Expose) │
└──────────────────┘     └──────────────────┘     └──────────────────┘
```

---

## Phase 1: News-Cycle Phase

**Trigger:** Automatic at the start of each round, server-initiated.

**Process:**
1. The Spring Boot backend randomly selects or generates a global market event
2. The AI Sentiment Engine (Gemini API) generates a satirical social media post for the event
3. Market asset prices are algorithmically adjusted based on event type and AI sentiment score
4. The generated event and price changes are broadcast to all clients via WebSocket

**Event Examples:**
- "Trade war announced on social media" → affects PatriotCoin (+30% volatility)
- "Central bank dissolved by executive order" → FD inflation spike
- "Crypto Guru arrested for fraud" → LibertyDoge -45%
- "New deregulation bill passed at 3AM" → all assets +20% for 1 round

**Market Algorithm:**
```
newPrice = currentPrice × (1 + baseVolatility × eventMultiplier × aiSentimentMultiplier)

where:
  baseVolatility      = per-asset constant (e.g., PatriotCoin: 0.15)
  eventMultiplier     = event type modifier (-2.0 to +2.0)
  aiSentimentMultiplier = derived from LLM response (-1.0 BEARISH to +1.0 BULLISH)
```

---

## Phase 2: Lobbying Phase (Action Phase)

**Trigger:** After News-Cycle Phase completes.

**Process:**
1. Each player has a hand of cards (drawn at game start, replenished each round)
2. Players take turns playing cards (turn order determined at game start)
3. Each player may play **up to 2 cards** per Lobbying Phase
4. Card effects are applied immediately and confirmed server-side
5. Players may also buy/sell assets during this phase using current market prices
6. Players may post to the **FreedomFeed** to manipulate market sentiment (see below)

**Hand Size:** 5 cards per player (replenished to 5 at start of each round)

**Asset Trading Rules:**
- Buy/sell any listed asset at current market price
- Transaction recorded server-side with timestamp for audit purposes
- Purchases made before a "3AM Capslock Tweet" are traceable

---

## FreedomFeed — Player Social Posts & Market Manipulation

The **FreedomFeed** is a shared in-game social media timeline, visible to all players in real time.
Players can post short messages to manipulate market sentiment, bluff other players, and trigger
AI reactions — exactly as powerful figures move real markets with social media posts.

> *"Why issue a press release when a single post in all caps moves billions?"*

### Posting Rules

| Rule | Value |
|------|-------|
| Max post length | 280 characters |
| Free posts per Lobbying Phase | 1 per player |
| Additional posts | 5,000,000 FD each ("Paid Promotion") |
| Timing | Any time during the Lobbying Phase |
| Visibility | Broadcast instantly to all players via WebSocket |

### How Posts Move Markets

When a player submits a post, the backend sends it to the **Gemini API** for sentiment analysis.
The AI extracts:
1. **Sentiment** (BULLISH / BEARISH / STRONGLY_BULLISH / STRONGLY_BEARISH / NEUTRAL)
2. **Mentioned asset** (PatriotCoin, LibertyDoge, any Custom Token, or "market general")

The extracted sentiment is converted to a **market multiplier** and applied to the mentioned asset's
price at the **end of the current Lobbying Phase** — after all players have posted and traded.

```
Player post submitted
       │
       ▼
SocialPostService.analyze(postText, playerId, sessionId)  [async, server-side]
       │
       ▼
Gemini API: sentiment analysis of player's text
       │
       ▼
Extract: sentiment + mentioned asset
       │
       ├── Apply volatility multiplier to asset price (queued, applied at phase end)
       │
       ├── Broadcast post to all players via /topic/feed/{sessionId}
       │
       └── Trigger AI persona reaction post (with probability, see ai-engine.md)
```

### Strategic Depth: Bluffing & Manipulation

Posts are **not fact-checked** by the game engine. Players can post anything:

| Strategy | Example Post | Intent |
|----------|-------------|--------|
| **Pump** | "Just bought 10,000 PatriotCoin. This is the moment. 🚀" | Drive up price before selling |
| **FUD** (Fear, Uncertainty, Doubt) | "Heard rumors the PatriotCoin dev team just rage-quit. Not financial advice." | Drive down price before buying |
| **Misdirection** | "LibertyDoge is about to moon, trust me bro" | Distract others while you buy PatriotCoin |
| **Coordinated Pump** | Multiple players post bullishly about same asset | Stack sentiment multipliers |
| **False Flag** | Post bearish content about your own asset, buy the dip you created | Advanced manipulation |

> Other players must decide whether to **believe and trade** on the post, or **ignore it as FUD**.
> The AI takes all posts at face value and reacts accordingly.

### Verified Checkmark (FD Multiplier)

Players may spend **50,000,000 FD** at the start of a round to activate **"Verified" status**
for that round. This doubles the market impact multiplier of all their FreedomFeed posts for
that round, simulating how high-profile accounts move markets more than anonymous ones.

| Status | Market Multiplier | Cost |
|--------|------------------|------|
| Normal post | 1× base sentiment multiplier | Free (1 per round) |
| Verified post | 2× base sentiment multiplier | 50,000,000 FD / round |
| Additional post | 1× base sentiment multiplier | 5,000,000 FD / post |

### Stacking Posts (Multiple Players)

If multiple players post bullishly about the same asset in the same Lobbying Phase, the sentiment
multipliers **stack additively**, up to a maximum cap of **+3.0× combined**. This makes coordinated
posting (or coordinated FUD) a powerful team strategy — but also a visible signal to alert opponents.

```
Combined multiplier = sum of all individual post multipliers (capped at ±3.0)
```

### Post History & Audit

All posts are stored in the database with `playerId`, `timestamp`, `text`, `analyzedSentiment`,
and `affectedAsset`. During the Audit Phase, any player can tap a post in the feed to see its
recorded market impact — making coordinated manipulation visible in hindsight.

---

## Phase 3: Audit Phase (Network Interaction)

**Trigger:** After all players complete their Lobbying Phase.

**Process:**
1. All transactions from the Lobbying Phase are synchronized to the database
2. Each player's wealth delta is broadcast to all players
3. Any player may initiate a **Congressional Hearing** against any other player
4. Only **one Congressional Hearing** per Audit Phase is permitted

---

## Cheat Mechanic: "Taxpayer Bailout"

### Activation
- **Gesture:** Two-finger swipe (direction: left-to-right) on the game screen
- **Timing:** Can be performed **at any time**, even outside the player's turn (asynchronous)
- **Visibility:** The gesture must be concealed from other players (social deception)
- **Effect:** Transfers a random amount of FD (5–50 million) from the "State Treasury" pool to the player's account
- **Server Validation:** The backend records the transaction with a special `BAILOUT` flag in the transaction table

### Detection Risk
- Other players observe a sudden wealth increase during the Audit Phase
- If another player suspects cheating, they may initiate a "Congressional Hearing"

---

## Expose Mechanic: "Congressional Hearing"

### Initiation
- Any player may accuse another player during the Audit Phase
- Accusation costs: **10 million FD** (refunded if accusation is upheld)
- Only one hearing per Audit Phase allowed

### Verification Process
```
Accusation made
      │
      ▼
Accused player receives notification on Android device
      │
      ▼
5-second countdown timer begins
      │
      ├── Device has fingerprint sensor?
      │         YES → BiometricPrompt displayed
      │         NO  → PIN entry fallback
      │
      ▼
Player responds within 5 seconds
```

### Outcomes

| Outcome | Condition | Consequence |
|---------|-----------|-------------|
| **Pleading the 5th** | Player does not authenticate within 5 seconds | Player guilty: **50% of total assets confiscated** and distributed to state treasury |
| **Authentication Failure** | Fingerprint/PIN rejected | Same as "Pleading the 5th" |
| **Authenticated (Innocent)** | Biometric/PIN accepted successfully | Accuser found guilty of "Fake News": must pay **20% of their assets** to the accused as "legal fees" |

### Presidential Pardon Override
- If the accused holds a "Presidential Pardon" card, they may play it immediately upon accusation
- The Pardon **nullifies all negative consequences** regardless of guilt
- The Pardon card is consumed after use

---

## State Machine (Server-Side)

```
WAITING_FOR_PLAYERS
      │ (min. 2 players joined)
      ▼
GAME_STARTING
      │ (cards dealt, assets initialized)
      ▼
NEWS_CYCLE ──────────────────────────────────────────┐
      │                                              │
      ▼                                              │
LOBBYING_PHASE                                       │
      │ (all players acted or timer expired)         │
      ▼                                              │
AUDIT_PHASE                                          │
      │ (transactions synced)                        │
      ├── [Congressional Hearing?] → HEARING         │
      │         │                                    │
      │         └──────────────────────────────────► │
      │                                              │
      ├── [Win condition met?] → GAME_ENDED          │
      │                                              │
      └────────────────────────────────────────────► ┘
                    (next round)
```

---

## Leaderboard System

### Categories

| Category | Metric | Description |
|----------|--------|-------------|
| **"Biggest Rug Pull"** | Max single-event profit via fraud | Highest FD gained in a single "Rug Pull" event |
| **"Most Pardons Used"** | Count of Presidential Pardons played | Total pardons used to avoid punishment |
| **"Top 1% Elite"** | Fastest win (rounds to 1B FD) | Minimum rounds to reach the win condition |

### Persistence
- All stats are persisted to PostgreSQL after each game session ends
- Leaderboard data is served via REST API (`GET /api/v1/leaderboard/{category}`)
- Updated in real-time during active sessions via WebSocket push

---

## Asset Price Model

| Asset | Base Price (FD) | Base Volatility | Special Rules |
|-------|----------------|-----------------|---------------|
| PatriotCoin | 1,000 | ±15% per round | Affected by "3AM Tweet" card and player posts |
| LibertyDoge | 500 | ±25% per round | Extra meme multiplier on player hype posts (×1.5) |
| Custom Token | Player-set | 100% → 0 | Destroyed on "Rug Pull" execution; player posts during setup inflate price |

### Full Price Formula (with Social Posts)

```
finalPrice = currentPrice
           × (1 + baseVolatility × eventMultiplier × aiEventSentiment)
           × (1 + clamp(sum(playerPostMultipliers), -3.0, +3.0))
```

*The event layer (News-Cycle + AI) and the social layer (player posts + AI reactions) are
applied multiplicatively, making a coordinated round of both cards and posts extremely powerful.*

---

## Round Timer

| Phase | Duration |
|-------|---------|
| News-Cycle Phase | 10 seconds (server-controlled) |
| Lobbying Phase | 90 seconds per player turn (cards + trades + posts) |
| FreedomFeed posting window | Concurrent with Lobbying Phase (not a separate timer) |
| Audit Phase | 30 seconds + hearing time |
| Congressional Hearing | 5 seconds for accused to authenticate |
