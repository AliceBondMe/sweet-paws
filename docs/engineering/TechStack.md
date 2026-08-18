# Sweet Paws Technology Stack

## Purpose

This document records the planned MVP technology stack and each part's responsibility. It is a decision baseline, not a package-lock file: exact dependency versions are selected and pinned when the application is scaffolded.

## Guiding principles

- Prefer a small, well-supported stack over bespoke infrastructure.
- Keep medical-journal rules independent of screens, HTTP transport, and database access.
- Use a conventional full-stack boundary: React communicates with a REST API; only the backend communicates with MongoDB.
- Build a responsive web application first, with PWA capabilities rather than separate native mobile applications.
- Avoid global state libraries and extra infrastructure unless concrete complexity demonstrates a need.

## Stack at a glance

| Area | Selected technology | Responsibility |
| --- | --- | --- |
| Frontend | React 19, TypeScript | User interface and client-side application logic. |
| Build tooling | Vite | Development server, builds, and environment configuration. |
| Routing | React Router | URL-based navigation and route protection. |
| Styling | CSS Modules and modern CSS | Component-scoped styles, responsive layout, design tokens, and themes. |
| Forms | React Hook Form | Performant form state and submission handling. |
| Validation | Zod | Shared, explicit validation schemas and typed form data. |
| Internationalisation | i18next and react-i18next | English and Ukrainian UI translations, with room for further locales. |
| Backend | Node.js, TypeScript, Express | REST API, authentication/authorisation, application services, and integrations. |
| Database | MongoDB Atlas | Operational storage for pets, journals, reminders, and preferences. |
| Backend hosting | Render free tier initially | Planned deployment host for the Node/Express service. |
| Frontend hosting | Free-tier static hosting, provider to be selected | Delivery of the Vite PWA build. |
| Testing | Vitest and React Testing Library | Frontend unit and component/integration tests. |
| Code quality | ESLint and Prettier | Static analysis and consistent formatting. |
| Git checks | Husky and lint-staged | Run focused quality checks before commits. |

## Frontend

### React and TypeScript

React 19 is the UI foundation. TypeScript is required for application code, including domain types, form values, and API-facing data adapters.

React components render and coordinate interaction. They do not contain scattered REST calls or duplicate domain validation. Data access is isolated behind frontend API-client/repository modules and custom hooks.

### Vite and React Router

Vite provides the local development server and production build. Environment-specific configuration uses Vite environment variables; browser-visible variables must not contain backend or database secrets.

React Router manages persistent destinations such as Journal, Routine Entry, Batch Entry, Import, Reports, Reminders, and settings. Short-lived Single Event/Edit and Export interactions are modal surfaces rather than primary routes. Routes that access pet data require an authenticated API session and backend authorisation.

### Styling and state

CSS Modules and modern native CSS are the default styling mechanism. CSS custom properties hold design tokens and support light, dark, and system themes. Layouts are mobile-first; Batch Entry is intentionally desktop-first.

The initial state approach is React state, Context for app-wide concerns, custom hooks, and API-backed feature state. Expected Context responsibilities include authenticated user, selected pet, locale, and theme. Redux is intentionally not part of the MVP stack.

## Forms and validation

React Hook Form handles form state, dirty state, submission state, and accessible field errors. Zod defines validation schemas for event types and form data.

Shared client validation is used by Single Event/Edit, Routine Entry, Batch Entry rows, and CSV import where compatible. Client validation improves UX but is not the security boundary: backend application services validate requests independently.

## Internationalisation

The MVP supports English and Ukrainian through i18next and react-i18next. User-facing strings live in translation resources, while locale-aware date/number formatting follows the active locale. Measurement units are governed by each pet's preferences and each event's stored source unit, not translation alone.

## Backend and database

### Node.js, TypeScript, and Express

The backend is a Node.js + TypeScript + Express service. It exposes a REST API, performs authentication and authorisation, runs application/business logic, and invokes repositories. Controllers remain thin; MongoDB access lives behind repository implementations.

The React frontend communicates with this API for all standard application operations. There is no direct browser-to-database access.

### MongoDB Atlas

MongoDB Atlas is the primary operational database. It stores pets, journal events, user preferences, medication definitions, reminders, and import metadata. Queries are bounded and paginated; indexes follow documented endpoint/query needs.

MongoDB credentials and connection strings remain server-side. The browser never receives them.

### Hosting

Render is the initial planned backend host using its free tier where appropriate. The static frontend host is deliberately not selected yet; it only needs to serve the Vite PWA build and securely configure its API base URL.

## PWA and offline behaviour

The Vite application is configured as an installable Progressive Web Application with a web app manifest and service worker.

The initial offline goal is deliberately narrow: the app shell and recently accessed content remain available where browser storage permits; the UI shows online/offline state; and pending writes are visibly differentiated from confirmed API writes. Offline queues, conflicts, and retries follow `../architecture/OfflineAndSync.md`.

## Testing and quality controls

Vitest and React Testing Library cover frontend validation and user-visible behaviour. Backend tests cover controllers, application services, repositories, authorisation, and MongoDB integration using isolated test data.

Testing priorities include event schemas, timezone/unit edge cases, `Hi` glucose results, form flows, journal ordering, API authorisation, idempotent retries, and repository query/index behaviour.

ESLint and Prettier run locally and in continuous integration. Husky and lint-staged run focused checks on staged files before commits.

## Deliberate non-choices

The MVP does not select a separate GraphQL API, a separate relational database, Redux, Sass/CSS-in-JS, a native mobile app, a MongoDB ODM, an analytics vendor, or an error-monitoring vendor. Each requires a concrete need and a documented decision before adoption.

## Related documents

- `../architecture/BackendArchitecture.md` — API, application, repository, and hosting boundaries.
- `../architecture/MongoDB.md` — collections, indexes, and persistence principles.
- `../product/MVPDefinition.md` — product scope and release boundaries.
- `../domain/EventSchemas.md` — journal-event domain contract.
- `../architecture/OfflineAndSync.md` — offline and synchronisation contract.
- `RepositoryStructure.md` — planned frontend/backend source layout.
