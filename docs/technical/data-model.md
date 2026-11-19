# Initial Data Model Sketch

This diagram is an overview of the current Prisma schema. It is intentionally
conceptual: implementation details and fields can evolve as tournament rules
are refined. The executable schema lives in
[`backend/prisma/schema.prisma`](../../backend/prisma/schema.prisma).

```mermaid
erDiagram
    USER ||--o{ TEAM_MEMBER : joins
    TEAM ||--o{ TEAM_MEMBER : includes
    USER ||--o{ TEAM : captains
    USER ||--o{ REGISTRATION : enters_solo
    TEAM ||--o{ REGISTRATION : enters_as_team
    TOURNAMENT ||--o{ REGISTRATION : accepts

    GAME ||--o{ STATISTIC_DEFINITION : defines
    GAME ||--o{ TOURNAMENT : played_in
    USER ||--o{ TOURNAMENT : creates
    EXTERNAL_SOURCE o|--o{ TOURNAMENT : imports
    TOURNAMENT ||--o{ IMPORT_SYNC : synchronizes
    EXTERNAL_SOURCE ||--o{ IMPORT_SYNC : provides

    TOURNAMENT ||--|{ STAGE : contains
    STAGE ||--o{ POOL : groups
    STAGE ||--o{ MATCH : schedules
    STAGE ||--o{ QUALIFICATION_RULE : advances
    POOL ||--o{ MATCH : contains
    POOL ||--o{ POOL_STANDING : ranks
    REGISTRATION ||--o{ POOL_STANDING : ranked_as
    MATCH ||--|{ MATCH_SIDE : has
    REGISTRATION o|--o{ MATCH_SIDE : competes
    MATCH ||--o{ MATCH_GAME : consists_of
    MATCH_GAME ||--o{ STAT_ENTRY : records
    STATISTIC_DEFINITION ||--o{ STAT_ENTRY : describes

    USER ||--o{ PREDICTION : submits
    MATCH ||--o{ PREDICTION : receives
    TOURNAMENT ||--o{ PICKEM_QUESTION : asks
    PICKEM_QUESTION ||--o{ PICKEM_ANSWER : receives
    USER ||--o{ PICKEM_ANSWER : answers
    USER ||--o{ LEADERBOARD_ENTRY : earns_points
    TOURNAMENT ||--o{ LEADERBOARD_ENTRY : ranks
    TOURNAMENT ||--o{ AUDIT_LOG : records
    MATCH o|--o{ AUDIT_LOG : changed_in
    USER o|--o{ AUDIT_LOG : acts
```

## Main concepts

| Area | Entities | Responsibility |
| --- | --- | --- |
| Accounts and entrants | `User`, `Team`, `TeamMember`, `Registration` | User roles, team captain and membership, and solo/team tournament entries. |
| Games and data | `Game`, `StatisticDefinition`, `StatEntry` | Games define configurable statistics, whose values are recorded for each match game. |
| Tournament structure | `Tournament`, `Stage`, `Pool`, `QualificationRule`, `Match`, `MatchSide`, `MatchGame`, `PoolStanding` | Tournament formats, rounds, participants, scores, group tables, and advancement between stages. |
| Predictions | `Prediction`, `PickemQuestion`, `PickemAnswer`, `LeaderboardEntry` | Match winner/exact-score predictions, custom Pick'em questions, awarded points, and rankings. |
| External data and traceability | `ExternalSource`, `ImportSync`, `AuditLog` | Provider connector identity, import synchronization history, and sensitive-action history. |

## Important relationships and rules

- A tournament belongs to one game and contains ordered stages. A stage can be
  single elimination or round robin; round-robin stages may contain pools.
- A match has two participant slots. Each slot can point to a tournament
  registration; an empty slot supports byes during bracket generation.
- A match contains one or more match games. Statistics are attached to a match
  game and use definitions declared for that game's `Game`.
- Users submit one match prediction per match and one answer per Pick'em
  question. Awarded points feed per-tournament leaderboard entries.
- Imported tournaments identify their provider and external ID. Their source
  records synchronization attempts; imported results are intended to be
  read-only in application services.
- `AuditLog` records score changes and Pick'em actions, including before/after
  values where relevant.

Some invariants need transactional validation in the backend: each
registration must refer to exactly one solo user or team; a prediction must be
locked when its match starts; and imported tournament results must not be
edited locally. These are application rules, not fully expressed by the
current relational constraints.
