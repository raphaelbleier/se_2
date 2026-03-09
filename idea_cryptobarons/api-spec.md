# API Specification – Crypto Barons

## Base URL
```
https://<backend-host>/api/v1
```

All endpoints require `Authorization: Bearer <JWT_TOKEN>` unless marked as public.

---

## Authentication Endpoints

### POST `/auth/register`
Register a new player account.

**Request:**
```json
{
  "username": "PatriotBaron",
  "email": "player@example.com",
  "password": "securePassword123"
}
```

**Response `201 Created`:**
```json
{
  "id": "uuid",
  "username": "PatriotBaron",
  "token": "eyJhbGci..."
}
```

---

### POST `/auth/login`
Authenticate and receive JWT token.

**Request:**
```json
{
  "username": "PatriotBaron",
  "password": "securePassword123"
}
```

**Response `200 OK`:**
```json
{
  "token": "eyJhbGci...",
  "expiresAt": "2026-05-15T15:00:00Z"
}
```

---

## Lobby / Session Endpoints

### POST `/sessions`
Create a new game session (lobby).

**Request:**
```json
{
  "maxPlayers": 4,
  "sessionName": "WallStreetBets2026"
}
```

**Response `201 Created`:**
```json
{
  "sessionId": "abc123",
  "joinCode": "BARON7",
  "status": "WAITING_FOR_PLAYERS",
  "createdBy": "PatriotBaron"
}
```

---

### POST `/sessions/{sessionId}/join`
Join an existing session by ID or join code.

**Request:**
```json
{
  "joinCode": "BARON7"
}
```

**Response `200 OK`:**
```json
{
  "sessionId": "abc123",
  "playersJoined": 2,
  "maxPlayers": 4
}
```

---

### POST `/sessions/{sessionId}/start`
Start the game (only session creator).

**Response `200 OK`:**
```json
{
  "sessionId": "abc123",
  "status": "NEWS_CYCLE",
  "round": 1
}
```

---

## Game Action Endpoints

### POST `/sessions/{sessionId}/actions/play-card`
Play a card during the Lobbying Phase.

**Request:**
```json
{
  "cardId": "3AM_TWEET",
  "targetAsset": "PATRIOTCOIN",
  "targetPlayerId": null
}
```

**Response `200 OK`:**
```json
{
  "actionId": "uuid",
  "effect": "PRICE_MULTIPLIED",
  "affectedAsset": "PATRIOTCOIN",
  "newPrice": 5000,
  "aiCommentaryTriggered": true
}
```

---

### POST `/sessions/{sessionId}/actions/buy-asset`
Buy a crypto asset during the Lobbying Phase.

**Request:**
```json
{
  "asset": "LIBERTYDOGE",
  "quantity": 1000,
  "pricePerUnit": 500
}
```

**Response `200 OK`:**
```json
{
  "transactionId": "uuid",
  "asset": "LIBERTYDOGE",
  "quantity": 1000,
  "totalCost": 500000,
  "newBalance": 49500000
}
```

---

### POST `/sessions/{sessionId}/actions/sell-asset`
Sell a crypto asset during the Lobbying Phase.

**Request:**
```json
{
  "asset": "PATRIOTCOIN",
  "quantity": 500
}
```

**Response `200 OK`:**
```json
{
  "transactionId": "uuid",
  "asset": "PATRIOTCOIN",
  "quantity": 500,
  "totalReceived": 2500000,
  "newBalance": 52000000
}
```

---

### POST `/sessions/{sessionId}/actions/cheat`
(Internal — triggered server-side by validated gesture event from client)
Executes a "Taxpayer Bailout" transfer.

**Request:**
```json
{
  "gestureHash": "sha256_of_gesture_data",
  "deviceId": "device_fingerprint"
}
```

**Response `200 OK`:**
```json
{
  "transactionId": "uuid",
  "type": "BAILOUT",
  "amount": 25000000,
  "newBalance": 75000000
}
```

---

## Audit Endpoints

### POST `/sessions/{sessionId}/audit/initiate`
Initiate a Congressional Hearing against a player.

**Request:**
```json
{
  "accusedPlayerId": "player_uuid"
}
```

**Response `200 OK`:**
```json
{
  "hearingId": "uuid",
  "accusedPlayer": "CorruptBaron",
  "timeoutSeconds": 5,
  "status": "AWAITING_AUTHENTICATION"
}
```

---

### POST `/sessions/{sessionId}/audit/{hearingId}/respond`
Submit biometric/PIN authentication result.

**Request:**
```json
{
  "authenticated": true,
  "method": "BIOMETRIC",
  "biometricToken": "android_biometric_result_token"
}
```

**Response `200 OK`:**
```json
{
  "hearingId": "uuid",
  "verdict": "INNOCENT",
  "consequence": "FAKE_NEWS_PENALTY_APPLIED_TO_ACCUSER",
  "accuserPenalty": 20000000
}
```

---

## Leaderboard Endpoints

### GET `/leaderboard/biggest-rug-pull`
**Public endpoint (no auth required)**

**Response `200 OK`:**
```json
{
  "category": "BIGGEST_RUG_PULL",
  "entries": [
    {
      "rank": 1,
      "username": "RugMaster9000",
      "value": 750000000,
      "date": "2026-05-12"
    }
  ]
}
```

---

### GET `/leaderboard/most-pardons`
**Response `200 OK`:**
```json
{
  "category": "MOST_PARDONS_USED",
  "entries": [
    {
      "rank": 1,
      "username": "UntouchableDon",
      "count": 7,
      "gamesPlayed": 3
    }
  ]
}
```

---

### GET `/leaderboard/top-1-percent`
**Response `200 OK`:**
```json
{
  "category": "TOP_1_PERCENT_ELITE",
  "entries": [
    {
      "rank": 1,
      "username": "SpeedrunBaron",
      "roundsToWin": 12,
      "date": "2026-06-01"
    }
  ]
}
```

---

## WebSocket Protocol (STOMP over OkHttp)

**Connection Endpoint:**
```
wss://<backend-host>/ws
```

**Subscribe Topics:**

| Topic | Description | Payload Type |
|-------|-------------|-------------|
| `/topic/game/{sessionId}` | Full game state updates | `GameStateMessage` |
| `/topic/feed/{sessionId}` | AI social feed entries | `FeedEntryMessage` |
| `/topic/market/{sessionId}` | Asset price updates | `MarketUpdateMessage` |
| `/user/queue/audit` | Private audit notifications | `AuditNotificationMessage` |
| `/user/queue/errors` | Private error messages | `ErrorMessage` |

**Send Destinations (Client → Server):**

| Destination | Description |
|-------------|-------------|
| `/app/game/{sessionId}/action` | Send game action |
| `/app/game/{sessionId}/ready` | Signal player ready |

---

### GameStateMessage Schema
```json
{
  "type": "GAME_STATE",
  "sessionId": "abc123",
  "round": 3,
  "phase": "LOBBYING",
  "currentPlayerId": "player_uuid",
  "players": [
    {
      "id": "player_uuid",
      "username": "PatriotBaron",
      "balance": 150000000,
      "assets": { "PATRIOTCOIN": 1000, "LIBERTYDOGE": 500 },
      "cardCount": 3,
      "hasNepotismBoard": false
    }
  ],
  "marketPrices": {
    "PATRIOTCOIN": 1250,
    "LIBERTYDOGE": 380
  },
  "stateTreasury": 5000000000
}
```

---

### AuditNotificationMessage Schema
```json
{
  "type": "AUDIT_NOTIFICATION",
  "hearingId": "uuid",
  "accuserUsername": "WhistleBlower",
  "accusedUsername": "CorruptBaron",
  "timeoutSeconds": 5,
  "action": "AUTHENTICATE_NOW"
}
```
