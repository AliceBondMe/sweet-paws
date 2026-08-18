# Repository Structure

## Purpose

Sweet Paws uses two separate code repositories: this frontend repository and a companion backend repository. This document keeps the full-stack architecture visible here while defining the source structure that belongs to each repository.

The backend documentation remains in this repository for product and architecture coherence, but backend source code, configuration, and deployment live in the companion backend repository.

## Frontend repository (this repository)

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
├─ tests/                          # Cross-application tests when needed
└─ configuration files
```

## Frontend structure

`src/features/` is organised around workflows: auth, onboarding, pets, journal, event-entry, routine-entry, batch-entry, reminders, import, export, reports, and settings. A feature owns its pages/modal composition, local components, hooks, view models, and tests.

`src/repositories/` contains frontend interfaces that express API operations in domain terms. `src/infrastructure/api/` implements them with HTTP requests, response mapping, authentication/session handling, and API error mapping. UI components and feature hooks do not make scattered `fetch` calls.

Frontend domain modules do not import React, Express, MongoDB, or CSS. They may share compatible Zod schemas with the backend later only through a deliberate shared-contract decision; a separate shared package is not introduced yet.

## Companion backend repository

The separate backend repository owns the Node.js + TypeScript + Express service and its Render deployment. Its internal structure follows the conventional layers defined in `../architecture/BackendArchitecture.md`:

```text
sweet-paws-backend/
├─ src/
│  ├─ api/                         # Routes, controllers, middleware, HTTP DTOs
│  ├─ application/                 # Use cases and business logic
│  ├─ domain/                      # Backend domain types and policies
│  ├─ repositories/                # Persistence interfaces
│  ├─ infrastructure/              # MongoDB and external adapters
│  ├─ config/                      # Server configuration
│  └─ test/
├─ tests/                          # Integration tests where needed
└─ configuration files
```

The frontend and backend repositories communicate through versioned REST API contracts. Neither repository imports source files from the other. Contract changes require coordinated updates to API documentation and compatibility tests.

### Backend layers

### `server/src/api`

Contains Express routes, controllers, middleware, request parsing, response DTOs, and HTTP error translation. Controllers delegate immediately to application services.

### `server/src/application`

Contains use cases/business logic such as journal CRUD, routine save, batch/import processing, export generation, and membership checks. It depends on repository interfaces, not MongoDB driver types or Express objects.

### `server/src/repositories`

Contains interfaces such as `JournalRepository`, `PetRepository`, `ReminderRepository`, `MedicationRepository`, and `UserPreferencesRepository`. They use backend domain types, never HTTP DTOs or MongoDB collection objects.

### `server/src/infrastructure`

Contains MongoDB Atlas connection/configuration, repository implementations, document mappers, indexes/query helpers, and future external-service adapters. This is the only backend layer that imports MongoDB driver types for normal persistence.

## Dependency direction across repositories

```text
Frontend repository                 Backend repository
──────────────────                  ──────────────────
React features / shared UI
        ↓
Frontend API repositories
        ↓
   HTTPS REST API  ─────────────→   Express API/controllers
                                         ↓
                                    Application services
                                         ↓
                                    Backend repository interfaces
                                         ↓
                                    MongoDB infrastructure
```

Dependencies move downward. UI does not depend on MongoDB; application services do not depend on Express or MongoDB; MongoDB implementations do not leak database types upward.

## Testing

- Frontend unit/feature tests live in this repository beside their owners and use Vitest/React Testing Library.
- Backend unit and integration tests live in the companion repository.
- API compatibility/contract tests are added to the owning repository, with coordinated coverage when endpoint changes affect the frontend.
- Cross-feature/end-to-end tests are added only when a concrete need arises and may live in either repository by agreement.

## Deferred structure

- No separate API gateway, worker service, message queue, or microservice is created for the MVP.
- The frontend and backend are intentionally not a monorepo. A shared contract package is not introduced until duplicate frontend/backend contract definitions become a demonstrated problem.
- Integrations such as scheduled reminders, Telegram, PDF generation, or AI remain backend features added only when approved.
