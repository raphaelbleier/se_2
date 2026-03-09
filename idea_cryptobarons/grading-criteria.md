# Grading Criteria – Crypto Barons

## Target Grade: "Sehr Gut" (S1)

**Required:** > 180 of 200 total points

---

## Point Distribution

### Sprint 1 (Total: 50 Points)

| Category | Max Points | Criteria |
|----------|-----------|---------|
| Functionality & SW Quality | 15 | Working CI pipeline, backend running, Android app connects, SonarCloud active |
| SE Techniques (Testing, GitHub, Sonar) | 15 | Branches used correctly, PRs created, first unit tests present, Sonar reporting |
| Agile (Planning, Standups, SCRUM) | 20 | Sprint Planning conducted, backlog created, Weekly Meeting held, Review prepared |

### Sprint 2 (Total: 70 Points)

| Category | Max Points | Criteria |
|----------|-----------|---------|
| Functionality & SW Quality | 20 | Core game loop working, multiplayer functional, leaderboard persistent, AI feed displaying |
| SE Techniques (Testing, GitHub, Sonar) | 20 | ≥70% test coverage on new code, Quality Gate passing, PRs reviewed and merged properly |
| Agile (Planning, Standups, SCRUM) | 30 | All ceremonies held, backlog updated, velocity tracked, Sprint Review with demo |

### Sprint 3 (Total: 80 Points)

| Category | Max Points | Criteria |
|----------|-----------|---------|
| Functionality & SW Quality | 20 | Hardware features complete, full AI engine, all cards working, app deliverable |
| SE Techniques (Testing, GitHub, Sonar) | 20 | Zero SonarCloud blocker/critical issues, full test suite, code smells resolved |
| Agile (Planning, Standups, SCRUM) | 30 | All ceremonies documented, retrospective outcomes acted upon, Retrospective held |

---

## Individual Requirements (Mandatory for Each Team Member)

Each developer **must** demonstrate full ownership of their assigned feature by providing:

| Deliverable | Evidence |
|-------------|---------|
| **Analysis** | User stories / acceptance criteria in GitHub Issues or project board |
| **Design** | UML diagrams, API contracts, or DB schema contributions (documented) |
| **Implementation** | Code commits attributed to the developer's GitHub account |
| **Testing** | Unit tests and/or integration tests with coverage evidence in SonarCloud |

> **Failure to fulfill any one of these four areas for your feature results in individual grade reduction.**

---

## Attendance Requirements

| Requirement | Threshold |
|-------------|----------|
| Total allowed absences | < 3 hours across all mandatory dates |
| Mandatory dates | All Sprint Reviews, all official university sessions |

### Mandatory Dates (Sommersemester 2026)

| Event | Date(s) |
|-------|---------|
| Sprint 1 Review | 14.04.2026 and 16.04.2026 |
| Sprint 2 Review | 11.05.2026 and 12.05.2026 |
| Sprint 3 Review | 09.06.2026, 11.06.2026, 16.06.2026, 18.06.2026 |
| Final Presentations | 22.06.2026 – 25.06.2026 |

---

## GitHub & SonarCloud Requirements Checklist

### GitHub
- [ ] All development done in feature branches (not directly on `main`)
- [ ] Pull Requests used for all merges into `develop` / `main`
- [ ] PRs reviewed by at least one other team member before merge
- [ ] Issues / GitHub Project Board used for backlog management
- [ ] Commit messages are meaningful and attributable
- [ ] CI pipeline runs and passes on every PR

### SonarCloud
- [ ] Quality Gate active and enforced on PRs
- [ ] Code coverage ≥ 70% for new/changed code
- [ ] Zero blocker issues in production code
- [ ] Zero critical issues in production code
- [ ] Code smells resolved before each Sprint Review
- [ ] Technical debt ratio acceptable (< team-agreed threshold)

### Testing
- [ ] Unit tests for all business logic (backend services)
- [ ] Unit tests for ViewModel logic (Android)
- [ ] Integration tests for REST endpoints (Spring Boot Test)
- [ ] At least one UI test per major screen (Espresso or Compose Test)
- [ ] Tests run automatically in GitHub Actions CI

---

## Agile Process Checklist

### Required Documentation / Artifacts
- [ ] Product backlog with user stories (GitHub Issues or Jira equivalent)
- [ ] Sprint backlog for each sprint (subset of product backlog)
- [ ] Sprint Retrospective notes (documented after each sprint)
- [ ] Sprint Review presentation / demo recorded or documented
- [ ] Weekly standup attendance tracked

### SCRUM Meeting Evidence
The team must document and be able to present:
- Sprint Planning meeting notes + backlog items assigned
- At least 4 Weekly standup meeting records (format: What did I do? What will I do? Blockers?)
- Sprint Review: demo of working software + stakeholder feedback
- Sprint Retrospective: What went well? What to improve? Action items?

---

## Definition of "Done" — Project Level

The final deliverable (end of Sprint 3) must include:

- [ ] Installable Android APK (Min SDK 26, tested on real device or emulator)
- [ ] Spring Boot backend deployed and reachable at stable URL
- [ ] PostgreSQL database with production data persisted
- [ ] All planned features implemented and demonstrable
- [ ] Source code on GitHub with full commit history
- [ ] SonarCloud analysis showing passing Quality Gate
- [ ] Test suite with documented coverage
- [ ] README with setup instructions for both frontend and backend
