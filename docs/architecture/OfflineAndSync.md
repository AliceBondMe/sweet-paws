# Offline and Sync

## Purpose

This document defines the user-facing contract for offline behaviour. Sweet Paws may use Firestore's local persistence, but it must not treat local storage as proof that a write has reached the server.

## MVP goal

The MVP remains useful during short connectivity loss without implying that it is fully offline-first medical software.

- The installed PWA shell and recently accessed content are available where browser storage permits.
- Recently viewed pet and journal data may be displayed from local cache and identified as potentially stale.
- A user can create or edit ordinary journal events while offline when the platform can queue the operation.
- Pending changes synchronise automatically when connectivity returns.
- The UI visibly distinguishes pending, synchronised, and failed changes.

Features that require a network connection, such as authentication refresh, broad historical queries, imports, exports, and reliable background reminder delivery, must state that requirement rather than fail silently.

## Event-write lifecycle

Every user-initiated write has a visible lifecycle:

1. **Editing:** unsaved values exist only in the form.
2. **Queued/pending:** the local application accepted the write but server acknowledgement has not arrived.
3. **Synchronised:** Firestore confirmed the write.
4. **Failed:** the write could not be accepted or synchronised; the app retains sufficient context to let the user retry or correct it.

Pending is not equivalent to confirmed. The application must not silently present an offline entry as permanently saved.

## UI requirements

- Show a concise application-level offline indicator.
- Mark individual pending journal entries and batch/routine saves until synchronised.
- Preserve form input on validation error, connection loss, navigation warning, or temporary failure.
- Provide a retry path for failed writes and explain errors in plain language.
- Do not duplicate an event when the user retries after an uncertain connection state.
- Avoid disruptive confirmation dialogs for ordinary successful sync; surface failures prominently enough to be actionable.

## Conflicts and concurrent edits

The MVP does not claim collaborative real-time conflict resolution. The likely initial behaviour is last confirmed write wins for independently edited fields, backed by `updatedAt` and audit metadata.

Before shared caregiver editing is introduced, the product must define:

- How two simultaneous edits to the same event are detected or resolved.
- Whether the user sees conflict details or a last-write notice.
- How deletions interact with offline edits.
- Which audit history is retained for recovery.

This is acceptable for a single-owner MVP only if the UI clearly reports sync failure and never hides an unresolved write.

## Idempotency and duplicates

Network retries, double taps, and browser reloads can create duplicate writes unless the repository layer supplies stable client-generated event IDs or idempotency keys.

- Single Event, Routine Entry, and desktop Batch Entry must be safe to retry.
- Routine Entry writes related individual events together where possible.
- Batch Entry reports saved versus unsaved rows accurately if work must be split due to platform limits.
- Import duplicate detection is separate and follows `../product/Import.md`.

## Cache and data limits

Browser cache can be cleared, may be unavailable in private browsing, and is not a backup. The application must not promise access to complete history offline.

Users retain control through JSON export, not through reliance on browser cache. The app should avoid unbounded listeners and unbounded cached journal queries for both cost and device-storage reasons.

## Time and sync

An event's `occurredAtUtc` is determined from the owner-selected local time and timezone before it is queued. A delayed sync does not change when the care occurred. Details are defined in `../domain/TimeAndUnits.md`.

## Testing

Offline testing includes:

- Creating and editing entries without a connection.
- Regaining connectivity and confirming final sync state.
- Repeated submission after timeout/double tap.
- Failed writes caused by Security Rules or validation.
- Browser refresh during pending work.
- Cached journal display with clear stale/pending indicators.

## Open decisions

- Exact conflict-resolution UI before multi-user sharing.
- Which edits may be queued offline versus requiring confirmation online.
- Cache retention/eviction behaviour and supported-browser policy.
- Whether a user may explicitly keep a local draft outside Firestore persistence.
