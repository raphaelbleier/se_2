# Tech Stack Guidelines – Crypto Barons

> **BINDING:** These guidelines are mandatory. Deviations require explicit team consensus and
> documentation. Any implementation that contradicts these guidelines will not be accepted.

---

## Frontend A — Android App (Game Client)

| Property | Specification |
|----------|---------------|
| **Platform** | Native Android App |
| **Language** | Kotlin (exclusively) |
| **Minimum SDK** | API Level 26 (Android 8.0 Oreo) |
| **HTTP Client** | Retrofit (REST API calls) |
| **WebSocket Client** | OkHttp (real-time communication) |
| **UI Framework** | Android Views / XML Layouts (or Jetpack Compose — team decision, document choice) |
| **Architecture Pattern** | MVVM recommended (ViewModel + LiveData / StateFlow) |

### Android Frontend Constraints
- No cross-platform frameworks (no Flutter, React Native, Xamarin)
- No web views as primary UI
- Biometric authentication via `BiometricPrompt` API (AndroidX Biometric)
- Gesture detection for cheat mechanic via `GestureDetector` / `MotionEvent`

---

## Frontend B — Web Leaderboard Interface

| Property | Specification |
|----------|---------------|
| **Type** | Static web page (no server-side rendering required) |
| **Language** | HTML5, CSS3, Vanilla JavaScript (ES2020+) |
| **Data Access** | `fetch()` API calling the existing REST leaderboard endpoints |
| **Hosting** | Served as static resources from Spring Boot (`/src/main/resources/static/`) **or** deployed separately via GitHub Pages |
| **Auto-Refresh** | Poll `GET /api/v1/leaderboard/{category}` every 30 seconds |
| **Authentication** | Public endpoints only — no login required to view leaderboard |

### Web Frontend Constraints
- No heavy SPA framework required — vanilla JS is sufficient and preferred for simplicity
- Must be responsive (readable on desktop and tablet)
- Must display all 3 leaderboard categories: "Biggest Rug Pull", "Most Pardons Used", "Top 1% Elite"
- No separate build pipeline needed; if hosted via Spring Boot static folder, it deploys with the backend automatically
- CORS must be configured on the backend to allow browser requests (add `@CrossOrigin` or global `CorsConfiguration` in Spring Security)

---

## Backend

| Property | Specification |
|----------|---------------|
| **Framework** | Spring Boot (latest stable) |
| **Language** | Kotlin **or** Java (team decision — document and commit to one) |
| **REST API** | Spring MVC (`@RestController`) |
| **WebSocket** | Spring WebSocket (`@MessageMapping`, STOMP over SockJS) |
| **Security** | Spring Security (JWT-based authentication) |
| **ORM** | Spring Data JPA / Hibernate |
| **Build Tool** | Gradle (Kotlin DSL preferred) or Maven |

### Backend Constraints
- All Gemini API calls are made **server-side only** — API keys must never be exposed to the client
- Game state machine must live entirely on the server
- Business logic must not be duplicated in the Android client

---

## Database

| Property | Specification |
|----------|---------------|
| **Engine** | PostgreSQL |
| **Hosting** | Cloud DBaaS (Supabase, Render DB, or equivalent) |
| **Access** | Via Spring Data JPA repositories |
| **Schema Management** | Flyway or Liquibase for migrations |

### Database Constraints
- No SQLite or in-memory databases in production
- All player profiles, game stats, and leaderboard data must be persisted
- Sensitive data (passwords) must be hashed (bcrypt via Spring Security)

---

## AI Integration

| Property | Specification |
|----------|---------------|
| **Provider** | Google Gemini API |
| **Integration Point** | Spring Boot Backend (server-side only) |
| **Communication** | Asynchronous API calls from backend service layer |
| **Client Delivery** | Generated content pushed via WebSocket to Android clients |
| **Fallback** | Static pre-defined text arrays when API is unavailable |

### AI Constraints
- API keys stored as environment variables / secrets — never in source code
- Prompt engineering logic encapsulated in a dedicated service class
- LLM responses must be sanitized before forwarding to clients
- Rate limiting and error handling required for all Gemini API calls

---

## Infrastructure & Quality Assurance

| Tool | Purpose | Platform |
|------|---------|----------|
| **GitHub** | Version control, collaboration | github.com |
| **GitHub Actions** | CI/CD pipeline automation | GitHub |
| **SonarCloud** | Static code analysis, quality metrics | sonarcloud.io |

### CI/CD Pipeline Requirements (GitHub Actions)
- Triggered on every Pull Request to `main`/`develop`
- Pipeline stages:
  1. Build (compile Kotlin/Java)
  2. Unit Tests (JUnit 5 for backend, JUnit / Mockito for Android)
  3. SonarCloud analysis
  4. (Optional Sprint 3) Build signed APK

### SonarCloud Requirements
- Quality Gate must pass before PR merge
- Coverage threshold: minimum 70% for new code
- Zero critical or blocker issues in production code
- Code smells must be addressed before each Sprint Review

---

## Hosting Strategy

| Component | Technology | Hosting Target |
|-----------|------------|----------------|
| Android App (Game Client) | Kotlin Native | Device / Emulator (APK distribution) |
| Web Leaderboard Interface | HTML5 / CSS3 / Vanilla JS | Spring Boot static resources **or** GitHub Pages |
| Spring Boot Backend | Kotlin or Java | Render, Heroku, or equivalent PaaS |
| PostgreSQL Database | PostgreSQL | Supabase, Render DB, or equivalent DBaaS |
| Gemini API | Google API | Consumed from backend (no direct hosting) |
| CI/CD | GitHub Actions | GitHub |
| Code Quality | SonarCloud | sonarcloud.io |

---

## Dependency Summary (Non-Exhaustive)

### Android (`build.gradle.kts`)
```
implementation("com.squareup.retrofit2:retrofit:2.x.x")
implementation("com.squareup.okhttp3:okhttp:4.x.x")
implementation("androidx.biometric:biometric:1.x.x")
implementation("com.google.code.gson:gson:2.x.x")  // or Moshi
```

### Backend (`build.gradle.kts`)
```
implementation("org.springframework.boot:spring-boot-starter-web")
implementation("org.springframework.boot:spring-boot-starter-websocket")
implementation("org.springframework.boot:spring-boot-starter-data-jpa")
implementation("org.springframework.boot:spring-boot-starter-security")
implementation("org.postgresql:postgresql")
implementation("io.jsonwebtoken:jjwt-api:0.12.x")
```
