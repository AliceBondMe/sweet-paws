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

- **Status:** Superseded by DL-007
- **Decision:** Standard application operations communicated directly with Firestore through repositories; Cloud Functions were optional later infrastructure.
- **Reason:** This was initially chosen to reduce operational complexity.
- **Consequences:** Replaced by a conventional backend/API architecture before implementation.

### DL-006 — JSON backup and portability

- **Status:** Accepted
- **Decision:** Support complete, versioned JSON export of user data.
- **Reason:** Users need backups and the product should not trap their history in one database installation.
- **Consequences:** Exports use domain data rather than raw database documents and must preserve units/time context.

### DL-007 — Conventional REST backend with MongoDB Atlas

- **Status:** Accepted
- **Decision:** Replace the Firebase BaaS architecture with a Node.js + TypeScript + Express backend, REST API, and MongoDB Atlas database. Render is the planned initial backend host.
- **Reason:** Keep database access and authorisation on the server while using familiar controller, application-service, and repository boundaries.
- **Consequences:** The React app communicates only with the backend API; the browser never connects directly to MongoDB. Repository abstractions remain in both frontend and backend layers.

### DL-008 — Separate frontend and backend repositories

- **Status:** Accepted
- **Decision:** Keep React frontend source in this repository and Node.js/Express backend source in a separate companion repository.
- **Reason:** Keep frontend and backend deployment/ownership independent while preserving a clear REST API integration boundary.
- **Consequences:** Backend architecture documentation remains here for product coherence; source code, environment configuration, and deployment automation remain in the backend repository. API contract changes require coordinated updates.

## Entry template

```md
### DL-XXX — Short decision title

- **Status:** Proposed | Accepted | Superseded
- **Decision:**
- **Reason:**
- **Consequences:**
- **Supersedes / superseded by:** Optional reference
```
