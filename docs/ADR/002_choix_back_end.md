# ADR-002: Choice of back-end framework (NestJS, TypeScript)

- **Status**: Accepted
- **Date**: 2026-11-06
- **Decision makers**: the project team

## Context

The back end must:

- expose an API (REST, possibly complemented by real time) consumed by the front end;
- manage users and authentication;
- import and transform data from the start.gg GraphQL API;
- handle the prediction and pick'em logic (entering predictions, closing, resolution, awarding points), which demands rigor and reliability;
- send real-time updates (results, leaderboard);
- remain **readable and structured** for a team of several developers, and evolve without becoming unmanageable.

## Decision

We use **NestJS** (Node.js, TypeScript) for the back end.

Planned organization into independent modules: `auth`, `users`, `tournaments`, `matches`, `predictions`, `pickem`, `leaderboard`, `startgg-import`. Real time goes through NestJS WebSocket Gateways, and asynchronous tasks (start.gg import, prediction resolution and leaderboard computation) go through job queues.

## Alternatives considered

| Option | Advantages | Reasons for rejection |
|---|---|---|
| **Express / Fastify alone** | Very simple, very flexible | No imposed structure: code organization depends on each person's discipline, risk of drift in a team |
| **Nuxt server routes (Nitro)** | Single project, quick start | Tight coupling to the front end, less suited to substantial business logic (predictions, jobs, import) and to a modular architecture |
| **Another language (Python/FastAPI, Java/Spring, etc.)** | Mature frameworks | Loss of types shared with the front end, two languages to maintain in the team |

## Consequences

**Positive**

- Imposed modular architecture (modules, controllers, services), the same for all developers: more predictable code that is easier to review.
- Native dependency injection, which makes unit testing easier (notably of the predictions module).
- Input validation, authentication guards, interceptors and WebSocket gateways built in.
- Same language and same types as the front end: a `Match` or `Prediction` model defined only once.
- Mature, well-documented ecosystem; suited to a project that must last.

**Negative / accepted risks**

- Steeper initial learning curve (decorators, modules, dependency injection) than Express.
- More "structural" code (boilerplate) for small features.

**Points to watch**: keep the `predictions` module isolated and heavily tested; isolate the start.gg import behind an adapter so that our model is not coupled to that of the external API.