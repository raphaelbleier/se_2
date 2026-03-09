# Sprint Plan – Crypto Barons

## Overview

Development follows Scrum across **3 Sprints** in Summer Semester 2026.
The team has **5 developers**. Each developer fully owns at least one feature end-to-end:
backend, Android frontend, and tests.

Weekly reviews are mandatory. Sprint 1 carries lower grading weight.

---

## Timeline

```
March          April              May                    June
───────────────────────────────────────────────────────────────────
Sprint 1                Sprint 2                Sprint 3
23.03 ──────── 20.04    27.04 ──────── 18.05    01.06 ──── 15.06
│    │         │        │    │    │    │         │    │     │
23.03 13.04 20.04       27.04 04.05 11.05 18.05  01.06 08.06 15.06
(plan)        (review)  (plan)          (review) (plan)    (final)
```

---

## Sprint 1: Foundation & Infrastructure

**Period:** 23.03 → 20.04
**Review Dates:** 23.03 (Sprint Planning), 13.04 (Mid-sprint), 20.04 (Sprint Review)
**Weight:** Lower (15 points max for functionality & quality)

### Goal
Every developer sets up the base of their feature. A working skeleton exists by 20.04:
the Android app connects to the backend, authentication works, and the university server
is reachable. No game logic required yet.

---

### Dev 1 — Auth & Infrastructure

**Feature:** Authentication + Project Setup

**Sprint 1 Tasks:**
- [ ] Create GitHub organisation and repositories (backend, android, separate branches)
- [ ] Configure branch protection on `main` and `develop`
- [ ] Set up GitHub Actions CI pipeline (build + lint for both repos)
- [ ] Integrate SonarCloud, configure Quality Gate
- [ ] Initialise Spring Boot project (Kotlin or Java, Gradle, fat JAR via `bootJar`)
- [ ] Configure PostgreSQL (local dev profile + production profile)
- [ ] Implement `POST /auth/register` and `POST /auth/login` with JWT (Spring Security + bcrypt)
- [ ] First deploy of the JAR to the university server (`java -jar app.jar --spring.profiles.active=prod`)
- [ ] Android: Login screen + Register screen (Retrofit calls to auth endpoints, JWT stored in SharedPreferences)
- [ ] Web leaderboard: Create `/resources/static/leaderboard/` folder, basic HTML skeleton (static placeholder data)

**Tests to write:**
- Unit tests: JWT generation + validation
- Integration tests: `POST /auth/register` and `POST /auth/login` (Spring Boot Test)
- Android unit tests: ViewModel logic for login/register forms

---

### Dev 2 — Game Engine

**Feature:** Round-based State Management + Core Game Screen

**Sprint 1 Tasks:**
- [ ] Define and document all REST API contracts (OpenAPI draft, shared with team)
- [ ] Design and finalise database schema — ERD covering all entities (Player, GameSession, Card, Transaction, SocialPost, LeaderboardEntry, AuditEvent)
- [ ] Implement `Player` JPA entity + repository
- [ ] Implement `GameSession` and `PlayerAsset` JPA entities
- [ ] Android: Core game screen skeleton (placeholder hand of cards, placeholder asset prices, balance display)

**Tests to write:**
- Unit tests: Player entity validation
- Repository tests: save/find Player (H2 in-memory for CI)

---

### Dev 3 — Multiplayer & Lobby

**Feature:** WebSocket Multiplayer + Lobby

**Sprint 1 Tasks:**
- [ ] Initialise Android project (Kotlin, Min SDK 26, navigation graph)
- [ ] Set up Retrofit and OkHttp dependencies
- [ ] Implement Login screen wired end-to-end to Dev 1's auth backend
- [ ] Establish basic WebSocket connection to backend (OkHttp STOMP client)
- [ ] Backend: `SessionController` stub — `POST /sessions` and `POST /sessions/{id}/join` (in-memory only, no persistence yet)
- [ ] Android: Lobby screen skeleton (list of open sessions, create/join buttons)

**Tests to write:**
- Unit tests: WebSocket message parsing (mock server)
- Integration test: Create session → join session (two simulated clients)

---

### Dev 4 — AI & FreedomFeed

**Feature:** AI Sentiment Engine + FreedomFeed

**Sprint 1 Tasks:**
- [ ] Proof of concept: single Gemini API call from Spring Boot (hardcoded prompt, log response)
- [ ] Define `SocialPost` JPA entity (playerId, text, analyzedSentiment, affectedAsset, marketImpact, round, timestamp)
- [ ] Define `FeedEntry` WebSocket message format (agreed with Dev 3)
- [ ] Android: FreedomFeed panel skeleton (scrollable list, static placeholder posts, text input field, 280-char counter)
- [ ] Document all 4 AI personas and their system prompts (in `ai-engine.md`)

**Tests to write:**
- Unit test: Gemini API client with mocked HTTP response
- Unit test: Sentiment JSON parsing (`BULLISH`/`BEARISH`/`NEUTRAL` extraction)

---

### Dev 5 — Leaderboard, Cheat & Hardware

**Feature:** Leaderboard + Congressional Hearing + Biometric Auth

**Sprint 1 Tasks:**
- [ ] **Proof of Concept:** Access Android `BiometricPrompt` API on a real device and emulator
- [ ] Test PIN fallback when no fingerprint sensor is available
- [ ] Document sensor detection logic
- [ ] Define `LeaderboardEntry` and `AuditEvent` JPA entities
- [ ] Implement `GET /api/v1/leaderboard/{category}` with static test data
- [ ] Android: Leaderboard screen skeleton (3 tabs: Biggest Rug Pull / Most Pardons / Top 1% Elite)

**Tests to write:**
- Unit test: Biometric availability detection (mocked `BiometricManager`)
- Unit test: PIN fallback path
- Repository test: Save and query `LeaderboardEntry`

---

### Sprint 1 Definition of Done
- [ ] CI pipeline passes on every PR to `develop`
- [ ] SonarCloud Quality Gate active and reporting
- [ ] Backend JAR deployed and reachable on university server
- [ ] Android app: login works end-to-end against live backend
- [ ] Biometric PoC demonstrated on device (Dev 5)
- [ ] Gemini API PoC demonstrated (Dev 4)
- [ ] Database schema ERD finalised and signed off by team

---

## Sprint 2: Core Gameplay & Network Synchronisation

**Period:** 27.04 → 18.05
**Review Dates:** 27.04 (Sprint Planning), 04.05, 11.05, 18.05 (Sprint Review)
**Weight:** High (20 points max for functionality & quality)

### Goal
A full 2-player game round is playable end-to-end. Cards work. The market moves. The
FreedomFeed posts affect asset prices. The leaderboard persists real data.

---

### Dev 1 — Auth & Infrastructure

**Sprint 2 Tasks:**
- [ ] Enhance CI pipeline: add test stage, coverage report upload to SonarCloud
- [ ] Set SonarCloud coverage threshold to ≥ 70% for new code
- [ ] Update university server deploy process (document steps in `DEPLOYMENT.md`)
- [ ] Web leaderboard: implement live `fetch()` calls to all 3 leaderboard REST endpoints
- [ ] Web leaderboard: tab switching between the 3 categories, auto-refresh every 30 seconds
- [ ] Coordinate server environment variable setup with team (DB password, Gemini key, JWT secret)

**Tests to write:**
- CI pipeline verification test (build + test on clean branch passes)
- Web leaderboard: manual test protocol (all 3 tabs load with real data)

---

### Dev 2 — Game Engine

**Sprint 2 Tasks:**
- [ ] Implement server-side game state machine (states: `WAITING`, `NEWS_CYCLE`, `LOBBYING`, `AUDIT`, `HEARING`, `ENDED`)
- [ ] Implement News-Cycle Phase: random event selection, AI trigger, market algorithm
- [ ] Implement Lobbying Phase: turn order, card play, buy/sell assets, free-post counter
- [ ] Implement Audit Phase: transaction sync, wealth delta broadcast
- [ ] Implement all 7 card effects: 3AM Capslock Tweet, Alternative Facts, Nepotism Board Member, The Ultimate Rug Pull, Presidential Pardon, Suppress the Post, Echo Chamber
- [ ] Implement market price volatility algorithm (event layer + social post layer)
- [ ] Android: Functional game screen — card hand, asset prices, balance, card play interaction with animations

**Tests to write:**
- Unit tests: all 7 card effects (test every outcome and edge case)
- Unit tests: market price algorithm (boundary values, stacking cap)
- Unit tests: state machine transitions (every valid and invalid transition)
- Android unit tests: `GameViewModel` state transformations

---

### Dev 3 — Multiplayer & Lobby

**Sprint 2 Tasks:**
- [ ] Implement Spring WebSocket controller (STOMP): subscribe topics for game state, market updates, feed entries
- [ ] Implement full lobby management: create, join, start game, player disconnect handling
- [ ] Real-time broadcast of game state to all connected clients after each phase
- [ ] Broadcast market price updates via `/topic/market/{sessionId}`
- [ ] Deliver AI feed entries via `/topic/feed/{sessionId}`
- [ ] Android: Lobby screen fully functional (create/join/list sessions via REST; live player list via WebSocket)
- [ ] Android: `WebSocketManager` — connects, subscribes to topics, dispatches messages to ViewModels

**Tests to write:**
- Unit tests: session lifecycle (create, join, start, disconnect, rejoin)
- Integration test: WebSocket message delivery to 2 simulated clients simultaneously
- Android unit tests: WebSocket message deserialisation

---

### Dev 4 — AI & FreedomFeed

**Sprint 2 Tasks:**
- [ ] Implement `SocialPostService`: accept player post text, call Gemini, extract sentiment + asset, queue market impact
- [ ] Implement free-post-per-round counter and paid-post FD deduction (5M FD)
- [ ] Implement `POST /sessions/{id}/social/post` REST endpoint
- [ ] Implement `POST /sessions/{id}/social/verify` endpoint (Verified status, 50M FD)
- [ ] Implement AI event posts (Personas A–D reacting to game events)
- [ ] Implement fallback: static text arrays per persona/sentiment when Gemini unavailable
- [ ] Android: FreedomFeed panel — live feed via WebSocket, post input with 280-char counter, free-post counter, sentiment-coloured borders

**Tests to write:**
- Unit tests: `SocialPostService` sentiment parsing for all sentiment values
- Unit tests: paid-post FD deduction logic (insufficient balance path)
- Unit tests: free-post counter reset per round
- Integration test: player post → Gemini mock → market impact applied

---

### Dev 5 — Leaderboard, Cheat & Hardware

**Sprint 2 Tasks:**
- [ ] Implement `LeaderboardService`: calculate all 3 rankings from DB (Biggest Rug Pull, Most Pardons, Top 1% Elite)
- [ ] Implement `GET /api/v1/leaderboard/biggest-rug-pull`, `/most-pardons`, `/top-1-percent`
- [ ] Implement `AuditService` backend: Congressional Hearing initiation (`POST /sessions/{id}/audit/initiate`) and response (`POST /sessions/{id}/audit/{hearingId}/respond`)
- [ ] Implement penalty logic: 50% confiscation on guilt, 20% transfer on false accusation
- [ ] Database: Flyway migration scripts for all tables
- [ ] Android: Leaderboard screen live (Retrofit calls to all 3 endpoints, RecyclerView per tab)

**Tests to write:**
- Unit tests: leaderboard ranking calculation (all 3 categories)
- Unit tests: penalty enforcement (all 3 hearing outcomes)
- Repository integration tests: leaderboard queries (H2 in-memory)
- Android unit tests: leaderboard data binding

---

### Sprint 2 Definition of Done
- [ ] Full 2-player game round playable end-to-end (all 3 phases)
- [ ] All 7 card types functional
- [ ] Player FreedomFeed posts move asset prices end-to-end
- [ ] AI event posts appear in the feed (fallback texts at minimum)
- [ ] Leaderboard persisted to DB and displayed in Android app
- [ ] Web leaderboard page shows live data in browser
- [ ] SonarCloud Quality Gate passes for all new code

---

## Sprint 3: Hardware, AI Completion & Finalisation

**Period:** 01.06 → 15.06
**Review Dates:** 01.06 (Sprint Planning), 08.06 (Mid-sprint), 15.06 (Final Review)
**Weight:** High (20 points max for functionality & quality)

### Goal
Hardware features complete. Full AI pipeline with all personas. Game is presentable and
bug-free. University server deployment stable. Presentations ready.

---

### Dev 1 — Auth & Infrastructure

**Sprint 3 Tasks:**
- [ ] Web leaderboard: error states (backend unreachable), loading states, responsive layout
- [ ] Web leaderboard: deployed and publicly accessible on university server (served from Spring Boot static resources)
- [ ] Final production deploy checklist: all env vars set, JAR starts cleanly, DB connected
- [ ] SonarCloud: zero blocker/critical issues team-wide, code smells reduced
- [ ] Prepare `DEPLOYMENT.md` with exact steps (build JAR, SCP to server, start command)

**Tests to write:**
- Manual test protocol for web leaderboard (3 tabs, auto-refresh, error state)

---

### Dev 2 — Game Engine

**Sprint 3 Tasks:**
- [ ] Game screen polish: error states, loading indicators, timeout handling
- [ ] All card edge cases resolved (e.g., Rug Pull with only 2 players, Pardon when already convicted)
- [ ] Winning condition detection and GAME_ENDED broadcast
- [ ] Post-game summary screen (final rankings, session stats)

**Tests to write:**
- Integration tests: full game round (news cycle → lobbying → audit) on test server
- Android UI tests (Espresso): card play flow, post-game screen

---

### Dev 3 — Multiplayer & Lobby

**Sprint 3 Tasks:**
- [ ] Private WebSocket notifications for audit events (`/user/queue/audit`)
- [ ] Reconnection and session recovery (rejoin after disconnect)
- [ ] Stress test: 4–6 concurrent players, verify no message loss or state drift
- [ ] Handle edge case: player leaves mid-game

**Tests to write:**
- Integration test: disconnect → reconnect → state is consistent
- Load test: 6 simulated concurrent WebSocket clients

---

### Dev 4 — AI & FreedomFeed

**Sprint 3 Tasks:**
- [ ] Complete prompt engineering for all 4 personas (Irrational Politician, Crypto Bro, Victimized Investor, Fictional Regulator)
- [ ] Implement AI persona reactions to player posts (40% probability trigger, server-side random)
- [ ] Implement Verified post multiplier (×2 on market impact)
- [ ] Implement stacking cap (±3.0 combined social post multiplier)
- [ ] Android: FreedomFeed polish — verified badge, tap-to-expand post detail, blue/orange border distinction, market impact shown per post

**Tests to write:**
- Unit tests: AI reaction probability gate (mock Random)
- Unit tests: Verified multiplier + stacking cap enforcement
- Android unit tests: FeedEntry rendering for player vs AI posts

---

### Dev 5 — Leaderboard, Cheat & Hardware

**Sprint 3 Tasks:**
- [ ] Implement two-finger swipe gesture detection (`GestureDetector` / `MotionEvent`) for Taxpayer Bailout
- [ ] Secure gesture event transmission to backend (`POST /sessions/{id}/actions/cheat`)
- [ ] Backend: validate bailout request, apply random FD transfer (5M–50M), record `BAILOUT` flag
- [ ] Full `BiometricPrompt` integration for Congressional Hearing response
- [ ] Hearing UI: 5-second countdown, biometric dialog, PIN fallback, result display
- [ ] Android: leaderboard screen updated after each game session ends
- [ ] End-to-end test: cheat gesture → accused → hearing → biometric → verdict → penalty

**Tests to write:**
- Unit tests: gesture detection (simulated `MotionEvent` sequences)
- Unit tests: biometric fallback (sensor unavailable / locked out paths)
- Unit tests: all 3 hearing verdicts (guilty, innocent, pardon override)
- Integration test: full cheat → hearing → penalty flow

---

### All Developers — Sprint 3 Final Tasks
- [ ] Resolve all SonarCloud blocker and critical issues
- [ ] Reduce code smells to acceptable level (team-agreed threshold)
- [ ] Write any missing unit/integration tests to reach ≥ 70% coverage
- [ ] Conduct Sprint 3 Review and Retrospective (15.06)
- [ ] Prepare demo and presentation slides

### Sprint 3 Definition of Done
- [ ] Cheat mechanic (two-finger swipe) works end-to-end
- [ ] Biometric auth (+ PIN fallback) works end-to-end for Congressional Hearing
- [ ] All 7 card types fully functional including edge cases
- [ ] Full AI pipeline: all 4 personas active, player post analysis + reactions working
- [ ] All 3 leaderboard categories displayed on Android and on web
- [ ] Web leaderboard accessible at stable URL on university server
- [ ] Backend JAR stable on university server, prod environment confirmed
- [ ] Android APK runs on Min SDK 26 device
- [ ] SonarCloud Quality Gate passes — zero blocker/critical issues

---

## Recurring Ceremonies

| Ceremony | Frequency | Duration | Attendance |
|----------|-----------|----------|-----------|
| Sprint Planning | Sprint start | 1–2 hours | All 5 |
| Weekly Review / Standup | Every review date | 30–60 min | All 5 |
| Sprint Retrospective | After final review of each sprint | 30 min | All 5 |

> **Attendance rule:** Total absences must stay below 3 hours across all mandatory dates
> for the maximum grade.
