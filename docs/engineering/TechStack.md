# Sweet Paws Technology Stack

## Purpose

This document records the planned technology stack for the Sweet Paws MVP and the responsibility of each part. It is a decision baseline, not a package-lock file: exact dependency versions are selected and pinned when the application is scaffolded.

## Guiding principles

- Prefer a small, well-supported stack over bespoke infrastructure.
- Keep the medical journal and its validation rules independent of screens and Firebase SDK calls.
- Use Firebase as a Backend-as-a-Service for the MVP; introduce server-side components only when a feature requires trusted execution, scheduled processing, or a third-party integration.
- Build a responsive web application first, with PWA capabilities rather than separate native mobile applications.
- Avoid a global state-management library unless concrete complexity demonstrates a need.

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
| Authentication | Firebase Authentication | Account sign-in and authenticated identity. |
| Primary database | Cloud Firestore | Pet, journal, medication-definition, reminder, and application data. |
| File storage | Cloud Storage for Firebase | Future pet photos and document/attachment storage. |
| Hosting | Firebase Hosting | Delivery of the web application and PWA assets. |
| Optional server-side infrastructure | Node.js runtime on Firebase Cloud Functions | Introduced later only for webhooks, scheduled jobs, protected processing, or future AI endpoints. |
| Testing | Vitest and React Testing Library | Unit and component/integration tests. |
| Code quality | ESLint and Prettier | Static analysis and consistent formatting. |
| Git checks | Husky and lint-staged | Run focused quality checks before commits. |

## Frontend

### React and TypeScript

React 19 is the UI foundation. TypeScript is required for application code, including domain types, form values, and Firebase-facing data adapters.

React components are responsible for rendering and user interaction. They must not contain direct, scattered Firestore query logic or duplicate domain validation. Data access is isolated behind repository/service modules and custom hooks.

### Vite

Vite provides the local development server and production build. Environment-specific configuration uses Vite environment variables; secrets must never be embedded in the client bundle.

### React Router

React Router manages application routes such as authentication, pet selection, journal, routine entry, batch entry, import, reports, reminders, and settings. Short-lived Single Event/Edit and Export interactions are modal surfaces rather than primary routes. Routes that access pet data require both authenticated identity and authorisation checks through the data layer and Firestore security rules.

### CSS Modules and modern CSS

CSS Modules are the default styling mechanism. Modern native CSS is preferred over Sass.

- CSS custom properties hold the shared design tokens: colour, spacing, typography, elevation, radius, motion, and breakpoint values.
- Light, dark, and system themes are supported through tokens and a persisted theme preference.
- Layouts are mobile-first. The desktop-oriented Batch Entry screen may use a dense spreadsheet-like layout, while remaining accessible.
- Global CSS is restricted to reset/base styles, tokens, and application-wide theme rules.

### State management

The initial approach is React state, Context for genuinely app-wide concerns, custom hooks, and Firestore subscriptions.

Expected Context responsibilities include authenticated user, selected pet, locale, and theme. Server data must not be copied into a global client store without a demonstrated need. Redux is intentionally not part of the MVP stack.

## Forms and validation

React Hook Form handles form state, dirty state, submission state, and accessible field errors. Zod defines validation rules for each event type and is the single source of truth for form-level validation where practical.

The same event schemas and validation rules are used by:

- Single Event create and edit modes.
- Routine Entry.
- Batch Entry row validation.
- CSV import validation where compatible with imported source data.

Client-side validation improves usability but is not a security boundary. Firestore Security Rules independently enforce authorisation. Future trusted server-side operations apply their own validation and authorisation checks.

## Internationalisation

The MVP supports English and Ukrainian through i18next and react-i18next.

- User-facing strings live in translation resources; feature components do not hardcode English text.
- Locale-specific date and number formatting uses the active locale.
- Medical measurement units are controlled by the pet's preferences and each event's stored source unit, not by translation alone.
- Translation keys are stable, semantic identifiers rather than English sentences.

## Firebase platform

The MVP communicates directly with Firestore for standard application operations. Server-side components are introduced only for features that require trusted execution, scheduled processing, or third-party integrations.

`../architecture/FirebaseArchitecture.md` defines the Firebase architecture, repository boundary, security model, backup philosophy, and optional server-side infrastructure in detail.

### Firebase Authentication

Firebase Authentication supplies the signed-in user identity. The specific sign-in methods are a separate product decision; email/password and a major identity provider may be considered during implementation.

Authentication alone does not grant access to pet data. Access is governed by Firestore security rules and the pet membership/ownership model.

### Cloud Firestore

Cloud Firestore is the primary operational datastore for the MVP. It stores journal events, pets, user preferences, pet-scoped medication definitions, reminders, and import metadata. Standard reads and writes—including journal CRUD, pet loading, and preference updates—go from repositories in the React application directly to Firestore; they do not require a backend API.

Firestore supports real-time UI updates and offline persistence, but its constraints shape the design:

- The UI queries bounded date ranges and paginates long journal histories.
- Required composite indexes are documented alongside each supported query.
- Event data follows the canonical contract in `../domain/EventSchemas.md`.
- The client must not assume offline writes have synchronised until acknowledgement is visible.
- Security Rules enforce the ownership/membership model for every read and write.

Firestore is not used as a substitute for a reporting warehouse or unrestricted full-text search engine. The MVP uses bounded journal filters and simple text search; richer search and analytics are future decisions.

### Cloud Storage for Firebase

Cloud Storage is reserved for files that do not belong in Firestore documents, such as pet photos and future attachments. Storage access is controlled by Firebase Storage Security Rules tied to authorised pet access. The MVP does not need attachments, so Storage may remain unused initially.

### Firebase Hosting

Firebase Hosting serves the production web application, including PWA assets. Deployment environments, custom domain setup, headers, and preview-channel strategy are documented when deployment work begins.

### Optional Firebase Cloud Functions and Node.js

Cloud Functions are not part of the core MVP architecture. They are introduced only when a feature needs trusted execution, server credentials, scheduled processing, or a third-party integration. Ordinary journal CRUD does not require Cloud Functions.

Possible later uses include:

- Reminder scheduling and notification delivery.
- Telegram webhook processing, if the optional bot is added.
- Server-side PDF report generation.
- Import operations that exceed safe client-side limits, if needed.
- Future AI endpoints.

When introduced, Functions must validate authenticated requests, enforce authorisation, use idempotency for externally retried work, and avoid placing clinical decision-making in automation.

## PWA and offline behaviour

The Vite application is configured as an installable Progressive Web Application with a web app manifest and service worker.

The initial offline goal is deliberately narrow:

- The installed application shell and recently accessed data remain available where browser storage permits.
- A user can see visible online/offline status.
- Pending journal writes are visibly differentiated from synchronised writes.
- Offline behaviour and conflicts follow `../domain/TimeAndUnits.md` and `../architecture/OfflineAndSync.md`.

Offline capability does not promise that reminders, notifications, large imports, exports, or every historical record function without connectivity.

## Testing and quality controls

Vitest is the test runner and React Testing Library tests user-visible component behaviour.

Testing priorities for the MVP:

- Zod validation for every journal event type.
- Timezone and unit conversion/formatting edge cases.
- Single Event create/edit behaviour.
- Routine and Batch Entry validation and submission behaviour.
- Journal sorting, filtering, and latest-entry initial position.
- Repository/data-adapter behaviour with Firebase emulators where practical.
- Firestore Security Rules tests for ownership and unauthorised access.

ESLint and Prettier run locally and in continuous integration. Husky and lint-staged run focused checks on staged files before commits; they complement, rather than replace, CI.

## Deliberate non-choices

The MVP does not select the following until a demonstrated need exists:

- Redux or another global state-management library.
- A separate REST or GraphQL API for ordinary journal CRUD.
- Sass, CSS-in-JS, or a component-library dependency.
- A separate relational database.
- Native iOS or Android applications.
- An analytics, error-monitoring, or product-analytics vendor. These need a separate privacy and observability decision before adoption.

## Related documents

- `../product/MVPDefinition.md` — product scope and release boundaries.
- `../product/ProductRequirements.md` — required user workflows.
- `../domain/EventSchemas.md` — journal-event domain contract.
- `../domain/TimeAndUnits.md` — timestamp and measurement-unit rules.
- `../architecture/FirebaseArchitecture.md` — Firebase BaaS model, repositories, security, portability, and optional Functions.
- `../architecture/Firestore.md` — Firestore collection, query, and Rules principles.
- `../architecture/OfflineAndSync.md` — offline and synchronisation contract.
- `../product/Notifications.md` — reminder and notification design.
- `TechDecisions.md` — active technical decisions.
