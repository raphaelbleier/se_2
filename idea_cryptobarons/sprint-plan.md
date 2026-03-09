# Sprint Plan – Crypto Barons

## Overview

Development follows the Scrum methodology across **3 Sprints** in Summer Semester 2026.
Sprint 1 has lower weighting in grading. Sprints 2 and 3 carry the most points.

---

## Timeline

```
March 2026      April 2026         May 2026            June 2026
────────────────────────────────────────────────────────────────────
[Project Start] [S1 Review 14./16.] [S2 Review 11./12.] [S3 Reviews 09./11./16./18.]
                                                         [Presentations 22.-25.06.]
```

---

## Sprint 1: Infrastructure & Prototyping

**Duration:** Project start → Mid-April 2026
**Review Dates:** 14.04.2026 and 16.04.2026
**Weight:** Lower (15 points max for functionality & quality)

### Goal
Establish the technical foundation: repositories, CI/CD, base architecture, and a first
running prototype demonstrating connectivity between Android client and Spring Boot backend.

### Stories & Tasks

#### Dev 1 (DevOps & Base Backend)
- [ ] Create GitHub organization and repositories (frontend, backend)
- [ ] Configure branch protection rules (`main`, `develop`)
- [ ] Set up GitHub Actions CI pipeline (build + lint)
- [ ] Integrate SonarCloud with quality gate
- [ ] Initialize Spring Boot project (Kotlin or Java, Gradle)
- [ ] Configure PostgreSQL connection (local dev + cloud staging)
- [ ] Deploy first "Hello World" backend to cloud PaaS (Render/Heroku)

#### Dev 2 (Core Game Engine) — Sprint 1 Focus
- [ ] Define REST API contracts (OpenAPI/Swagger spec draft)
- [ ] Design database schema (ERD for all entities)
- [ ] Implement Player entity + repository (JPA)
- [ ] Implement basic authentication (register/login + JWT)

#### Dev 3 (Network & Sync) — Sprint 1 Focus
- [ ] Initialize Android project structure (Kotlin, Min SDK 26)
- [ ] Set up navigation graph (Login → Lobby → Game screens)
- [ ] Implement Login screen + Retrofit integration
- [ ] Establish WebSocket connection to backend (OkHttp + STOMP)

#### Dev 4 (Android UI) — Sprint 1 Focus
- [ ] Design wireframes for all major screens
- [ ] Implement Lobby screen UI
- [ ] Implement basic Game screen layout (placeholder data)

#### Dev 5 (Hardware & Anti-Cheat) — Sprint 1 Focus
- [ ] **Proof of Concept:** Access Android BiometricPrompt API
- [ ] Test fingerprint authentication flow on device and emulator (PIN fallback)
- [ ] Document sensor availability detection logic

#### Dev 6 (Leaderboard & Persistence) — Sprint 1 Focus
- [ ] Define leaderboard database schema
- [ ] Implement basic REST endpoint `GET /leaderboard/{category}` (static test data)
- [ ] Design Android leaderboard screen (static mockup)

### Sprint 1 Definition of Done
- [ ] GitHub Actions pipeline passes on PR to `develop`
- [ ] SonarCloud integrated and Quality Gate active
- [ ] Backend deployed and reachable at cloud URL
- [ ] Android app connects to backend (login works end-to-end)
- [ ] Biometric PoC demonstrated on device

---

## Sprint 2: Core Gameplay & Network Sync

**Duration:** Mid-April → Mid-May 2026
**Review Dates:** 11.05.2026 and 12.05.2026
**Weight:** High (20 points max for functionality & quality)

### Goal
Implement the core game loop, card mechanics, multiplayer synchronization, AI integration
(basic), and a functional leaderboard.

### Stories & Tasks

#### Dev 2 (Core Game Engine)
- [ ] Implement server-side game state machine (all states + transitions)
- [ ] Implement News-Cycle Phase event generation
- [ ] Implement Lobbying Phase turn management
- [ ] Implement Audit Phase transaction sync
- [ ] Implement all 5 card effects (3AM Tweet, Alternative Facts, Nepotism, Rug Pull, Pardon)
- [ ] Implement market price algorithm (volatility model)
- [ ] **Basic Gemini API integration** — single endpoint test, fallback texts working

#### Dev 3 (Network & Sync)
- [ ] Full WebSocket game state synchronization (all phases)
- [ ] Lobby management (create, join, start game)
- [ ] Player session management (reconnect handling)
- [ ] Broadcast market updates via `/topic/market/{sessionId}`
- [ ] Deliver AI feed entries via `/topic/feed/{sessionId}`

#### Dev 4 (Android UI)
- [ ] Functional game screen (hand of cards, asset prices, balance)
- [ ] Card play interaction (select, confirm, animate)
- [ ] AI news ticker (scrolling, sentiment-colored)
- [ ] Lobby screen (create/join/list sessions)

#### Dev 5 (Hardware & Anti-Cheat)
- [ ] Write unit tests for biometric logic (mocked BiometricPrompt)
- [ ] Monitor SonarCloud metrics for entire team
- [ ] Support Dev 2 with audit event backend wiring

#### Dev 6 (Leaderboard & Persistence)
- [ ] Leaderboard service (calculate rankings from DB)
- [ ] REST endpoints for all 3 leaderboard categories
- [ ] Android leaderboard screen (live data from REST API)
- [ ] Database persistence for all game session stats

#### Dev 1 (DevOps)
- [ ] Enhance CI pipeline: add test stage, coverage report
- [ ] Update SonarCloud quality gate thresholds (coverage ≥ 70%)
- [ ] Ensure cloud deployment pipeline works for both repos

### Sprint 2 Definition of Done
- [ ] Full 2-player game round playable end-to-end (all 3 phases)
- [ ] At least 3 card types functional
- [ ] AI feed showing posts (fallback texts at minimum)
- [ ] Leaderboard persisted and viewable in app
- [ ] All new code passes SonarCloud Quality Gate

---

## Sprint 3: Hardware Features, Polish & Finalization

**Duration:** Mid-May → Mid-June 2026
**Review Dates:** 09.06.2026, 11.06.2026, 16.06.2026, 18.06.2026
**Presentation/Handover:** 22.06.2026 – 25.06.2026
**Weight:** High (20 points max for functionality & quality)

### Goal
Complete hardware sensor integration, full cheat/expose mechanic, full AI engine,
and finalize the application for delivery.

### Stories & Tasks

#### Dev 5 (Hardware & Anti-Cheat)
- [ ] Implement two-finger swipe gesture detection for "Taxpayer Bailout"
- [ ] Full BiometricPrompt integration for Congressional Hearing
- [ ] Server-side validation of audit events and penalty application
- [ ] End-to-end test: cheat → accused → hearing → biometric → verdict

#### Dev 2 (Core Game Engine)
- [ ] Complete Gemini prompt engineering (all 4 personas)
- [ ] Implement sentiment → market multiplier algorithm
- [ ] Server-side Congressional Hearing validation logic
- [ ] Asset confiscation / penalty enforcement

#### Dev 3 (Network & Sync)
- [ ] Private WebSocket notifications for audit events (`/user/queue/audit`)
- [ ] Reconnection and session recovery
- [ ] Stress test with 4+ concurrent players

#### Dev 4 (Android UI)
- [ ] Finalize all screens (polish, error states, loading states)
- [ ] Integrate leaderboard in post-game summary screen
- [ ] Hearing notification UI (5-second countdown + biometric dialog)
- [ ] Accessibility pass (content descriptions, contrast ratios)

#### Dev 6 (Leaderboard & Persistence)
- [ ] Real-time leaderboard updates during active session
- [ ] Historical stats per player (profile screen)
- [ ] Final database migration scripts

#### All Developers
- [ ] Resolve all SonarCloud blocker and critical issues
- [ ] Reduce code smells to acceptable level
- [ ] Write missing unit and integration tests
- [ ] Conduct Sprint Review and Retrospective
- [ ] Prepare presentation and demo

### Sprint 3 Definition of Done
- [ ] Cheat mechanic (two-finger swipe) working
- [ ] Biometric authentication (and PIN fallback) working end-to-end
- [ ] All 5 card types fully functional
- [ ] Full Gemini AI integration with all 4 personas
- [ ] All 3 leaderboard categories populated and displayed
- [ ] App installable and runnable on Android device (Min SDK 26)
- [ ] SonarCloud Quality Gate passed
- [ ] Zero blocker/critical code issues

---

## Recurring Ceremonies

| Ceremony | Frequency | Duration | Required |
|----------|-----------|----------|---------|
| Weekly Standup (Daily Scrum) | Weekly | 15 min | All members |
| Sprint Planning | Sprint start | 1-2 hours | All members |
| Sprint Review | See dates above | 1 hour | All members |
| Sprint Retrospective | After each review | 30 min | All members |

> **Attendance:** Mandatory at all official dates. Max 3 hours absence total for "Sehr Gut" grade.
