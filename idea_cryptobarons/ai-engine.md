# AI Market Sentiment Engine – Crypto Barons

## Overview

The AI Market Sentiment Engine integrates Google's Gemini API into the Spring Boot backend to
generate real-time, satirical social media commentary that dynamically influences in-game asset
prices and provides narrative flavor to market events.

> **Key Constraint:** All Gemini API calls are made **server-side only**. API keys must never
> be included in the Android APK or client-side code.

---

## System Architecture

```
Game Event / Card Trigger
         │
         ▼
AiSentimentService (Spring Boot)
         │
         ├── Build system prompt (persona + context)
         │
         ▼
Gemini API (async HTTP call, server-side)
         │
         ▼
Parse LLM Response
         │
         ├── Extract sentiment score (BULLISH / BEARISH / NEUTRAL)
         │         │
         │         └──► MarketService.applyVolatility(asset, sentimentMultiplier)
         │
         └── Format commentary text
                   │
                   └──► WebSocketController.broadcastFeedEntry(sessionId, entry)
                                 │
                                 └──► Android clients: In-Game Ticker / Social Feed
```

---

## Trigger Events

The following game events trigger an asynchronous Gemini API call:

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

## Social Feed UI (Android)

The generated AI commentary is displayed in an **In-Game Ticker / Social Feed**:

- Implemented as a horizontally scrolling ticker at the top of the game screen
- New entries slide in from the right (animated)
- Each entry shows: persona avatar icon, username (fictional), and generated text
- Color-coded by sentiment: green for BULLISH, red for BEARISH, grey for NEUTRAL
- Tapping an entry shows full text in a modal dialog

**WebSocket Message Format (Server → Client):**
```json
{
  "type": "FEED_ENTRY",
  "sessionId": "abc123",
  "timestamp": "2026-05-15T14:32:00Z",
  "persona": "IRRATIONAL_POLITICIAN",
  "username": "@RealDonalDuck45",
  "text": "SAD! PATRIOTCOIN IS BEING ATTACKED BY THE DEEP STATE!",
  "sentiment": "BULLISH",
  "triggeredBy": "CARD_3AM_TWEET",
  "affectedAsset": "PATRIOTCOIN"
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
