# ADR-003: Choice of database and ORM (PostgreSQL + Prisma)

- **Status**: Accepted
- **Date**: 2026-11-06
- **Decision makers**: the project team

## Context

The project's data is highly relational: users, teams, tournaments, stages and pools, matches, predictions, pick'em questions, leaderboard and audit log.

Main requirements:

- **data integrity**: a user has only one prediction per match and one answer per pick'em question, and points are awarded only once;
- **ACID transactions** for resolving predictions and awarding points;
- relational queries (joins, aggregation of statistics and rankings);
- reproducible schema migrations, shared by the whole team;
- strong typing consistent with the rest of the TypeScript stack.

## Decision

- **Database**: **PostgreSQL**
- **ORM**: **Prisma**

## Alternatives considered

| Option | Advantages | Reasons for rejection |
|---|---|---|
| **MySQL / MariaDB** | Very widespread, simple | PostgreSQL offers better support for advanced types (JSONB, enumerations), constraints and complex queries |
| **MongoDB** | Flexible schema | Highly relational data; transactional and integrity guarantees are less natural for predictions and a leaderboard |
| **SQLite** | Zero configuration | Poorly suited to concurrent access and scalable deployment |
| **TypeORM** | Native NestJS integration, decorators | Schema scattered across classes, less strict typing, harder maintenance |
| **Drizzle ORM** | Lightweight, close to SQL | More recent, fewer resources, less accessible for beginners |

## Consequences

**Positive**

- ACID transactions and integrity constraints (foreign keys, uniqueness): a solid foundation for predictions and the leaderboard.
- Prisma describes the whole model in **a single readable file** (`schema.prisma`), which also serves as documentation of the data model.
- Migrations are generated and versioned in the repository; TypeScript types are generated automatically from the schema.
- PostgreSQL is open source, very widespread, and scales well (replication, advanced indexes).
- JSON columns let us store game-specific statistics and pick'em resolution rules (`StatEntry.value`, `resolutionSpec`) without one column per statistic.

**Negative / accepted risks**

- Prisma adds an abstraction layer: very complex queries may require raw SQL (`$queryRaw`).
- The schema forces us to think about the model early (the tournament bracket model in particular), at the cost of somewhat less flexibility than a document database.

**Points to watch**: design the match bracket model (slots fed by a participant or by the result of a previous match) before writing the first migrations.