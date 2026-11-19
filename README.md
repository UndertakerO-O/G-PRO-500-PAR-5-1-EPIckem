# EPIckem

Sports prediction application developed as part of the EPIckem project.

> The project specification PDF is not yet available in the repository. This
> README is a starting point and will be completed with the required features,
> business rules, and delivery criteria from the specification.

## Technology Stack

| Layer | Technology |
| --- | --- |
| Database | PostgreSQL |
| ORM | Prisma |
| Backend | NestJS |
| Frontend | Vue.js |

## Planned Architecture

```text
EPIckem/
├── frontend/       # Vue.js application
├── backend/        # NestJS REST API
└── README.md
```

The backend exposes the API consumed by the frontend. Prisma manages access to
the PostgreSQL database and database migrations.

## Prerequisites

- Node.js (version to be defined)
- npm or pnpm (to be defined)
- PostgreSQL

## Getting Started

Installation and startup commands will be added after the frontend and backend
applications are initialized.

## Features

To be completed from the project specification:

- [ ] Authentication and user management
- [ ] Competition, match, and prediction management
- [ ] Score and leaderboard calculation and display
- [ ] Prediction entry and browsing interface
- [ ] Roles and administration

## Environment Variables

The backend will use a `DATABASE_URL` variable for the PostgreSQL connection,
for example:

```env
DATABASE_URL="postgresql://user:password@localhost:5432/epickem?schema=public"
```

Never commit files that contain secrets (`.env`).

## Documentation to Add

- Data model and Prisma migrations
- API endpoints
- Point calculation rules
- Contribution guide and coding conventions
