# StepUp — Functional and Non-Functional Requirements

## 1. Functional Requirements

### FR-01 — User Registration

The system shall allow a new user to register and create the required student profile and wallet.

A newly registered user receives a wallet with **100 welcome StepCoins**.

### FR-02 — Authentication

The system shall support:

- login
- short-lived access tokens
- refresh tokens
- password verification
- disabled-account rejection

### FR-03 — Role-Based Authorization

The system shall distinguish between student and administrator access.

Admin-only operations shall reject unauthorized student requests.

### FR-04 — Activity Synchronisation

The system shall accept activity records through the REST API and store activity information such as steps, distance, calories, source and timing.

### FR-05 — Duplicate-Safe Activity Storage

The system shall use a client-supplied `syncId` to prevent duplicate activity rows caused by repeated submissions.

### FR-06 — Integrity Classification

The system shall store an integrity result associated with an activity.

Supported classifications are:

- Genuine walking
- Genuine running
- Phone shaking
- Vehicle movement
- Duplicate
- Unknown

The result also stores confidence and verified/suspicious step information.

### FR-07 — Activity Verification

Genuine walking and genuine running shall set the associated activity's `isVerified` value to `true`.

Other classifications shall leave it false.

### FR-08 — Challenge Management

Administrators shall be able to create and update challenges.

The system shall support challenge participation and progress tracking.

### FR-09 — Step Battles

The system shall support:

- battle creation
- battle acceptance
- battle completion
- winner determination
- stake handling
- reward handling

### FR-10 — StepCoin Wallet

The system shall maintain user wallet balances and an immutable transaction history.

Wallet transactions shall record activity rewards, challenge completion, battle stakes and battle payouts where applicable.

### FR-11 — Sport-Buddy Matching

The system shall find sport buddies using skill compatibility, availability, reliability and preference fit.

### FR-12 — Sport Sessions

The system shall support organised sport sessions and participant records.

### FR-13 — Notifications

The system shall create and retrieve in-app notification records.

### FR-14 — Leaderboards

The system shall store leaderboard and leaderboard-entry information.

### FR-15 — Privacy

The system shall support:

- consent records
- consent granting/revocation
- data-deletion requests
- administrative resolution of deletion requests

### FR-16 — Evaluation

The system shall store detector evaluation metrics such as precision, recall, F1, false-positive rate and latency.

### FR-17 — Audit Logging

Administrative actions shall be recorded in an audit log.

## 2. Non-Functional Requirements

### NFR-01 — Security

The system shall use:

- JWT authentication
- role-based authorization
- bcryptjs password hashing
- Helmet
- CORS controls
- request validation
- rate limiting

### NFR-02 — Data Integrity

Wallet stake and reward operations shall be executed transactionally so that partial updates do not leave wallet data inconsistent.

### NFR-03 — Reliability

Repeated activity submissions using the same `syncId` shall not create duplicate activity rows.

### NFR-04 — Maintainability

The backend follows a thin:

```text
Route → Service → Prisma
```

architecture.

Non-trivial business logic is separated into dedicated services.

### NFR-05 — Type Safety

The backend uses TypeScript and Prisma's type-safe database access.

### NFR-06 — Auditability

Important administrative actions and wallet movements shall have persistent records.

### NFR-07 — Usability

The React dashboard shall provide administrative visibility through charts, searchable tables and feature-specific pages.

### NFR-08 — Scalability

The stateless REST API and relational database design allow a future student-facing client to consume the same API contract.

### NFR-09 — Observability

Requests are wrapped with structured logging using Morgan and Winston.

### NFR-10 — Deployment Support

Docker Compose is used for local orchestration of PostgreSQL, backend and frontend components.

## 3. Current Scope Limitations

The present implementation does **not** include:

- a live Google Fit activity import
- an outbound email/push notification service
- a student-facing dashboard/mobile interface
- server-side ML classification of activity integrity
- automatic XP/streak updates from verified activity

These are documented as future extensions rather than current implemented features.
