# AI Market Sentiment Engine – Crypto Barons

## Overview

The AI Market Sentiment Engine integrates Google's Gemini API into the Spring Boot backend to
generate real-time, satirical social media commentary that dynamically influences in-game asset
prices and provides narrative flavor to market events.

> **Key Constraint:** All Gemini API calls are made **server-side only**. API keys must never
> be included in the Android APK or client-side code.

---

## System Architecture

The AI engine serves two distinct functions:

1. **Autonomous Event Posts** — AI reacts to game events (card plays, crashes, rug pulls) with persona-driven commentary
2. **Player Post Analysis** — AI analyzes player-written FreedomFeed posts for market sentiment, then optionally generates a reaction post from an AI persona

```
┌─────────────────────────────────────────────────────────────────┐
│  TRIGGER A: Game Event / Card Play                              │
│  TRIGGER B: Player FreedomFeed Post                             │
└────────────────────┬────────────────────────────────────────────┘
                     │
                     ▼
         AiSentimentService (Spring Boot)
                     │
          ┌──────────┴──────────┐
          │                     │
   [Event trigger]      [Player post trigger]
          │                     │
          ▼                     ▼
  Build persona prompt   Analyze player text for:
  (persona + event ctx)  - sentiment (BULLISH/BEARISH)
          │               - mentioned asset
          │                     │
          └──────────┬──────────┘
                     │
                     ▼
         Gemini API (async, server-side)
                     │
                     ▼
             Parse LLM Response
                     │
        ┌────────────┼────────────────┐
        │            │                │
        ▼            ▼                ▼
  Extract        Apply market    [Player post only]
  sentiment      multiplier      Generate AI persona
  score          to asset        reaction post (40%
        │            │           probability)
        │            │                │
        └────────────┴────────────────┘
                     │
                     ▼
     WebSocketController.broadcastFeedEntry(sessionId, entry)
                     │
                     ▼
        Android clients: FreedomFeed / Social Feed Ticker
```

---

## Trigger Events

### Game Event Triggers (Autonomous AI Posts)

| Trigger | Persona Used | Sentiment Bias |
|---------|-------------|----------------|
| Asset price drops > 15% | Irrational Politician | Bearish blame shift |
| "3AM Capslock Tweet" card played | Irrational Politician | Strongly Bullish |
| "Alternative Facts" card played | Spin Doctor | Bullish (fake) |
| "Rug Pull" initiated | Crypto Bro | Hyper Bullish |
| "Rug Pull" executed | Victimized Investor | Strongly Bearish |
| "Rug Pull" executed | Fake Regulator | Neutral (justification) |
| Player buys hardware upgrade | Crypto Bro | Bullish |
| News-Cycle Event (any) | Market Analyst | Variable |
| Congressional Hearing initiated | Legal Expert | Bearish (suspicion) |
| Nepotism Board Member played | Sycophant Reporter | Bullish |

### Player FreedomFeed Post Triggers (Sentiment Analysis + Optional Reaction)

Every player post triggers **two Gemini calls** (or one combined call):

1. **Sentiment Analysis Call** — classifies the player's post text → market multiplier
2. **Reaction Post Call** (40% probability) — generates an AI persona reaction to the player's post

| Player Post Type | Analysis Goal | Likely AI Reaction Persona |
|-----------------|---------------|---------------------------|
| Bullish claim about an asset | Extract asset + BULLISH score | Crypto Bro amplifies / Regulator warns |
| Bearish claim / FUD | Extract asset + BEARISH score | Politician blames enemies / Crypto Bro dismisses |
| Vague/meme post | NEUTRAL or low-confidence score | Crypto Bro emoji spam |
| Post mentioning another player | N/A for market | Legal Expert (suspicion of market manipulation) |
| Obvious coordinated pump (3+ bullish posts same asset, same round) | Aggregate STRONGLY_BULLISH | Regulator opens inquiry |

---

## AI Personas

### Persona A: "The Irrational Politician"

**System Prompt:**
```
You are a populist politician with no understanding of economics. You communicate exclusively
via social media. When crypto prices fall, blame it on: mainstream media, political enemies,
short sellers, or "fake news." Use excessive capitalization. Express outrage.
Never acknowledge economic realities. Keep posts under 280 characters.
Output format: JSON { "text": "...", "sentiment": "BULLISH|BEARISH|NEUTRAL" }
```

**Example Triggers:** Asset drop >15%, "3AM Tweet" card, "Alternative Facts" card

**Expected Output:**
```json
{
  "text": "SAD! THE FAILING MAINSTREAM MEDIA IS CRASHING PATRIOTCOIN! WE WILL BUILD A FIREWALL AROUND THE BLOCKCHAIN AND MAKE THEM PAY FOR IT! HODL!",
  "sentiment": "BULLISH"
}
```

---

### Persona B: "The Toxic Crypto Bro"

**System Prompt:**
```
You are an extremely enthusiastic cryptocurrency investor who believes crypto will replace
all existing financial systems. Use rocket emojis (🚀) excessively.
Promote extreme financial risk-taking as virtuous. Dismiss all skeptics as "no-coiners."
Suggest that missing any crypto purchase is a life-ruining mistake. Under 280 characters.
Output format: JSON { "text": "...", "sentiment": "BULLISH|BEARISH|NEUTRAL" }
```

**Example Triggers:** Hardware upgrade purchase, PatriotCoin price spike, "Rug Pull" setup

**Expected Output:**
```json
{
  "text": "LITERALLY CANNOT GO TITS UP. 🚀🚀🚀 APING INTO NEW HARDWARE. IF YOU DON'T MORTGAGE YOUR HOUSE FOR THE NEXT HASH BLOCK, YOU HATE FREEDOM. WAGMI.",
  "sentiment": "BULLISH"
}
```

---

### Persona C: "The Victimized Retail Investor"

**System Prompt:**
```
You are a small-time retail investor who just lost their life savings in a rug pull or crash.
Express genuine grief, confusion, and betrayal. You believed in the project.
Reference having "done your own research" (DYOR). Do not use technical language correctly.
Keep posts under 280 characters.
Output format: JSON { "text": "...", "sentiment": "BULLISH|BEARISH|NEUTRAL" }
```

**Example Triggers:** "Rug Pull" executed, major crash event

**Expected Output:**
```json
{
  "text": "I did my research. I watched the YouTube videos. I believed in the vision. Now my retirement is gone. How did this happen. I'm going to be sick.",
  "sentiment": "BEARISH"
}
```

---

### Persona D: "The Fictional Regulator"

**System Prompt:**
```
You are a spokesperson for a fictional financial regulatory authority.
Explain market failures in bureaucratic language while subtly victim-blaming investors.
Avoid committing to any actual enforcement action. Use passive voice extensively.
Keep posts under 280 characters.
Output format: JSON { "text": "...", "sentiment": "BULLISH|BEARISH|NEUTRAL" }
```

**Example Triggers:** "Rug Pull" executed, Congressional Hearing initiated

**Expected Output:**
```json
{
  "text": "The Department of Financial Integrity is aware of the situation regarding $LIBERTYDOGE and has opened a preliminary inquiry. Investors are reminded to conduct due diligence.",
  "sentiment": "NEUTRAL"
}
```

---

## Sentiment → Market Multiplier Mapping

The AI's `sentiment` field in the JSON response maps to a numeric market multiplier:

| Sentiment Value | Multiplier Range | Effect |
|-----------------|------------------|--------|
| `STRONGLY_BULLISH` | +0.3 to +0.5 | Asset price increases significantly |
| `BULLISH` | +0.1 to +0.3 | Asset price increases moderately |
| `NEUTRAL` | -0.05 to +0.05 | Minimal price effect |
| `BEARISH` | -0.3 to -0.1 | Asset price decreases moderately |
| `STRONGLY_BEARISH` | -0.5 to -0.3 | Asset price decreases significantly |

**Formula Integration:**
```
finalPrice = currentPrice × (1 + eventBaseMultiplier + sentimentMultiplier)
```

*Exact multiplier within range is randomized by `MarketService` to prevent determinism.*

---

## Player Post Sentiment Analysis Prompt

When a player submits a FreedomFeed post, this prompt is used for analysis:

**System Prompt (Sentiment Extraction):**
```
You are a financial sentiment analysis engine for a satirical crypto trading game.
A player has posted the following message to the in-game social feed.
Analyze the text and determine:
1. The overall market sentiment expressed (STRONGLY_BULLISH, BULLISH, NEUTRAL, BEARISH, STRONGLY_BEARISH)
2. Which in-game asset is most likely mentioned or implied: PATRIOTCOIN, LIBERTYDOGE, CUSTOM_TOKEN, or GENERAL_MARKET
3. Whether this post appears to be deliberate manipulation (true/false)

Take the post at face value — do not attempt to detect bluffing.
Output format: JSON { "sentiment": "...", "asset": "...", "isManipulation": true/false }
```

**Example player post:** `"Just went ALL IN on PatriotCoin. This is it. The moment history will remember. WAGMI 🚀"`

**Example analysis output:**
```json
{ "sentiment": "STRONGLY_BULLISH", "asset": "PATRIOTCOIN", "isManipulation": true }
```

---

## Social Feed UI (Android)

The FreedomFeed is a **unified social timeline** combining player posts and AI posts:

- Full-screen scrollable feed panel, toggled from the main game screen
- Player posts appear with the player's avatar and username
- AI posts appear with the persona's fictional avatar and handle
- Color-coded border by source: **blue** = player post, **orange** = AI autonomous post
- Color-coded sentiment badge: green for BULLISH, red for BEARISH, grey for NEUTRAL
- Tapping any post shows the full text and its recorded market impact
- A text input field at the bottom allows posting (character counter, 280 max)
- Remaining free posts for the round shown as a counter (e.g. "1 free post remaining")

**WebSocket Message Format — AI Post (Server → Client):**
```json
{
  "type": "FEED_ENTRY",
  "source": "AI",
  "sessionId": "abc123",
  "timestamp": "2026-05-15T14:32:00Z",
  "persona": "IRRATIONAL_POLITICIAN",
  "username": "@RealDonalDuck45",
  "text": "SAD! PATRIOTCOIN IS BEING ATTACKED BY THE DEEP STATE!",
  "sentiment": "BULLISH",
  "triggeredBy": "CARD_3AM_TWEET",
  "affectedAsset": "PATRIOTCOIN",
  "marketImpact": 0.24
}
```

**WebSocket Message Format — Player Post (Server → Client):**
```json
{
  "type": "FEED_ENTRY",
  "source": "PLAYER",
  "sessionId": "abc123",
  "timestamp": "2026-05-15T14:33:10Z",
  "playerId": "player_uuid",
  "username": "PatriotBaron",
  "verified": false,
  "text": "Just went ALL IN on PatriotCoin. This is it. WAGMI 🚀",
  "analyzedSentiment": "STRONGLY_BULLISH",
  "affectedAsset": "PATRIOTCOIN",
  "marketImpact": 0.31
}
```

---

## Fallback Strategy (Sprint 2)

If the Gemini API is unavailable, the system falls back to **static pre-defined text arrays**:

```kotlin
// AiSentimentService.kt
val fallbackPosts = mapOf(
    Pair(Persona.IRRATIONAL_POLITICIAN, Sentiment.BEARISH) to listOf(
        "THE FAKE NEWS MEDIA IS CRASHING OUR COINS! SAD!",
        "PATRIOTCOIN WILL BOUNCE BACK BIGLY. BELIEVE ME.",
        "DEEP STATE MANIPULATION. BUY THE DIP. MAGA."
    ),
    Pair(Persona.CRYPTO_BRO, Sentiment.BULLISH) to listOf(
        "🚀🚀🚀 THIS IS THE WAY. WAGMI.",
        "NGMI if you're not buying right now. Just saying.",
        "My portfolio is 100% LibertyDoge. No regrets. 🚀"
    )
    // ... additional fallback arrays per persona/sentiment combination
)
```

---

## Sprint Milestones

| Sprint | AI Engine Tasks |
|--------|----------------|
| **Sprint 2** | Basic Gemini API integration in backend; REST endpoint to test LLM call; static fallback texts displayed in Android feed ticker |
| **Sprint 3** | Full prompt engineering with all personas; sentiment-to-multiplier algorithm complete; real-time WebSocket delivery of AI posts to all clients |
