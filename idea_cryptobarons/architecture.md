# System Architecture – Crypto Barons

## Overview

The system follows a strict client-server architecture with three tiers:
**Android Client → Spring Boot Backend → PostgreSQL Database**

The AI layer (Gemini API) is integrated exclusively on the server side.

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                        ANDROID CLIENTS                          │
│                                                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │  Player 1    │  │  Player 2    │  │  Player N    │         │
│  │  Android App │  │  Android App │  │  Android App │         │
│  │  (Kotlin)    │  │  (Kotlin)    │  │  (Kotlin)    │         │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘         │
│         │ Retrofit (REST)  │ OkHttp (WS)     │                 │
└─────────┼──────────────────┼─────────────────┼─────────────────┘
          │                  │                 │
          ▼                  ▼                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                    SPRING BOOT BACKEND                          │
│                    (Kotlin or Java)                             │
│                    Cloud PaaS (Render/Heroku)                   │
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐                     │
│  │  REST API Layer │  │  WebSocket Layer │                     │
│  │  @RestController│  │  @MessageMapping │                     │
│  │  - Auth         │  │  - GameState     │                     │
│  │  - Leaderboard  │  │  - AuditEvents   │                     │
│  │  - User Profile │  │  - NewsFeed      │                     │
│  └────────┬────────┘  └────────┬─────────┘                     │
│           │                    │                               │
│  ┌────────▼────────────────────▼─────────┐                     │
│  │           Service Layer               │                     │
│  │  - GameStateService (State Machine)   │                     │
│  │  - CardEffectService                  │                     │
│  │  - MarketService (Volatility Engine)  │                     │
│  │  - AuditService (Cheat Detection)     │                     │
│  │  - LeaderboardService                 │                     │
│  │  - AiSentimentService ──────────────► │──► Gemini API       │
│  └────────┬──────────────────────────────┘                     │
│           │                                                     │
│  ┌────────▼──────────────────────────────┐                     │
│  │      Spring Data JPA / Hibernate      │                     │
│  └────────┬──────────────────────────────┘                     │
└───────────┼─────────────────────────────────────────────────────┘
            │
            ▼
┌─────────────────────────────────────────────────────────────────┐
│                      POSTGRESQL DATABASE                        │
│                  Cloud DBaaS (Supabase/Render)                  │
│                                                                 │
│  Tables: players, game_sessions, game_states, cards,           │
│          transactions, leaderboard_entries, audit_events        │
└─────────────────────────────────────────────────────────────────┘
```

---

## Component Responsibilities

### Android Client (Frontend)

| Responsibility | Implementation |
|----------------|---------------|
| Display game state | Observe ViewModel, render via UI |
| Send player actions | Retrofit POST to REST API or WebSocket message |
| Receive real-time updates | OkHttp WebSocket listener |
| Cheat gesture detection | GestureDetector, two-finger swipe MotionEvent |
| Biometric authentication | AndroidX BiometricPrompt API |
| Display AI social feed | WebSocket-pushed ticker in RecyclerView/LazyColumn |
| Login / Registration | REST API calls with JWT token storage |

### Spring Boot Backend (Server)

| Responsibility | Implementation |
|----------------|---------------|
| Manage game sessions | GameSessionService, in-memory + DB sync |
| Run game state machine | GameStateService (state: WAITING, NEWS_CYCLE, LOBBYING, AUDIT, ENDED) |
| Process card effects | CardEffectService per card type |
| Calculate market volatility | MarketService with configurable algorithms |
| Generate AI commentary | AiSentimentService → Gemini API (async) |
| Broadcast updates | WebSocket STOMP topic `/topic/game/{sessionId}` |
| Validate audit events | AuditService (cheat/expose logic) |
| Persist data | JPA Repositories → PostgreSQL |
| Authenticate players | Spring Security + JWT |

### PostgreSQL Database

| Table | Purpose |
|-------|---------|
| `players` | User profiles, credentials (hashed), stats |
| `game_sessions` | Active and completed game sessions |
| `game_states` | Serialized state snapshots per round |
| `player_assets` | Per-session portfolio (FD, crypto holdings) |
| `card_inventory` | Cards in hand per player per session |
| `transactions` | All FD movements for audit trail |
| `leaderboard_entries` | Aggregated stats for global rankings |
| `audit_events` | Congressional hearing records |

---

## Communication Protocols

### REST API (Retrofit)
Used for: Authentication, lobby management, leaderboard queries, one-time actions.

```
Base URL: https://<backend-host>/api/v1/
Auth: Bearer JWT token in Authorization header
Format: JSON
```

### WebSocket (OkHttp / STOMP)
Used for: Real-time game state sync, AI feed, audit notifications.

```
Endpoint: wss://<backend-host>/ws
Protocol: STOMP over WebSocket
Topics:
  /topic/game/{sessionId}     ← broadcast game state updates
  /topic/feed/{sessionId}     ← AI-generated news feed
  /user/queue/audit           ← private audit notifications
```

---

## Data Flow: AI Market Sentiment

```
Player Action (e.g., "3AM Capslock Tweet")
    │
    ▼
CardEffectService.apply(card, gameState)
    │
    ▼
AiSentimentService.generateCommentary(trigger, context)  [async]
    │
    ▼
Gemini API Request (server-side, key in env var)
    │
    ▼
Parse response → extract sentiment (BULLISH / BEARISH)
    │
    ├── MarketService.applyVolatilityMultiplier(sentiment, asset)
    │       → updates asset prices in game state
    │
    └── WebSocketController.broadcastFeedEntry(generatedText, sessionId)
            → pushed to /topic/feed/{sessionId}
            → Android client renders in AI ticker
```

---

## Security Architecture

| Concern | Measure |
|---------|---------|
| API Key exposure | Gemini key in server env var only, never in APK |
| Authentication | JWT tokens, short expiry, refresh token rotation |
| Password storage | bcrypt hashing via Spring Security |
| Cheat validation | Server-side authoritative — client cannot self-report wealth |
| Biometric result | Android confirms locally, server validates session token |
| Input validation | All REST inputs validated with Bean Validation (`@Valid`) |
| HTTPS | TLS enforced on all endpoints (PaaS default) |
