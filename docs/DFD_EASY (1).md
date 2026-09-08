# StepUp — Data Flow Diagrams

## 1. Context / Level 0 DFD

The StepUp system has two external entities:

- **Student / API Client** — sends registration, activity, integrity-classification, challenge, battle, sport, wallet and consent requests.
- **Administrator** — uses the web dashboard to manage students, challenges, battles, sports/sessions, wallet/leaderboard information, evaluation data and privacy requests.

The central **StepUp System** consists of the backend REST API, database and administrator dashboard.

> **Implementation note:** The current build does not contain a live fitness API integration or an outbound email/push notification service. Activity data is supplied by the caller through the REST API, and notifications are stored as database records.

## 2. Level 1 DFD

The system is decomposed into nine major processes:

1. **User Management**
   - Registration and login
   - Profile management
   - Role management

2. **Activity Synchronisation**
   - Receive activity data
   - Validate activity fields
   - Deduplicate using `syncId`
   - Store `ActivityRecord`

3. **Integrity Verification**
   - Store integrity classification
   - Store confidence and verified/suspicious step counts
   - Update `isVerified`

4. **Challenge Management**
   - Create and update challenges
   - Manage challenge participation
   - Track progress

5. **Step Battle Management**
   - Create battles
   - Accept battles
   - Resolve battles
   - Handle stakes and rewards

6. **Sport Buddy & Sessions**
   - Manage sport profiles
   - Find compatible sport buddies
   - Manage sport sessions

7. **Wallet Management**
   - Maintain StepCoin balance
   - Record wallet transactions
   - Process battle stakes and rewards

8. **Notification Management**
   - Store notifications
   - Retrieve notifications
   - Track read/unread state

9. **Admin / Evaluation / Privacy**
   - Dashboard statistics
   - Detector evaluation metrics
   - Pilot information
   - Consent
   - Data-deletion requests
   - Audit information

## 3. Level 2 DFD — Activity Synchronisation and Integrity Verification

### Activity flow

```text
Student / API Client
        |
        | Activity data
        v
2.1 Receive Activity
POST /activity/sync
        |
        v
2.2 Validate & Deduplicate
        |
        |-- duplicate syncId --> return existing record
        |
        v
2.3 Store Activity
Create ActivityRecord
        |
        v
ActivityRecord
```

### Integrity flow

```text
Student / API Client
        |
        | Integrity classification
        | confidence
        | verified/suspicious steps
        v
2.4 Store Integrity Result
        |
        v
IntegrityResult
        |
        v
2.5 Update Verification
        |
        | Genuine walking/running
        |        -> isVerified = true
        |
        | Other classifications
        |        -> isVerified = false
        v
Activity status / verification result
```

### Integrity classifications

- `GENUINE WALKING`
- `GENUINE RUNNING`
- `PHONE SHAKING`
- `VEHICLE MOVEMENT`
- `DUPLICATE`
- `UNKNOWN`

The classification is currently supplied by the calling client. It is **not** computed by a server-side ML model in the present implementation.
