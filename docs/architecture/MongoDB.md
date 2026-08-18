# MongoDB Design

## Purpose

This document defines MongoDB Atlas responsibilities, collection boundaries, query/index principles, and persistence rules for the Sweet Paws MVP. It complements `BackendArchitecture.md`: database access happens only through backend repository implementations.

## Principles

- MongoDB Atlas is the operational database; it is never exposed directly to the browser.
- Backend repositories own MongoDB driver usage, document mapping, and query construction.
- Backend application services enforce authentication, membership, validation, and audit rules before repository calls.
- Documents are shaped for supported product queries.
- Journal history is bounded and paginated; the API does not return years of events unnecessarily.
- Database indexes are explicit and documented alongside the query patterns that require them.

## Conceptual collections

```text
users
pets
petMemberships
journalEvents
medicationDefinitions
reminders
importBatches
```

The final physical schema may evolve during implementation, but each collection preserves the domain boundaries described below. A separate `petMemberships` collection is preferred for future sharing queries and explicit access control; the backend treats it as the authority for pet-scoped access.

## Collection responsibilities

### Users

Stores account-level preferences such as locale, theme, and selected-pet convenience state. It does not duplicate pet medical history.

### Pets

Stores pet profile data from `../domain/EventSchemas.md`: name, species, home timezone, unit preferences, lifecycle status, and ownership metadata.

### Pet memberships

Stores the relationship between a user and a pet, including role. The MVP creates an owner membership; its structure supports future caregiver and veterinarian access without moving journal history.

### Journal events

Stores the unified journal-event contract and type-specific payloads from `../domain/EventSchemas.md`.

- `petId` and `occurredAtUtc` support bounded journal queries.
- Soft-deleted events are excluded from ordinary results.
- Source/import/audit metadata supports corrections and duplicate handling.
- Historical event content is not rewritten merely because a display preference or reusable medication label changes.

### Medication definitions, reminders, and import batches

Medication definitions are pet-scoped reusable entry options, not prescriptions. Reminders are planned care, never evidence of care. Import batches store source assumptions, outcome counts, and errors needed for user-visible audit/duplicate handling, but not an unapproved full source spreadsheet.

## Query and index principles

- Journal queries scope by `petId` and bounded `occurredAtUtc` range, use chronological order, and paginate.
- Event-type filters are supported only when backed by documented indexes.
- API endpoints return only the records required by a page/workflow; no broad “load all pet history” endpoint exists.
- Reports use bounded date ranges and deliberate aggregation/query design.
- Search remains limited in the MVP; MongoDB Atlas is not assumed to provide unrestricted full-text search without a separately approved design.
- Pet-list and membership queries are bounded to the authenticated user.

Each implemented repository query has an accompanying index decision and test coverage.

## Consistency, transactions, and idempotency

Single Event writes are application-service operations. Routine Entry creates several independent events in one logical operation and should use a MongoDB transaction when atomicity is required and supported by the selected Atlas deployment.

Batch Entry and CSV import validate before writing. If a large operation must be chunked, the API reports accurate saved/failed rows and never claims that unsaved records were imported. Stable client request IDs or idempotency keys protect against retry duplicates.

## Validation and security

MongoDB schema validation may be used as defence in depth, but it does not replace application-layer validation. The backend validates domain schemas, derives audit fields from the authenticated caller, and rejects cross-pet access.

MongoDB Atlas credentials remain server-side only. Network access controls, least-privilege database users, and environment-managed connection strings are configured during deployment.

## Testing

Repository and integration tests use an isolated test database or controlled MongoDB test environment. Essential tests cover ownership/membership enforcement, cross-pet rejection, event CRUD validation, pagination/indexed query behaviour, transactions, and idempotent retries.

## Open implementation decisions

- MongoDB driver versus an ODM; no ODM is selected by this architecture.
- Exact document validation rules and index definitions.
- Transaction boundaries for routine, batch, and import operations.
- Audit-history retention and recovery period for soft-deleted events.
