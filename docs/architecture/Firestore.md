# Firestore Design

## Purpose

This document defines the intended Firestore responsibilities, data boundaries, query principles, and Security Rules approach for the Sweet Paws MVP. It complements `FirebaseArchitecture.md`: standard application operations communicate directly with Firestore through repository implementations.

## Principles

- Firestore is the operational datastore, not an exposed application model.
- Repositories own Firestore SDK usage, document mapping, and query construction.
- Every client read and write is authorised by Firestore Security Rules.
- Documents are shaped for the queries the product actually supports.
- Journal history is bounded and paginated; the client does not load years of events unnecessarily.
- Security-sensitive relationships are represented in data that Rules can verify efficiently.

## Conceptual collection layout

The final physical layout may evolve during implementation, but it should preserve these domain boundaries:

```text
users/{userId}
  └─ preferences and account-level settings

pets/{petId}
  ├─ profile and ownership metadata
  ├─ memberships/{userId}
  ├─ events/{eventId}
  ├─ medicationDefinitions/{medicationId}
  ├─ reminders/{reminderId}
  └─ importBatches/{importBatchId}
```

`pets` is the core security boundary. A pet's journal events and other pet-scoped data live beneath that pet or otherwise carry enough pet membership information for equivalent rule enforcement. The selected structure must make it straightforward to query a single pet's date-bounded journal.

## Collection responsibilities

### Users

`users/{userId}` contains account-level preferences such as locale, theme, and selected-pet convenience state. It must not duplicate the pet's medical history.

Only the authenticated user may read or update their user document, except for intentionally designed future administration flows.

### Pets

`pets/{petId}` stores the pet profile from `../domain/EventSchemas.md`: name, species, home timezone, unit preferences, lifecycle status, and required ownership/membership metadata.

The pet document is not a denormalised store of all latest medical data unless a specific read-performance need is established and its update mechanism is safe.

### Memberships

`pets/{petId}/memberships/{userId}` records access to a pet. The MVP may initially create only an owner membership, but the data model is prepared for later caregiver and veterinary access.

Memberships are the authoritative relationship for pet-scoped access. Convenience copies, if ever introduced for a user's pet list, must not replace this authority.

### Events

`pets/{petId}/events/{eventId}` stores unified journal events. Every event follows the shared envelope and type-specific payload requirements in `../domain/EventSchemas.md`.

- `occurredAtUtc` supports chronology and date-range queries.
- Soft-deleted events remain excluded from normal journal results.
- Source and import metadata support audit and duplicate handling.
- Events are not rewritten merely to change the display timezone or a reusable medication's current label.

### Medication definitions

`pets/{petId}/medicationDefinitions/{medicationId}` stores reusable, pet-scoped dropdown options. It is convenience data, not a prescription or replacement for the medication name snapshot recorded on an administration event.

### Reminders

`pets/{petId}/reminders/{reminderId}` stores planned care reminders. A reminder is not evidence that care occurred; completed care is always a separate journal event.

### Import batches

`pets/{petId}/importBatches/{importBatchId}` records an import's source metadata, assumptions, counts, status, and error summary. Imported events link to this batch. The document must not retain the complete original spreadsheet unless that need is explicitly approved and secured.

## Query and index principles

- The default journal query is scoped to one pet and a bounded `occurredAtUtc` date range.
- Results are ordered chronologically and paginated in the direction required by the journal's oldest-first display and initial latest-entry position.
- Event-type filters are applied only for supported combinations; each combination is evaluated for required Firestore composite indexes before release.
- Journal summary and Reports queries read only the bounded/latest data they need, rather than subscribing to the whole journal.
- Pet lists are small and membership-scoped. Any denormalised user-to-pet index must be protected and kept consistent by a documented strategy.
- Search remains limited in the MVP; Firestore is not treated as a general full-text search service.

Each implemented repository query must have a corresponding index/rules test and a note of its expected read scope.

## Security Rules model

Security Rules are the mandatory authorisation boundary. The UI hides unavailable actions for usability, but Rules independently prevent unauthorised reads and writes.

Rules must enforce at least:

- Requests are authenticated.
- The user has an allowed membership for the target pet.
- A member cannot access another pet by changing an identifier in the client.
- Pet-scoped child documents inherit the same membership check.
- New events have a matching `petId`, permitted event type, required common fields, and valid creator/audit expectations.
- A user cannot forge ownership or grant themselves membership.
- A user can edit or delete only within their allowed future role; exact role policy is defined in `PermissionsAndSharing.md`.
- Soft deletion cannot be used to bypass authorisation or mutate immutable identity/audit fields.

Rules validate access and basic structural invariants. They do not replace client schema validation, user-friendly error messages, or server-side validation for any future privileged Function.

## Transactions, batches, and consistency

Single-event writes are direct repository operations. Routine Entry creates several independent events in one deliberate operation; the repository should use an atomic Firestore write batch where the chosen limits allow it.

The desktop historical Batch Entry validates before submission and writes a clearly reported set of events. If its size exceeds Firestore batch limits, it must split work transparently, report progress, and never falsely report a complete import. Duplicate protection relies on stable client-generated identifiers or idempotency keys.

## Emulator testing

The Firebase Local Emulator Suite is used for repository and Security Rules tests where practical. Essential tests cover:

- Owner access to their pet and child documents.
- Rejection of access to another user's pet.
- Rejection of forged `petId`, owner, or membership fields.
- Allowed and rejected event create/edit/delete cases.
- Query patterns used by Journal, Reports, and Reminders.

## Open implementation decisions

- Exact membership role set and corresponding write permissions.
- Whether the MVP stores memberships only in pet subcollections or also maintains a secure user-facing pet index.
- Exact event payload validation that can be safely expressed in Rules versus Zod only.
- Audit-history retention and recovery period for soft-deleted events.
