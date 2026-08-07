# Firebase Architecture

## Guiding principle

The MVP communicates directly with Firestore for standard application operations. Server-side components are introduced only for features that require trusted execution, scheduled processing, or third-party integrations.

Sweet Paws uses Firebase as a Backend-as-a-Service in the MVP. This keeps operational complexity low while preserving a clear boundary between application logic and persistence.

## Goals

- Minimal operational complexity.
- Secure client-side CRUD operations.
- Direct React-to-Firestore communication for ordinary application work.
- Clear separation between UI, application/domain logic, and persistence.
- A path to add server-side capabilities later without rewriting UI features.
- User-controlled data portability and reduced vendor lock-in.

## MVP architecture

```text
React UI and hooks
        |
        v
Repository interfaces and implementations
        |
        v
Firebase client SDK / Cloud Firestore
        |
        v
Firestore Security Rules + Firebase Authentication identity
```

The React application does not call a custom backend API for routine operations. Firebase Authentication establishes who the user is; Firestore Security Rules determine whether that user may read or write a resource.

## Standard application operations

The following operations communicate directly with Firestore through repository implementations:

- Create, edit, and delete journal events.
- Load and filter journals.
- Load, create, update, and archive pets.
- Load and update user preferences.
- Load and maintain pet-scoped medication definitions.
- Load and maintain reminder data, subject to the final reminder design.

Direct client access is appropriate only because it is protected by Firestore Security Rules. UI checks improve the experience but are never the authorisation mechanism.

## Repository pattern

React components, routes, and forms do not make scattered Firebase SDK calls. They communicate with repository interfaces through custom hooks or application services. Repository implementations are responsible for translating between domain models and Firestore documents.

Initial repositories include:

| Repository | Responsibilities |
| --- | --- |
| `JournalRepository` | Journal event CRUD, date-range queries, event filters, pagination, and real-time subscriptions. |
| `PetRepository` | Pet lifecycle, pet profile, membership-aware loading, and pet preferences. |
| `ReminderRepository` | Reminder CRUD and next-reminder queries. |
| `MedicationRepository` | Pet-scoped reusable medication definitions. |
| `UserPreferencesRepository` | User-level UI and account preferences. |

Repository interfaces use the product's domain types rather than Firebase-specific types. Firestore timestamps, document references, query construction, and serialization remain inside the persistence layer.

This structure has three benefits:

1. UI features remain focused on workflows and accessibility.
2. Repository behaviour can be tested independently using emulators or test doubles.
3. Firestore can later be replaced or supplemented without forcing every UI component to change.

The repository pattern is an abstraction boundary, not a claim that a database migration will be cost-free. Domain contracts, exports, and disciplined data mapping are what make future migration realistic.

## Firestore data and query principles

Firestore is the primary operational database for pets, journal events, reminder data, medication definitions, import metadata, and user preferences.

- Journal queries are bounded by pet and date range, then paginated for long histories.
- Query patterns are documented before implementation so that required composite indexes are deliberate.
- The journal event contract is defined in `../domain/EventSchemas.md`.
- Firestore real-time listeners are used only where they materially improve the experience; unsubscribe behaviour is part of each feature's lifecycle.
- The client visibly distinguishes local/pending writes from synchronised writes.
- Large reporting, unrestricted full-text search, and analytical workloads are not forced into Firestore during the MVP.

## Authentication and authorisation

Firebase Authentication identifies the current user. It does not by itself grant access to pet data.

Authorisation is enforced by Firestore Security Rules using the ownership and future membership model:

- A user may access only pets for which they have an allowed membership/ownership relationship.
- Child resources such as journal events and medication definitions inherit access from their pet.
- Rules validate required ownership references and reject cross-pet or cross-user access.
- Server timestamps and audit fields are validated according to the final Firestore rules design.

Security Rules are tested with Firebase emulators. Client-side checks are supplementary and cannot be treated as security controls.

## Firebase Hosting

Firebase Hosting is the preferred hosting solution for the PWA. It serves the built React application, PWA manifest, service-worker assets, and future deployment preview channels.

## Firebase Storage

Firebase Storage is optional and is not a dependency of the MVP. It may later hold pet photos, generated/exported reports, and user-uploaded attachments. If adopted, Storage Security Rules must enforce the same pet ownership/membership model as Firestore.

## Optional Cloud Functions

Cloud Functions are not part of the core MVP architecture and are not required for ordinary CRUD operations.

They are introduced only for a feature that cannot safely or reliably run as a direct client-to-Firestore operation, including:

- Telegram webhook processing.
- Scheduled reminder delivery.
- Future AI endpoints.
- Server-side PDF generation.
- Heavy import processing, only if client-side processing becomes insufficient.

When added, a Function is a narrow feature-specific service. It validates its inputs, authenticates and authorises requests, protects secrets, is idempotent for retried external calls, and returns a domain-oriented result that existing UI architecture can consume. Adding a Function must not cause routine journal CRUD to move behind a general custom API.

## Backup and portability

Users retain control of their data through a complete JSON export containing their pets, journal history, reusable medication definitions, preferences needed to interpret the data, and a versioned export schema.

This supports:

- User-controlled backups.
- Migration to another Sweet Paws installation.
- A future migration away from Firebase, should the product need it.

The export model is defined in product/domain terms, not as a raw Firestore collection dump. Repository interfaces and mapping layers treat Firestore as an implementation detail, so the export remains meaningful if persistence changes later.

Import and export rules, including timestamp, timezone, and unit preservation, follow `../domain/TimeAndUnits.md` and the export specification.

## Cost objective

The application should be designed so that a typical household—one or a few pets shared by a small number of users, such as family members and a veterinarian—can use it comfortably within Firebase's free tier under normal usage patterns.

This is a design objective, not a promise about Firebase pricing, quotas, or future service changes. The architecture supports this objective through bounded queries, pagination, restrained use of real-time listeners, compact documents, and avoiding unnecessary server-side infrastructure.

Cost-impacting features and query patterns should be evaluated before release, especially long journal histories, broad real-time subscriptions, exports, notifications, and sharing.

## Related documents

- `../engineering/TechStack.md` — full selected technology stack.
- `../domain/EventSchemas.md` — canonical journal-event contract.
- `../product/ProductRequirements.md` — user workflows and non-functional requirements.
- `../domain/TimeAndUnits.md` — timestamp and unit rules.
- `Firestore.md` — collection layout, indexes, and Security Rules detail.
- `../product/Notifications.md` — reminder and notification behaviour.
- `OfflineAndSync.md` — offline user contract and synchronisation behaviour.
- `../product/Export.md` — CSV export and versioned JSON backup.
