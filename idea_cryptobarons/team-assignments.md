# Team Assignments – Crypto Barons

## Core Rule

Each developer **fully owns at least one feature**, meaning they are responsible for:
1. **Analysis** — requirements, acceptance criteria, API contract definition
2. **Design** — UML, sequence diagrams, DB schema contribution
3. **Implementation** — both the Spring Boot backend **and** the Android frontend for their feature
4. **Testing** — unit tests, integration tests, manual test protocol

No developer is purely a backend or purely a frontend developer. Every feature touches both.

---

## Team (5 Developers)

---

### Developer 1 — Auth & Infrastructure

**Owned Feature:** Authentication system + project infrastructure + web leaderboard UI

**Backend Responsibilities:**
- Spring Boot project setup (Gradle, fat JAR via `bootJar`)
- Spring Security: `POST /auth/register` and `POST /auth/login` with JWT (bcrypt password hashing)
- Flyway database migration bootstrap
- Configuration profiles: `dev` (H2 or local PostgreSQL), `prod` (university server)
- University server deployment (JAR via SSH/SCP, environment variables for secrets)
- GitHub Actions CI/CD pipeline (build, test, SonarCloud analysis)
- SonarCloud project setup and Quality Gate configuration
- CORS configuration for the web leaderboard (leaderboard endpoints accessible from browser)
- Web leaderboard UI: static HTML/CSS/JS in `src/main/resources/static/leaderboard/` — serves from the same JAR

**Android Responsibilities:**
- Login screen (Retrofit call to `/auth/login`, JWT stored in `SharedPreferences` or `EncryptedSharedPreferences`)
- Register screen (Retrofit call to `/auth/register`, validation)
- Navigation from auth screens to lobby

**Key Deliverables:**
- `.github/workflows/ci.yml` — full CI pipeline (build + test + SonarCloud)
- `sonar-project.properties`
- `application.yml` (dev) and `application-prod.yml` (prod, no secrets committed)
- `DEPLOYMENT.md` — exact steps: build JAR, copy to server, start command, env var setup
- `src/main/resources/static/leaderboard/index.html` + `styles.css` + `leaderboard.js`
- Android: `LoginFragment` + `RegisterFragment` + `AuthViewModel`

**Tests to Write:**
- Backend: Integration tests for `/auth/register` and `/auth/login` (Spring Boot Test)
- Backend: Unit test for JWT token generation and validation
- Android: Unit tests for `AuthViewModel` (form validation, error states)
- Web: Manual test protocol — all 3 leaderboard tabs load, auto-refresh works, error state shown when backend unreachable

---

### Developer 2 — Game Engine

**Owned Feature:** Round-based state management + core game screen

**Backend Responsibilities:**
- Server-side game state machine: states `WAITING → GAME_STARTING → NEWS_CYCLE → LOBBYING → AUDIT → [HEARING] → ENDED`
- News-Cycle Phase: random event selection, AI sentiment trigger (calls Dev 4's `AiSentimentService`)
- Lobbying Phase: turn order management, card play processing, buy/sell execution, free-post counter reset
- Audit Phase: transaction sync, wealth delta calculation, win condition check
- All 7 card effects:
  - `3AM Capslock Tweet` — 5× price spike + mandatory crash next phase
  - `Alternative Facts` — retroactive loss inversion for card player
  - `Nepotism Board Member` — 50M FD/round passive income + regulatory immunity 2 rounds
  - `The Ultimate Rug Pull` — custom token issuance, forced investment, liquidity drain
  - `Presidential Pardon` — nullify hearing consequences
  - `Suppress the Post` — nullify target post's market impact
  - `Echo Chamber` — triple any post's market impact
- Market price algorithm: event multiplier layer × social post multiplier layer, stacking cap ±3.0

**Android Responsibilities:**
- Main game screen: card hand display, asset price ticker, FD balance, turn indicator
- Card play interaction: select card → confirm dialog → animated feedback
- Asset buy/sell UI: quantity input, price preview, confirm
- Post-game summary screen: final rankings, session statistics

**Key Deliverables:**
- `GameStateService.kt` — state machine
- `CardEffectService.kt` — all 7 card implementations
- `MarketService.kt` — price algorithm
- `GameSession`, `PlayerAsset`, `Card`, `Transaction` JPA entities
- State machine diagram (UML)
- Android: `GameFragment` / Compose screen, `GameViewModel`

**Tests to Write:**
- Unit tests: each card effect with all possible outcomes and edge cases
- Unit tests: state machine — every valid transition + rejection of invalid transitions
- Unit tests: market price algorithm — event + social layers combined, boundary values, stacking cap
- Android: Unit tests for `GameViewModel` state transformations
- Android: UI tests (Espresso) for card selection and play flow

---

### Developer 3 — Multiplayer & Lobby

**Owned Feature:** Real-time WebSocket multiplayer + lobby system

**Backend Responsibilities:**
- Spring WebSocket controller (STOMP over SockJS)
- Subscribe topics:
  - `/topic/game/{sessionId}` — full game state updates
  - `/topic/feed/{sessionId}` — FreedomFeed entries (player + AI)
  - `/topic/market/{sessionId}` — asset price updates
  - `/user/queue/audit` — private hearing notifications
  - `/user/queue/errors` — private error messages
- `SessionService`: create lobby, join lobby, start game, handle disconnects, session recovery
- `GameSession` JPA entity persistence (coordinates with Dev 2's schema)
- Player reconnect logic: re-subscribe to correct session state on reconnect

**Android Responsibilities:**
- `WebSocketManager`: singleton managing OkHttp WebSocket connection + STOMP frames, dispatches messages to the correct ViewModel
- Lobby screen: list open sessions (REST), create/join session (REST + WebSocket upgrade), player list updated live via WebSocket
- Handle network loss: reconnect with exponential backoff, rejoin session

**Key Deliverables:**
- `WebSocketController.kt` — all `@MessageMapping` handlers
- `SessionController.kt` — REST endpoints for session lifecycle
- `SessionService.kt`
- Sequence diagrams: join game flow, reconnect flow
- Android: `LobbyFragment` + `LobbyViewModel`, `WebSocketManager.kt`

**Tests to Write:**
- Unit tests: session lifecycle — create, join, start, disconnect, rejoin
- Integration test: two simulated WebSocket clients receive the same broadcast message
- Integration test: player disconnects mid-game → state is preserved → reconnect works
- Android: Unit tests for WebSocket message deserialisation (all message types)

---

### Developer 4 — AI & FreedomFeed

**Owned Feature:** AI Market Sentiment Engine + FreedomFeed social mechanic

**Backend Responsibilities:**
- `AiSentimentService`: build persona system prompts, call Gemini API asynchronously, parse response JSON
- Player post analysis: extract `sentiment` + `affectedAsset` + `isManipulation` from player-submitted text
- Autonomous AI event posts: trigger correct persona based on game event type
- AI reaction posts: 40% random probability when a player post is submitted
- `SocialPostService`: handle `POST /sessions/{id}/social/post`, validate free-post quota, deduct paid-post FD, queue market impact
- Verified status: `POST /sessions/{id}/social/verify` (50M FD, doubles post multiplier for round)
- Multiplier stacking: aggregate all post multipliers per phase, enforce ±3.0 cap
- `GET /sessions/{id}/social/feed` — paginated feed history
- Fallback: static text arrays per persona/sentiment when Gemini is unreachable
- `SocialPost` JPA entity persistence

**Android Responsibilities:**
- FreedomFeed panel: unified scrollable feed of player posts (blue border) and AI posts (orange border)
- Sentiment badge per post (green / red / grey)
- Post input field: 280-char counter, submit button, free-post counter badge
- Tap any post → expand detail (full text, recorded market impact, affected asset)
- Verified badge on verified player posts

**Key Deliverables:**
- `AiSentimentService.kt` — Gemini integration + fallback
- `SocialPostService.kt` — full posting pipeline
- `SocialPostController.kt` — REST endpoints
- `SocialPost` JPA entity + Flyway migration
- System prompts for all 4 personas documented in `ai-engine.md`
- Android: `FreedomFeedFragment` / Compose panel, `FeedViewModel`, `FeedEntryAdapter`

**Tests to Write:**
- Unit tests: sentiment extraction for all 5 sentiment values (mocked Gemini HTTP response)
- Unit tests: paid-post FD deduction — success + insufficient balance
- Unit tests: free-post counter reset at round start
- Unit tests: AI reaction probability gate (seed Random for determinism)
- Unit tests: multiplier stacking + cap enforcement
- Integration test: player post → sentiment analysis → market impact recorded

---

### Developer 5 — Leaderboard, Cheat & Hardware

**Owned Feature:** Leaderboard system + Congressional Hearing + biometric authentication + cheat gesture

**Backend Responsibilities:**
- `LeaderboardService`: calculate rankings for all 3 categories from DB
  - "Biggest Rug Pull" — max single FD gain via Rug Pull per player
  - "Most Pardons Used" — total Presidential Pardons played
  - "Top 1% Elite" — minimum rounds to reach 1 billion FD
- REST endpoints: `GET /api/v1/leaderboard/biggest-rug-pull`, `/most-pardons`, `/top-1-percent`
- `AuditService`:
  - `POST /sessions/{id}/audit/initiate` — charge 10M FD, open hearing, notify accused via `/user/queue/audit`
  - `POST /sessions/{id}/audit/{hearingId}/respond` — accept biometric result, apply verdict
  - Verdict logic: guilty → 50% confiscation to treasury; innocent → 20% from accuser to accused; pardon → dismiss
- Bailout validation: `POST /sessions/{id}/actions/cheat` — validate gesture hash, apply random FD transfer (5M–50M), record `BAILOUT` flag
- All DB schema Flyway migrations (all tables across the project, coordinates with Devs 2 and 4)
- `LeaderboardEntry`, `AuditEvent` JPA entities

**Android Responsibilities:**
- `CheatGestureDetector`: `GestureDetector` / `MotionEvent` — detect two-finger left-to-right swipe, transmit securely to backend
- `BiometricAuthHandler`: `BiometricPrompt` wrapper — fingerprint authentication with PIN fallback, sensor availability detection
- Congressional Hearing UI: receive `/user/queue/audit` notification → display hearing alert → 5-second countdown → launch biometric → transmit result → display verdict
- Android leaderboard screen: 3 tabs (RecyclerView per tab), refreshes after game session ends
- Post-game flow: navigate to leaderboard with updated stats after win/loss

**Key Deliverables:**
- `LeaderboardController.kt` + `LeaderboardService.kt`
- `AuditController.kt` + `AuditService.kt`
- Flyway migration scripts for all tables
- ER diagram (final, team-approved)
- Android: `CheatGestureDetector.kt`, `BiometricAuthHandler.kt`
- Android: `LeaderboardFragment` + `LeaderboardViewModel`
- Android: Hearing notification dialog + countdown view
- Security design note: cheat detection is server-authoritative (client cannot self-report wealth)

**Tests to Write:**
- Unit tests: leaderboard ranking for all 3 categories (various game histories)
- Unit tests: all 3 hearing verdicts — guilty (timeout), guilty (biometric fail), innocent (success), pardon override
- Unit tests: bailout FD range enforcement (5M–50M)
- Unit tests: gesture detection (simulated `MotionEvent` sequences — swipe vs non-swipe)
- Unit tests: biometric fallback paths (sensor unavailable, locked out, not enrolled)
- Integration test: full cheat → hearing → verdict → penalty end-to-end
- Repository tests: `LeaderboardEntry` aggregation queries

---

## Responsibility Matrix (RACI)

| Task | Dev 1 | Dev 2 | Dev 3 | Dev 4 | Dev 5 |
|------|-------|-------|-------|-------|-------|
| CI/CD Pipeline | **R** | I | I | I | I |
| SonarCloud | **R** | C | C | C | C |
| University server deploy | **R** | I | I | I | C |
| DB Schema + Migrations | C | C | I | C | **R** |
| Auth (register/login/JWT) | **R** | I | I | I | I |
| Android Login/Register screens | **R** | I | I | I | I |
| Game State Machine | I | **R** | C | I | I |
| Card Effects (all 7) | I | **R** | I | C | I |
| Market Price Algorithm | I | **R** | C | C | I |
| Android Game Screen | I | **R** | I | C | I |
| WebSocket Server (STOMP) | C | C | **R** | I | I |
| Lobby + Session Management | I | C | **R** | I | I |
| Android Lobby Screen + WS Client | I | I | **R** | I | I |
| Gemini API Integration | I | C | I | **R** | I |
| AI Personas + Prompts | I | I | I | **R** | I |
| SocialPostService | I | C | I | **R** | I |
| Android FreedomFeed Panel | I | I | C | **R** | I |
| Leaderboard Service + API | I | I | I | I | **R** |
| AuditService (Hearing Logic) | I | C | I | I | **R** |
| Biometric Auth | I | I | I | I | **R** |
| Cheat Gesture Detection | I | I | I | I | **R** |
| Android Leaderboard Screen | I | I | I | C | **R** |
| Web Leaderboard UI | **R** | I | I | I | C |
| CORS Configuration | **R** | I | I | I | C |

*R = Responsible, C = Consulted, I = Informed*
