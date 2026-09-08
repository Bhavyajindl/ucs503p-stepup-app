# StepUp — System Overview

## 1. Project Description

**StepUp** is a research-oriented campus step-verification and gamified fitness platform intended for a campus pilot at Thapar Institute of Engineering and Technology.

The implemented artefact consists of:

1. a backend REST API
2. a PostgreSQL database
3. a React administrative analytics dashboard

The current build does not include a student-facing mobile/web interface.

## 2. Problem

Campus step challenges can be affected by:

- manipulated activity counts
- phone shaking
- vehicle movement
- duplicate submissions

Administrators also need visibility into activity integrity, challenges, battles, wallets, matching, privacy and evaluation data.

StepUp addresses these needs through an auditable relational backend and an administrator dashboard.

## 3. Architecture

StepUp uses a three-tier architecture:

```text
+--------------------------------------+
| Presentation Layer                   |
| React + Vite + Tailwind CSS          |
| Administrative Dashboard             |
+------------------+-------------------+
                   |
                   | REST / JSON
                   v
+--------------------------------------+
| Application Layer                   |
| Node.js + Express + TypeScript      |
| Routes + Middleware + Services      |
+------------------+-------------------+
                   |
                   | Prisma ORM
                   v
+--------------------------------------+
| Data Layer                           |
| PostgreSQL                           |
| 24 relational tables                 |
+--------------------------------------+
```

## 4. Technology Stack

### Backend

- Node.js
- TypeScript
- Express 4
- Prisma ORM 5
- PostgreSQL

### Frontend

- React 18
- Vite
- Tailwind CSS
- React Router v6
- Recharts

### Authentication and Security

- JWT access/refresh tokens
- bcryptjs password hashing
- Helmet
- CORS
- rate limiting
- request validation

### Development / Tooling

- ts-node-dev
- Jest
- Docker Compose

## 5. Core Business Modules

### Authentication

`AuthService` handles registration, login, token refresh and password verification.

### Activity

`ActivityService` handles activity synchronisation, duplicate checking, integrity storage and verification updates.

### Challenges

`ChallengeService` manages challenge creation, updates, joining and progress.

### Battles

`BattleService` manages battle creation, acceptance, completion and rewards.

### Matching

`MatchingService` calculates and ranks sport-buddy candidates.

## 6. Database

The implemented PostgreSQL schema contains **24 relational tables**.

Major data areas include:

- User
- StudentProfile
- ActivityRecord
- IntegrityResult
- Sport
- SportProfile
- SportMatch
- SportSession
- SessionParticipant
- Challenge
- ChallengeParticipant
- StepBattle
- Wallet
- WalletTransaction
- Badge
- UserBadge
- Notification
- Leaderboard
- LeaderboardEntry
- Consent
- DataDeletionRequest
- EvaluationMetric
- Pilot
- AuditLog

## 7. Gamification

StepUp includes a StepCoin virtual economy.

Wallet movements are recorded through immutable `WalletTransaction` rows.

Step battles use a stake-and-reward model:

```text
Both players stake coins
        |
        v
Battle becomes ACTIVE
        |
        v
Winner determined
        |
        v
Winner receives double stake
```

## 8. Sport Matching

The matching algorithm combines:

```text
35% skill compatibility
35% reliability
30% preference fit
```

Preference fit considers factors such as location matching and group-size closeness.

Candidates are filtered first and then ranked, with the top ten returned.

## 9. Administrative Dashboard

The dashboard provides administrative pages for:

- Overview
- Integrity
- Students
- Challenges
- Step Battles
- Sport Buddy
- Sessions
- Wallet
- Leaderboard
- Evaluation
- Pilot
- Privacy

Charts are rendered with Recharts, while student and audit information is presented through searchable/paginated tables.

## 10. Current Scope

### Implemented

- relational database
- secured REST API
- role-based access
- activity synchronisation
- duplicate protection
- integrity-result storage
- challenge management
- step battles
- StepCoin wallet transactions
- sport-buddy matching
- sessions
- notifications as database records
- leaderboard data
- consent/deletion workflow
- evaluation metrics
- audit logging
- administrative dashboard

### Future Work

- real activity-source integration
- server-side integrity scoring / second-opinion detector
- outbound email/push notifications
- student-facing client
- automatic XP, level and streak updates
- live pilot data collection and genuine detector evaluation
