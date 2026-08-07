# Decision Log

## Purpose

The decision log preserves the history of material product and architecture choices. `TechDecisions.md` shows the current active decisions; this log records when a decision was made, changed, or superseded.

## Entries

### DL-001 — Unified timeline as the core journal

- **Status:** Accepted
- **Decision:** All supported care event types appear in one chronological pet journal.
- **Reason:** Owners understand their pet's care as a sequence of events rather than separate technical data tables.
- **Consequences:** Event types share a common envelope; filters, charts, imports, and exports operate from one event stream.

### DL-002 — Medications and weight are MVP requirements

- **Status:** Accepted
- **Decision:** The MVP includes medication administration and weight events.
- **Reason:** They are essential context for real diabetic-pet care, not optional portfolio features.
- **Consequences:** Medication definitions are pet-scoped and free-text-first; starting and subsequent weight readings are journal events.

### DL-003 — UTC storage with preserved timezone context

- **Status:** Accepted
- **Decision:** Store event instants in UTC and preserve the entered/imported IANA timezone.
- **Reason:** Correct display, import, daylight-saving, travel, and auditing behaviour requires more than a local formatted timestamp.
- **Consequences:** Imports require explicit date-format and timezone confirmation when the source is ambiguous.

### DL-004 — Three data-entry workflows

- **Status:** Accepted
- **Decision:** Support Single Event, Routine Entry, and desktop-oriented Batch Entry.
- **Reason:** Isolated events, live care routines, and historical manual transcription are meaningfully different workflows.
- **Consequences:** All workflows create the same journal-event domain data while using specialised UI.

### DL-005 — Firebase BaaS MVP, no custom backend by default

- **Status:** Accepted
- **Decision:** Standard application operations communicate directly with Firestore through repositories; Cloud Functions are optional later infrastructure.
- **Reason:** This reduces operational complexity without sacrificing security when backed by strong Firestore Security Rules.
- **Consequences:** Repository and rules design are core work; functions are introduced only for trusted execution, scheduling, or integrations.

### DL-006 — JSON backup and portability

- **Status:** Accepted
- **Decision:** Support complete, versioned JSON export of user data.
- **Reason:** Users need backups and the product should not trap their history in one Firebase installation.
- **Consequences:** Exports use domain data rather than raw Firestore documents and must preserve units/time context.

## Entry template

```md
### DL-XXX — Short decision title

- **Status:** Proposed | Accepted | Superseded
- **Decision:**
- **Reason:**
- **Consequences:**
- **Supersedes / superseded by:** Optional reference
```
