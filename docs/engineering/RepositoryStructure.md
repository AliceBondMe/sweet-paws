# Repository Structure

## Purpose

This structure keeps features understandable while preserving the repository boundary between application/domain code and Firebase persistence. It is a target for the React/Vite application when implementation begins, not a requirement to generate empty folders in advance.

## Proposed top-level layout

```text
sweet-paws/
├─ docs/
├─ public/                         # Static PWA assets
├─ src/
│  ├─ app/                         # Bootstrap, providers, router, shell
│  ├─ features/                    # User-facing feature modules
│  ├─ domain/                      # Framework-independent types and rules
│  ├─ repositories/                # Repository interfaces
│  ├─ infrastructure/              # Firebase repository implementations/configuration
│  ├─ shared/                      # Cross-feature UI and utility code
│  ├─ i18n/                        # Translation setup and resources
│  ├─ styles/                      # Global reset, tokens, themes only
│  ├─ test/                        # Shared test helpers and fixtures
│  └─ main.tsx
├─ firebase/                        # Firestore/Storage rules, indexes, emulator config
├─ functions/                       # Absent initially; added only for approved optional Functions
├─ tests/                           # Cross-feature/emulator/end-to-end tests when needed
├─ .github/                         # CI workflows when added
└─ configuration files
```

## `src/app`

Owns application composition, not product features:

- React entry point and root providers.
- Router and route guards.
- Auth, selected-pet, locale, and theme provider wiring.
- `AppShell`, navigation, global error boundary, and connectivity status composition.

## `src/features`

Features are organised around user workflows. A feature owns its page(s), local components, hooks, view models, and tests.

```text
features/
├─ auth/
├─ onboarding/
├─ pets/
├─ journal/
├─ event-entry/                    # Single Event/Edit and type-specific fields
├─ routine-entry/
├─ batch-entry/
├─ reminders/
├─ import/
├─ export/                         # Export modal workflow and file generation
├─ reports/
└─ settings/
```

For example, `journal/` owns timeline composition, filters, pagination state, and Journal page tests. It calls `JournalRepository` through a hook/application service; it does not construct Firestore queries itself.

`event-entry/` owns the shared Create/Edit `EventForm` and type-specific fields. `routine-entry/` composes the same domain validation/field-level primitives but retains its routine-specific layout and submission orchestration.

## `src/domain`

Contains durable, framework-independent product concepts:

- Pet, membership, reminder, import/export, and journal-event types.
- Event discriminated unions, including numeric and `Hi` glucose results.
- Zod schemas and validation helpers.
- Timezone/unit conversion and formatting rules that are not UI-specific.
- Domain-level constants and pure functions.

Domain modules do not import React, Firebase SDK types, routing, or CSS.

## `src/repositories`

Contains interfaces and domain-oriented query/input types, for example:

- `JournalRepository`
- `PetRepository`
- `ReminderRepository`
- `MedicationRepository`
- `UserPreferencesRepository`

Interfaces express required operations without exposing Firestore document references, collection paths, or query objects.

## `src/infrastructure`

Contains external-service implementation details:

```text
infrastructure/
├─ firebase/
│  ├─ client.ts                    # Firebase client initialisation
│  ├─ auth/                        # Auth adapter/hooks where needed
│  ├─ firestore/                   # Mappers, query helpers, repository implementations
│  └─ emulator/                    # Local-development/testing integration
└─ browser/                         # PWA, storage, notification platform adapters
```

Firestore document serialization, timestamps, query/index assumptions, optimistic/pending write handling, and Firebase error mapping stay here. This is the only application area that imports the Firebase client SDK for normal CRUD.

## `src/shared`

Contains intentionally shared code with no feature ownership:

- Accessible primitive UI components such as dialog, toast, form error, loading/error/empty states.
- Cross-feature formatters that are presentation-specific.
- Small utility functions and shared types that do not belong to the domain.

Do not move a component here merely because it is short. Promote it after real cross-feature reuse is established.

## Firebase configuration

`firebase/` contains deployable Firebase artifacts separate from React source:

- Firestore Security Rules.
- Firestore index definitions.
- Storage Rules if Storage is later adopted.
- Emulator configuration and related tests.

Rules and indexes are versioned with the repository and tested alongside repository behaviour.

## Tests

- Unit tests live beside pure domain modules or components when local ownership is clear.
- Feature tests live in their feature folder and exercise user-visible behaviour with React Testing Library.
- Firebase emulator/Security Rules tests live near `firebase/` or under `tests/` when they span repositories.
- Fixtures use domain examples and do not include real pet data.

## Dependency direction

```text
app / features / shared UI
          ↓
       domain + repositories
          ↓
     infrastructure adapters
          ↓
       Firebase / browser APIs
```

Dependencies move downward. Domain and repository interfaces must not depend on features or Firebase. This is what allows direct-to-Firestore CRUD today without coupling every page to Firestore forever.

## Deferred structure

- `functions/` is added only when a Cloud Function is approved for a specific feature.
- A separate backend/API package is not created for normal CRUD.
- A monorepo is unnecessary until the product has a genuine second deployable application, such as an approved Telegram service.
