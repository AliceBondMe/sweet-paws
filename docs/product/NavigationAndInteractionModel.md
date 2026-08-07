# Navigation and Interaction Model

## Purpose

Sweet Paws uses a small number of persistent destinations and keeps short CRUD actions in modal dialogs. This preserves the owner's current context—especially in the Journal—rather than making every action a full-page transition.

The Journal is the application's home and primary working area after onboarding.

## Interaction categories

| Category | Meaning | MVP examples |
| --- | --- | --- |
| Persistent page | A destination an owner may spend time in, bookmark, navigate between, or return to. | Journal, Routine Entry, Batch Entry, Import, Reports, Reminders, settings. |
| Modal dialog | A focused, short-lived interaction that opens over the current context and closes back to it. | Single Event, Edit Event, Export, confirmations. |
| Global UI | Always-available application controls outside a page's main content. | Header, pet switcher, navigation drawer, theme/language controls, sync status. |

## Persistent pages

### Journal (Home)

The default destination after sign-in/onboarding.

**Purpose:** chronological medical journal, filtering, searching, reviewing history, and launching data-entry workflows.

**Displays:** oldest-first unified timeline, grouped by the selected timezone's local day and initially positioned at the latest entries; filters; search; paging; event summaries; and pending-sync state.

**Actions:** open Single Event modal, edit/delete an event, navigate to Routine Entry, Batch Entry, Import, Reports, Reminders, Pet Settings, and open Export modal.

The Journal is the working record, not an analytics dashboard. A small future summary area may show selected-pet context, latest glucose, or next reminder, but it does not create a separate Dashboard page.

### Routine Entry

**Purpose:** record a complete diabetes-care routine.

This is a dedicated page because owners spend time entering several related events. It uses a card-based layout for glucose, insulin, feeding, optional medication, and optional note. Saving creates separate journal events and returns to Journal.

### Batch Entry

**Purpose:** transcribe many historical records from notes or device memory.

This is a dedicated, desktop-first page with a spreadsheet-like keyboard-first grid, shared defaults, row editing, validation, and accurate multi-write result reporting. It does not import arbitrary spreadsheet layouts.

### Import

**Purpose:** import historical records from the documented Sweet Paws CSV format.

This dedicated page provides the template, format example, upload, complete-file validation, row-level errors, duplicate review, timezone review, confirmation, and final summary. It does not offer column mapping.

### Reports

**Purpose:** keep analytics and charts conceptually separate from the Journal.

The route/page is reserved from the MVP start. The MVP may provide only basic reporting such as a glucose trend; richer analytics, veterinary reports, and PDF output are future work.

### Reminders

**Purpose:** manage reminder definitions and view planned care.

Reminder completion/dismissal never creates a journal event automatically. If the owner records care from a reminder, the normal event-entry workflow creates the event.

### Pet Settings

**Purpose:** manage selected-pet configuration.

Includes name, species, timezone, glucose/weight units, future profile photo, and archive action. Timezone changes explain their effect on display/reminders without rewriting history.

### Account Settings

**Purpose:** manage account-wide preferences.

Includes language, theme, sign out, and future account-management functions.

## Modal dialogs

### Single Event

Opened by the Journal's floating `+` action. It creates one isolated event without leaving Journal context. The owner selects the event type and sees only relevant fields. The default time is now in the selected pet timezone.

### Edit Event

Uses the same modal component and form as Single Event, in Edit mode with existing data pre-populated. A successful save updates the Journal immediately and closes the dialog; there is no page navigation or loss of filter/scroll position.

### Export

Export is a short-lived modal interaction. The owner chooses export type, optional filters, generates/downloads the file, and closes the dialog. CSV remains pet/date/filter scoped; JSON remains a versioned authorised backup.

### Other dialogs

Delete confirmation, leave-with-unsaved-work confirmation, and short undo feedback are temporary dialogs/toasts. They must accurately reflect pending versus confirmed sync state.

## Global UI

### Desktop header

The desktop header contains the selected-pet switcher, primary navigation, language selector, theme selector, and account menu. The selected pet always remains visible because switching pets is part of normal daily work.

### Mobile navigation

Mobile uses a hamburger side drawer. It contains Journal, Reports, Reminders, Pet Settings, and Account Settings. Language and theme controls may also live there.

Routine Entry is launched prominently from Journal; Batch Entry and Import are available through Journal's data-entry/data-management actions. This keeps the drawer focused without hiding persistent workflows.

### Shared state and accessibility

- Global connectivity/sync status is concise but actionable.
- A pet switcher is available only in authenticated pet context; switching pets with an unsaved form requires confirmation.
- Dialogs have labelled purpose, focus trapping, Escape/close behaviour where safe, and focus return to their trigger.
- Page transitions move focus to the main heading; modal opening moves focus into the dialog.

## Onboarding and system routes

Sign in/account recovery and first-pet onboarding remain necessary transitional routes. A user with no pet completes onboarding, then lands in the new pet's Journal. Not-found and access-unavailable states return the owner safely to Journal or account context without exposing private data.

## Initial wireframe priority

Start visual design with Journal, Single Event/Edit modal, Routine Entry, Batch Entry, Import, and the desktop/mobile navigation shell. Reports can begin with a minimal basic-trend layout.
