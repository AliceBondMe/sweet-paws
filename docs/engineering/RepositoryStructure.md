# Repository Structure

## Purpose

This structure keeps frontend features and backend layers understandable while preserving repository abstractions around persistence. It is a target for implementation, not a requirement to generate empty folders in advance.

## Proposed top-level layout

```text
sweet-paws/
├─ docs/
├─ public/                         # Static PWA assets
├─ src/                            # React frontend
│  ├─ app/                         # Bootstrap, providers, router, shell
│  ├─ features/                    # User-facing feature modules
│  ├─ domain/                      # Frontend domain types and validation
│  ├─ repositories/                # Frontend API-facing repository interfaces
│  ├─ infrastructure/              # REST API client implementations/browser adapters
│  ├─ shared/                      # Cross-feature UI and utilities
│  ├─ i18n/
│  ├─ styles/
│  └─ test/
├─ server/                         # Node.js + TypeScript + Express backend
│  └─ src/
│     ├─ api/                      # Routes, controllers, middleware, HTTP DTOs
│     ├─ application/              # Use cases and business logic
│     ├─ domain/                   # Backend domain types and policies
│     ├─ repositories/             # Persistence interfaces
│     ├─ infrastructure/           # MongoDB and external adapters
│     ├─ config/                   # Server configuration
│     └─ test/
├─ tests/                          # Cross-application tests when needed
└─ configuration files
```

## Frontend structure

`src/features/` is organised around workflows: auth, onboarding, pets, journal, event-entry, routine-entry, batch-entry, reminders, import, export, reports, and settings. A feature owns its pages/modal composition, local components, hooks, view models, and tests.

`src/repositories/` contains frontend interfaces that express API operations in domain terms. `src/infrastructure/api/` implements them with HTTP requests, response mapping, authentication/session handling, and API error mapping. UI components and feature hooks do not make scattered `fetch` calls.

Frontend domain modules do not import React, Express, MongoDB, or CSS. They may share compatible Zod schemas with the backend later only through a deliberate shared-contract decision; a separate shared package is not introduced yet.

## Backend structure

### `server/src/api`

Contains Express routes, controllers, middleware, request parsing, response DTOs, and HTTP error translation. Controllers delegate immediately to application services.

### `server/src/application`

Contains use cases/business logic such as journal CRUD, routine save, batch/import processing, export generation, and membership checks. It depends on repository interfaces, not MongoDB driver types or Express objects.

### `server/src/repositories`

Contains interfaces such as `JournalRepository`, `PetRepository`, `ReminderRepository`, `MedicationRepository`, and `UserPreferencesRepository`. They use backend domain types, never HTTP DTOs or MongoDB collection objects.

### `server/src/infrastructure`

Contains MongoDB Atlas connection/configuration, repository implementations, document mappers, indexes/query helpers, and future external-service adapters. This is the only backend layer that imports MongoDB driver types for normal persistence.

## Dependency direction

```text
React features / shared UI
        ↓
Frontend API repositories
        ↓
REST API
        ↓
Express API/controllers
        ↓
Application services
        ↓
Backend repository interfaces
        ↓
MongoDB infrastructure
```

Dependencies move downward. UI does not depend on MongoDB; application services do not depend on Express or MongoDB; MongoDB implementations do not leak database types upward.

## Testing

- Frontend unit/feature tests live beside their owners and use Vitest/React Testing Library.
- Backend unit tests cover application services and domain policies.
- Backend integration tests cover Express endpoints, authorisation, repository behaviour, and MongoDB queries using isolated test data.
- Cross-feature/end-to-end tests are added under `tests/` only when a concrete need arises.

## Deferred structure

- No separate API gateway, worker service, message queue, or microservice is created for the MVP.
- No shared-package/monorepo tooling is introduced until frontend/backend contract duplication becomes a demonstrated problem.
- Integrations such as scheduled reminders, Telegram, PDF generation, or AI remain backend features added only when approved.
