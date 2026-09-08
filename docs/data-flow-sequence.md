# StepUp — Data Flow and Sequence

## 1. Overall Request Sequence

```text
Student / Admin Client
        |
        v
React Dashboard / Future Mobile Client
        |
        | HTTP REST request
        v
Express REST API
        |
        +--> Authentication Middleware
        |
        +--> Admin Role Guard (where required)
        |
        v
Route / Controller
        |
        +--> Service Layer
        |      |
        |      +--> AuthService
        |      +--> ActivityService
        |      +--> ChallengeService
        |      +--> BattleService
        |      +--> MatchingService
        |
        v
Prisma ORM
        |
        v
PostgreSQL
        |
        v
JSON Response
```

## 2. Activity Synchronisation Sequence

```text
Client
  |
  | POST /activity/sync
  v
Activity Route
  |
  v
ActivityService.sync()
  |
  v
Validate request
  |
  v
Check syncId
  |
  +---- Existing? ---- Yes ---> Return existing record as duplicate
  |
  No
  |
  v
Create ActivityRecord
  |
  v
Return activity response
```

## 3. Integrity Classification Sequence

```text
Client
  |
  | POST /activity/integrity
  | classification + confidence
  v
Integrity Route
  |
  v
Store IntegrityResult
  |
  v
Determine verification state
  |
  +--> GENUINE WALKING
  |
  +--> GENUINE RUNNING
  |        |
  |        v
  |   isVerified = true
  |
  +--> PHONE SHAKING / VEHICLE MOVEMENT /
  |    DUPLICATE / UNKNOWN
  |        |
  |        v
  |   isVerified = false
  |
  v
Return status / result
```

## 4. Step Battle Sequence

```text
Creator
  |
  | Create battle
  v
PENDING battle
  |
  | Opponent accepts
  v
Check both wallets
  |
  v
Prisma transaction
  |
  +--> Debit creator stake
  +--> Debit opponent stake
  +--> Create two BATTLE STAKE ledger entries
  +--> Set battle ACTIVE
  |
  v
Battle progresses
  |
  v
Battle completion
  |
  v
Determine winner
  |
  v
Prisma transaction
  |
  +--> Credit winner with double stake
  +--> Create BATTLE REWARD ledger entry
  +--> Create in-app notifications
  |
  v
Completed battle
```

## 5. Sport-Buddy Matching Sequence

```text
Student
  |
  | Find matches
  v
MatchingService
  |
  v
Filter candidates
  |
  +--> Compatible skill
  +--> Availability overlap
  |
  v
Calculate score
  |
  v
Rank candidates
  |
  v
Return top 10 matches
```

## 6. Admin Dashboard Sequence

```text
Administrator
      |
      v
React Dashboard
      |
      | authenticated API requests
      v
Express API
      |
      v
Admin endpoints
      |
      v
Prisma
      |
      v
PostgreSQL
      |
      v
Statistics / records
      |
      v
Dashboard charts and tables
```

The current dashboard contains twelve planned page areas, including Overview, Integrity, Students, Challenges, Step Battles, Sport Buddy, Sessions, Wallet, Leaderboard, Evaluation, Pilot and Privacy.
