# ADR-001: Choice of front-end framework (Vue 3 + Vite, TypeScript)

- **Status**: Accepted
- **Date**: 2026-11-18
- **Decision makers**: the project team

## Context

We are building a tournament web platform: creation of tournament brackets, import of tournaments from the start.gg API, and predictions on matches and pick'em questions that earn points on a leaderboard.

The front end must:

- display readable and interactive tournament brackets;
- update in real time (results, leaderboard);
- handle authentication, predictions, pick'em answers and the leaderboard;
- remain **understandable by the whole team** and easy to maintain over time;
- integrate well with a TypeScript back end (shared types).

Constraints: team project with limited time, mixed levels of experience with front-end frameworks, free choice of stack.

The back end (NestJS, see ADR-002) already handles all the server logic. A large part of the application (predictions, pick'em, leaderboard, dashboard) sits behind a login, where search engine optimization (SEO) brings nothing.

## Decision

We use **Vue 3** (Composition API) with **TypeScript**, built and served by **Vite**.

Vite is the build tool and development server; it is not a framework. It is the default setup of a Vue project (`npm create vue@latest`).

Associated building blocks:

| Need | Choice |
|---|---|
| UI framework | Vue 3 |
| Build and development server | Vite |
| Routing | Vue Router |
| Global state | Pinia |
| API calls and data cache | TanStack Query (Vue) |
| Styling | Tailwind CSS |
| Real time | Socket.IO client |
| Bracket display | brackets-viewer.js (framework-independent) |
| Tests | Vitest + Vue Test Utils, Playwright (E2E) |

Folder structure conventions (by feature: `tournaments/`, `predictions/`, `pickem/`, `leaderboard/`...) will be defined and documented in the repository README.

## Alternatives considered

| Option | Advantages | Reasons for rejection |
|---|---|---|
| **Vue + Nuxt (SSR)** | Automatic routing, imposed structure, SEO and shareable link previews, many modules | Additional concepts (SSR, server/client execution distinction, hydration); duplicates the NestJS back end, which already handles the server side; SEO brings little on pages behind a login |
| **Next.js (React)** | Largest ecosystem, many resources | Heavier concepts to grasp (hooks, effect dependencies, server/client components); code less immediately readable for beginners |
| **SvelteKit** | Very concise code, very fast | Smaller ecosystem and fewer resources, higher risk of getting stuck when problems arise |

## Consequences

**Positive**

- A component fits in a single file (script, template, style), which makes code easier to read and review.
- Simple reactivity, gentle learning curve.
- No server rendering: fewer concepts to master and no hydration bugs.
- Simple deployment: the front end is made of static files.
- Instant startup and hot reload thanks to Vite.
- TypeScript types shared with the back end.
- The essential libraries (brackets, charts, real time, forms) have a Vue-compatible equivalent.

**Negative / accepted risks**

- No SSR: public pages (for example a tournament page) are less well indexed and do not display a rich preview when links are shared (Discord, social networks).
- Routing, global state and folder structure have to be assembled and enforced by ourselves (Vue Router, Pinia, team conventions), whereas Nuxt imposes them.
- Smaller ecosystem and fewer online resources than with React.
- If a very specific component (a drag-and-drop bracket editor, for example) does not exist in Vue, we will have to adapt or build it.

**Points to watch**

- Check early that brackets-viewer.js covers our display needs (double elimination, pools): this is the main identified risk.
- If SEO or sharing of tournament links becomes a goal, a migration to Nuxt remains possible, since Vue component code is reusable; this decision would then be recorded in a new ADR superseding this one.