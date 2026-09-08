# StepUp — API Contracts

## 1. API Overview

StepUp exposes a REST API implemented with:

- Node.js
- TypeScript
- Express
- Prisma ORM
- PostgreSQL

Routes are mounted under:

```text
/api/v1
```

The API is designed so that a future student mobile client can consume the same backend contract.

## 2. Authentication

### Public operations

The current implementation allows registration, login and the public sports list without an authenticated bearer token.

### Protected operations

Most other routes require:

```http
Authorization: Bearer <JWT_ACCESS_TOKEN>
```

Authentication middleware validates the token and attaches the decoded user identity and role to the request.

### Admin operations

Admin-only operations additionally pass through:

```text
requireAdmin()
```

A student token attempting an admin-only route is rejected with `403 FORBIDDEN`.

## 3. Feature Route Groups

The backend groups routes by feature:

| Route group | Main responsibility |
|---|---|
| `auth` | Registration, login and token refresh |
| `users` | User/profile operations |
| `activity` | Activity synchronisation |
| `integrity` | Integrity-classification records and results |
| `sports` | Sports and sport-profile information |
| `matches` | Sport-buddy matching |
| `sessions` | Sport-session management |
| `challenges` | Challenge creation, joining and progress |
| `battles` | Step-battle lifecycle |
| `wallet` | StepCoin balances and transactions |
| `leaderboards` | Leaderboard information |
| `notifications` | In-app notification records |
| `admin` | Administrative statistics and management |
| `evaluation` | Detector evaluation metrics |
| `privacy` | Consent and deletion workflows |

## 4. Activity Synchronisation

### POST `/activity/sync`

Receives activity information already populated by the caller.

Important activity information includes:

- `syncId`
- activity type
- step count
- distance
- calories
- source
- timing information

The `syncId` is used to prevent the same activity submission from being inserted twice.

### POST `/activity/integrity`

Associates an integrity classification with an activity.

The stored result contains:

- classification
- confidence score
- verified steps
- suspicious steps

Genuine walking and genuine running set the corresponding activity's `isVerified` value to `true`.

## 5. Challenge Contract

Challenge functionality supports:

- administrator-created challenges
- target step counts
- start and end dates
- challenge status
- student participation
- progress tracking

## 6. Step Battle Contract

A battle contains:

- creator
- opponent
- target steps
- stake
- status

Rules implemented by the backend include:

- target steps: `100` to `100,000`
- minimum stake: `10 StepCoins`
- creator must have enough balance
- accepting a battle debits both stakes transactionally
- winner receives double the stake when the battle is completed

## 7. Sport-Buddy Matching

Matching first filters candidates using:

- skill compatibility
- availability overlap

Candidates are then ranked using:

```text
score = 0.35 * skillFit
      + 0.35 * reliability
      + 0.30 * prefFit
```

The top ten candidates are returned.

## 8. Privacy

The API supports:

- consent records
- granting/revoking consent types
- data-deletion requests
- administrative resolution of deletion requests

## 9. Error and Security Expectations

The backend uses request validation and structured error handling. Security middleware includes:

- Helmet
- CORS
- rate limiting
- JWT authentication
- role-based authorization

Passwords are hashed with `bcryptjs` and are not returned in user-fetching results.
