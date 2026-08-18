# Backend Architecture

## Guiding principle

Sweet Paws is a conventional full-stack application. The React frontend communicates with a REST API; the API is the only application layer that communicates with MongoDB Atlas.

This protects database credentials and authorisation logic from the browser, while keeping the system small enough for the MVP. No additional infrastructure is introduced unless a concrete feature needs it.

## Goals

- A clear React → REST API → backend application layer → repository → MongoDB flow.
- Secure authentication and authorisation enforced on the backend.
- Separation of API transport, business logic, and database access.
- Repository abstraction so MongoDB remains an implementation detail.
- Minimal operational complexity and free-tier-friendly initial hosting.
- A path to later integrations without changing frontend page architecture.

## Architecture

```text
React UI and feature hooks
        |
        v
Frontend API clients / repositories
        |
        v
HTTPS REST API
        |
        v
Express controllers and middleware
        |
        v
Application services / use cases
        |
        v
Backend repository interfaces and MongoDB implementations
        |
        v
MongoDB Atlas
```

The browser never connects directly to MongoDB Atlas. It sends authenticated requests to the backend API, which validates identity, authorisation, and input before invoking application logic.

## Backend layers

### API layer

Express routes, controllers, request/response DTOs, authentication middleware, authorisation middleware, error handling, and HTTP concerns live here.

Controllers remain thin: they translate HTTP requests into application-service calls and return consistent HTTP responses. They do not contain journal business rules or MongoDB query logic.

### Application layer

Application services/use cases coordinate business workflows, such as creating an event, editing an event, importing a CSV batch, or exporting an authorised backup.

This layer enforces product rules that cannot be trusted to the client alone: ownership/membership, event validation, audit fields, idempotency, and multi-record consistency. It is independent of Express request objects and MongoDB driver types.

### Repository layer

Repository interfaces represent the persistence operations required by the application layer. MongoDB implementations map domain objects to database documents and contain query/index details.

Expected repositories include `JournalRepository`, `PetRepository`, `ReminderRepository`, `MedicationRepository`, and `UserPreferencesRepository`. Repository interfaces use domain-oriented inputs/outputs, not MongoDB collection or query objects.

### Infrastructure layer

MongoDB Atlas connection setup, repository implementations, configuration loading, and future external-service adapters live here. Only this layer imports MongoDB driver types for normal database access.

## Standard application operations

The React app uses the REST API for standard operations:

- Create, edit, and delete journal events.
- Load/filter/paginate journals.
- Create, load, update, and archive pets.
- Manage medication definitions and reminders.
- Update account preferences.
- Import canonical CSV data and export authorised CSV/JSON data.

The frontend maintains its own API-client/repository boundary so UI components and feature hooks do not make scattered `fetch` calls.

## Authentication and authorisation

Authentication is handled by the backend rather than a browser-to-database provider. The concrete session/token mechanism is a dedicated security implementation decision; it must provide a reliable authenticated user identity to Express middleware.

Authorisation is enforced server-side using pet ownership and membership data:

- Every pet-scoped request verifies the caller's allowed membership.
- Child resources inherit access from their pet.
- Users cannot gain access by changing identifiers in API requests.
- Role/write policy is defined in `PermissionsAndSharing.md`.

Client-side route guards and hidden controls improve UX only; backend authorisation is the security boundary.

## Hosting and deployment

The backend is a Node.js + TypeScript + Express service, initially planned for Render's free tier. Render is an initial hosting choice, not a pricing guarantee.

The frontend is a static Vite/PWA build hosted on a free-tier static hosting provider selected when deployment begins. It communicates with the backend through an environment-configured API base URL.

## Optional future capabilities

The backend exists from the MVP start for API, authentication, and persistence. Additional server-side features are added only when justified, for example scheduled reminder delivery, Telegram webhooks, server-side PDF generation, large import processing, or future AI endpoints.

Each such capability remains a narrow application service/adapter; it must not turn routine journal CRUD into an unnecessarily complex distributed system.

## Backup and portability

Complete versioned JSON export remains a product requirement. Exports are generated from domain data through application services and repositories, not as a raw MongoDB database dump.

This provides user-controlled backup, migration between installations, and a practical path to future database migration. Timezone and unit preservation follow `../domain/TimeAndUnits.md`.

## Related documents

- `MongoDB.md` — database collections, indexes, and persistence principles.
- `PermissionsAndSharing.md` — ownership and future sharing model.
- `OfflineAndSync.md` — client queue and synchronisation contract.
- `../engineering/TechStack.md` — selected technology stack.
- `../engineering/RepositoryStructure.md` — frontend and backend source layout.
