# Team Assignments – Crypto Barons

## Grading Requirement

Each team member must **fully own** at least one feature, covering:
1. **Analysis** — Requirements analysis and acceptance criteria
2. **Design** — UML diagrams, API contracts, DB schema contributions
3. **Implementation** — Code (frontend and/or backend)
4. **Testing** — Unit tests, integration tests, manual test protocol

---

## Developer Assignments (6-Person Team)

---

### Developer 1 — DevOps & Base Backend Architecture

**Owned Feature:** CI/CD Pipeline and Server Architecture

**Responsibilities:**
- GitHub organization setup (repositories, branch protection, PR templates)
- GitHub Actions CI/CD configuration (build, test, SonarCloud stages)
- SonarCloud project setup and Quality Gate configuration
- Spring Boot backend project initialization (project structure, Gradle config)
- Cloud hosting setup (Render / Heroku) with environment variable management
- PostgreSQL cloud instance setup and connectivity
- Database migration tooling (Flyway or Liquibase)

**Key Deliverables:**
- `.github/workflows/ci.yml` — Full CI pipeline
- `sonar-project.properties` — SonarCloud config
- Backend project skeleton with `application.yml` for all environments
- Documented deployment process in `DEPLOYMENT.md`

**Tests to Write:**
- Pipeline integration test (ensure build + test pass on clean branch)
- Smoke test: backend health endpoint reachable after deploy

---

### Developer 2 — Core Game Engine

**Owned Feature:** Round-Based State Management

**Responsibilities:**
- Server-side game state machine implementation
- News-Cycle Phase logic (event generation, market algorithm)
- Lobbying Phase turn management
- All 5 card effect implementations
- Market price volatility algorithm
- Gemini API integration (`AiSentimentService`)
- Sentiment-to-market-multiplier translation algorithm

**Key Deliverables:**
- `GameStateService.kt` / `GameStateService.java`
- `CardEffectService.kt` with all 5 card types
- `MarketService.kt` with volatility model
- `AiSentimentService.kt` with Gemini integration and fallback
- State machine diagram (UML)

**Tests to Write:**
- Unit tests for each card effect (test all outcomes)
- Unit tests for market price algorithm (boundary values)
- Unit tests for state machine transitions
- Integration test: Gemini API with mocked HTTP client

---

### Developer 3 — Network & Synchronization

**Owned Feature:** Multiplayer via Network (WebSockets)

**Responsibilities:**
- Spring Boot WebSocket controller (STOMP)
- Session/lobby management (create, join, start, disconnect handling)
- Real-time game state broadcast to all players
- AI feed delivery via WebSocket
- Android WebSocket client implementation (OkHttp + STOMP)
- Reconnection and session recovery logic

**Key Deliverables:**
- `WebSocketController.kt` — all STOMP message mappings
- `SessionService.kt` — lobby and player session management
- Android `WebSocketManager.kt` / equivalent class
- Sequence diagrams for key network interactions

**Tests to Write:**
- Unit tests for session management (join, leave, rejoin)
- Integration test: WebSocket message delivery (2 simulated clients)
- Android unit tests for WebSocket message parsing

---

### Developer 4 — Android UI & UX

**Owned Feature:** Main Game Screen

**Responsibilities:**
- Android UI implementation (all screens)
- Navigation graph (Login → Lobby → Game → Post-Game → Leaderboard)
- Game screen: card hand, asset prices, balance display, turn indicator
- AI news ticker (horizontal scrolling, animated, sentiment-colored)
- Card play interactions (select card, confirm, animated feedback)
- Congressional Hearing UI (5-second countdown, biometric dialog trigger)
- Lobby screen (create/join/player list)

**Key Deliverables:**
- All Activity / Fragment XML layouts or Compose screens
- `GameViewModel.kt` — state management for game screen
- `CardAdapter.kt` (or Compose equivalent) for hand display
- `NewsFeedAdapter.kt` for AI ticker
- UX wireframes and screen flow diagram

**Tests to Write:**
- Unit tests for GameViewModel state transformations
- UI tests (Espresso / Compose Test) for card play interaction
- Unit tests for news feed parsing and display logic

---

### Developer 5 — Hardware Integration & Anti-Cheat

**Owned Feature:** Cheat Mechanic and Congressional Hearing

**Responsibilities:**
- Two-finger swipe gesture detection (`GestureDetector` / `MotionEvent`)
- Secure transmission of "Taxpayer Bailout" gesture to server
- Android BiometricPrompt integration (fingerprint + PIN fallback)
- Biometric availability detection (sensor present / not present / locked out)
- Congressional Hearing response flow (receive notification → authenticate → submit)
- Server-side audit validation and penalty enforcement (with Dev 2)

**Key Deliverables:**
- `CheatGestureDetector.kt` — two-finger swipe detection
- `BiometricAuthHandler.kt` — BiometricPrompt wrapper with PIN fallback
- `AuditService.kt` (backend) — hearing validation and penalty logic
- Security design document: how cheat detection is server-authoritative

**Tests to Write:**
- Unit tests for gesture detection (simulated touch events)
- Unit tests for biometric fallback logic (sensor unavailable path)
- Backend unit tests for AuditService (all 3 hearing outcomes)
- Integration test: full cheat → hearing → verdict flow

---

### Developer 6 — Leaderboard & Persistence

**Owned Feature:** Global Leaderboards

**Responsibilities:**
- Database schema for all leaderboard statistics
- JPA entities and repositories for all tables
- `LeaderboardService.kt` — ranking calculation logic
- REST API endpoints for all 3 leaderboard categories
- Android leaderboard screen (Retrofit integration, RecyclerView/LazyColumn)
- Post-game screen with session summary and rank update

**Key Deliverables:**
- JPA entities: `Player`, `GameSession`, `LeaderboardEntry`, `Transaction`
- Flyway migration scripts for all tables
- `LeaderboardController.kt` — REST endpoints
- Android `LeaderboardFragment` / Compose screen
- ER diagram (final, approved)

**Tests to Write:**
- Unit tests for ranking calculation (all 3 categories)
- Repository integration tests (H2 in-memory for CI)
- Android unit tests for leaderboard data binding
- End-to-end test: game ends → stats persisted → leaderboard updated

---

## Responsibility Matrix (RACI)

| Task | Dev 1 | Dev 2 | Dev 3 | Dev 4 | Dev 5 | Dev 6 |
|------|-------|-------|-------|-------|-------|-------|
| CI/CD Pipeline | **R** | I | I | I | I | I |
| SonarCloud | **R** | C | C | C | C | C |
| DB Schema | C | C | I | I | I | **R** |
| Game State Machine | I | **R** | C | I | C | I |
| Card Effects | I | **R** | I | C | I | I |
| Gemini API | I | **R** | C | I | I | I |
| WebSocket Server | C | C | **R** | I | I | I |
| Android WebSocket | I | I | **R** | C | I | I |
| Android UI | I | I | C | **R** | C | C |
| Biometric Auth | I | I | I | C | **R** | I |
| Cheat Gesture | I | I | I | C | **R** | I |
| Leaderboard BE | I | I | I | I | I | **R** |
| Leaderboard FE | I | I | I | C | I | **R** |

*R = Responsible, C = Consulted, I = Informed*
