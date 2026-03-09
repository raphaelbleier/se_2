# Crypto Barons

> *A satirical multiplayer mobile game about market manipulation, political corruption, and the
> fine art of lying to your friends — with real consequences.*

---

We are building a **native Android multiplayer card game** where the goal is to become a
billionaire by any means necessary: pump meme coins with fake social media posts, bribe
government officials, rug pull your friends' investments, and when you inevitably get caught —
just pardon yourself.

It is a love letter to everything wrong with modern finance. And it is going to be genuinely
fun to play.

---

## The Pitch

Think of it as a cross between **Monopoly**, **Among Us**, and **Wall Street Bets** — played
on your phones, in real time, with an AI feeding you satirical breaking news the whole time.

You start with a pile of **Freedom Dollars**. The first player to hit **one billion FD** wins.
The market moves every round based on global events, AI-generated social media posts, and most
importantly: **whatever nonsense your fellow players are posting in the in-game feed**.

Yes, you can lie. In fact, you are expected to.

---

## How a Round Actually Feels

**Round starts.** The server fires a breaking news event:

> *"CENTRAL BANK DISSOLVED BY EXECUTIVE ORDER. ALL ASSETS UNREGULATED."*

The AI generates a panicked post from "@RealDonalDuck45":

> *"SAD! THE FAILING MAINSTREAM MEDIA IS CRASHING PATRIOTCOIN! WE WILL BUILD A FIREWALL
> AROUND THE BLOCKCHAIN AND MAKE THEM PAY FOR IT! HODL!"*

PatriotCoin spikes 40%.

**Now it's your turn.** You already bought PatriotCoin last round. Smart. You open the
FreedomFeed and post:

> *"Just heard from a very reliable source that PatriotCoin is about to be adopted as
> official government reserve currency. Loading up. Not financial advice."*

The AI reads your post. PatriotCoin climbs another 25%. Everyone else starts panic-buying.

You sell everything at the peak.

**Next round**, you play "Alternative Facts" — a card that converts last round's losses
(which you don't have, but your enemies do) into fictional gains. Then you quietly do a
two-finger swipe on your screen and steal 30 million from the State Treasury while nobody
is watching.

Someone notices your balance jumped. They accuse you of cheating. The game pulls up a
5-second biometric countdown on your phone. You press your fingerprint.

**Innocent.** The accuser pays you 20% of their wealth as "legal fees."

You're not even the richest player yet. This is round four.

---

## The FreedomFeed — Your Most Dangerous Weapon

Every player gets one free post per round to the shared social feed. Posts are analyzed by
Gemini AI in real time. The sentiment moves the market. The other players have no idea if
you are telling the truth.

Strategies that actually work:

- **The Pump** — Post bullishly about an asset you already own. Watch it climb. Sell quietly.
- **The FUD** — Post scary rumors about an asset you want to buy cheap. Buy the dip you created.
- **The Misdirect** — Hype LibertyDoge loudly while everyone ignores PatriotCoin, which you
  are quietly accumulating.
- **The Coordinated Pump** — Get two teammates posting bullishly about the same asset in the
  same round. Multipliers stack. Chaos ensues.

The AI doesn't just analyze your posts. It *reacts* to them. Post something bullish and a
Crypto Bro persona might amplify it with rocket emojis. Post FUD and the Irrational Politician
fires back, which accidentally makes the FUD look credible to everyone else.

The feed is half game mechanic, half psychological warfare.

---

## The Cards

Five core cards (plus two social-manipulation cards), each more absurd than the last:

| Card | What It Does |
|------|-------------|
| **3AM Capslock Tweet** | Pumps a worthless crypto 5× for one round. Pre-buyers profit massively. Then it crashes to 10% of original value. |
| **Alternative Facts** | Retroactively converts your last round's losses into gains. Math is a social construct. |
| **Nepotism Board Member** | Appoint a family member to a government oversight role. Earns 50M FD passive income per round. Grants regulatory immunity. |
| **The Ultimate Rug Pull** | Issue your own token, force everyone to invest in it, then drain the liquidity pool. Token goes to zero. You walk away with everything. |
| **Presidential Pardon** | Someone proved you cheated? Not anymore. Case dismissed. |
| **Suppress the Post** | Remove an opponent's FreedomFeed post before it can move the market. *"Violates Community Standards."* |
| **Echo Chamber** | Triple the market impact of any post. Make your pump go viral. Or make their FUD catastrophic. |

---

## The Cheat Mechanic (This Is The Best Part)

At any point in the game — during your turn, during someone else's turn, doesn't matter —
you can perform a **two-finger swipe** on your screen.

If nobody sees you, the server quietly transfers up to 50 million FD from the State Treasury
into your account.

If someone notices your balance jumped and accuses you, a **Congressional Hearing** is called.
Your phone displays a 5-second biometric countdown. You either authenticate with your
fingerprint (or PIN if no sensor), or you lose 50% of everything you own.

If you authenticate successfully, the accuser was guilty of "Fake News" and pays you 20% of
their balance as legal fees.

The entire mechanic is social. Someone has to actually notice. Someone has to be willing to
spend 10 million FD on an accusation. The accused person has to not panic.

It is a game within the game.

---

## The Tech Stack

We are building this properly. Android native in Kotlin. Spring Boot backend. PostgreSQL.
Gemini AI server-side. WebSockets for real-time everything. CI/CD from day one.

| What | How |
|------|-----|
| Android app | Kotlin, Min SDK 26, Retrofit + OkHttp |
| Backend | Spring Boot (Kotlin or Java), REST + WebSocket |
| Database | PostgreSQL on cloud DBaaS |
| AI | Gemini API — server-side only, never in the APK |
| Infrastructure | GitHub Actions CI, SonarCloud quality gates |
| Leaderboard Web UI | Vanilla HTML/JS, served publicly |

Every piece of game logic lives on the server. The Android app is a display and input layer.
The backend is the source of truth. The AI never touches the client directly.

This is not a toy project. This is a well-architected networked mobile game with a real AI
integration and hardware sensor features. It also happens to be extremely funny.

---

## What We Are Each Building

| Developer | Owns | Core challenge |
|-----------|------|---------------|
| Dev 1 | CI/CD, deployment, server setup | Get the whole thing running in the cloud from day one |
| Dev 2 | Game engine, cards, AI integration | State machine + Gemini API + market algorithm |
| Dev 3 | WebSocket multiplayer | Real-time sync across 2–6 phones simultaneously |
| Dev 4 | Android UI, FreedomFeed | Make it feel like a real social media app inside a game |
| Dev 5 | Cheat gesture + biometric auth | Two-finger swipe detection + fingerprint API |
| Dev 6 | Leaderboard backend + database | Rankings, stats, persistence |
| Dev 7 | Web leaderboard interface | Public browser leaderboard page |

Each person fully owns their feature: analysis, design, implementation, tests. This satisfies
the grading requirement and means nobody is a passive passenger.

---

## The Timeline

```
March → April         April → May            May → June
─────────────────     ────────────────────   ──────────────────────────
Sprint 1              Sprint 2               Sprint 3
Infrastructure        Core gameplay          Hardware + social features
Review: 14./16.04     Review: 11./12.05      Reviews: 09./11./16./18.06
                                             Presentations: 22.–25.06
```

Sprint 1 is about getting the skeleton working. Sprint 2 is the game actually running end to
end. Sprint 3 is where the fingerprint scanner, the cheat gesture, and the full AI social feed
all come together.

---

## The Documentation

Everything is fully specced out. No ambiguity about what we are building.

| File | Contains |
|------|---------|
| `game-overview.md` | Full concept, currencies, win condition |
| `game-mechanics.md` | Turn phases, FreedomFeed rules, cheat/expose system |
| `cards.md` | All 7 cards with exact effects and interactions |
| `ai-engine.md` | Gemini integration, all 4 AI personas, prompts, sentiment model |
| `api-spec.md` | Every REST endpoint and WebSocket message format |
| `architecture.md` | System diagram, component responsibilities, security model |
| `techstack.md` | Binding tech stack guidelines |
| `sprint-plan.md` | Sprint-by-sprint task breakdown per developer |
| `team-assignments.md` | Who owns what, RACI matrix |
| `grading-criteria.md` | Exactly what we need to do to get the top grade |

---

## Why This Is Going To Be Fun To Build

Most university projects are a CRUD app with a login screen. This is a real-time multiplayer
game with an AI feed, biometric hardware integration, a cheat mechanic that runs outside of
turn order, and a market system driven by both algorithmic events and player psychology.

Every feature is technically interesting. The WebSocket multiplayer is a real engineering
challenge. The Gemini sentiment analysis is genuinely novel for a student project. The
biometric auth mechanic is hardware integration that most apps never touch. And the
FreedomFeed is a real game mechanic — not just a chat window — because every message you
send changes the market.

We also get to make jokes about financial deregulation the whole time, which is a bonus.

---

*Let's build something worth presenting.*
