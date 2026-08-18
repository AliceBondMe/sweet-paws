# Sweet Paws Product Requirements

## Product statement

Sweet Paws is a Progressive Web Application that gives owners of diabetic pets one fast, chronological, and trustworthy place to record and review care.

## Personas

### Primary owner

An owner records glucose readings, insulin, food, medications, weight, and notes throughout the day. They are often using a phone one-handed and may be tired or stressed. They need low-friction entry and confidence that the journal reflects what happened.

### Future caregiver

A family member or pet sitter may later need to view or record care. Shared access is not part of the MVP, but the product must not assume a pet can only ever have one caregiver.

## Functional requirements

### Pet setup

When creating a pet, the owner must be able to provide:

- Name and species.
- Home timezone, confirmed from an IANA timezone such as `Europe/Kyiv`.
- Glucose display unit: `mmol/L` or `mg/dL`.
- Weight display unit: kilograms or pounds.
- Initial weight.

The setup flow must explain that the selected timezone is used to display the pet's journal and schedule reminders. Users must be able to change it later; the application must retain the timezone associated with historical entries.

### Journal

The journal must display all supported event types in one timeline for a single pet.

- Each event shows its local display time, type, primary value or description, and any relevant status.
- Events can be filtered by type and date range.
- The default order is oldest-first; the application initially positions the owner at the most recent journal entries, without changing the chronological order.
- When the owner opens an older date range or uses a filter/search, the application preserves an understandable position rather than unexpectedly returning them to the latest entry.
- Events with the same recorded timestamp must have deterministic ordering.
- Empty states explain how to add the first event.
- A removed event must not appear as an ordinary active journal event.

## Data entry workflows

All data-entry workflows create the same underlying journal events. The application supports three distinct workflows, each optimised for a different user scenario.

### Single event

**Purpose:** Record one event that was forgotten or occurred independently of a routine.

Typical examples: one glucose measurement, insulin injection, feeding, medication, weight measurement, or note.

Requirements:

- Available as a modal dialog from a floating `+` action, preserving the current Journal context.
- The user selects the event type.
- The form displays only fields relevant to the selected event type.
- Validation is specific to the selected event type.
- A successful save creates exactly one journal event.
- The default time is now in the pet's selected timezone; the owner can backdate it and sees the selected date/time clearly before saving.
- Successful saves provide confirmation and a brief undo action.
- A queued offline save is visibly marked pending until synchronised.

This is the default workflow for isolated events.

### Routine entry

**Purpose:** Record a complete diabetes-care routine containing several related events.

Typical morning routine: glucose measurement, insulin injection, feeding, optional medication, and optional note.

Requirements:

- Optimised for the most common diabetes-care workflow.
- Presented as a dedicated card-based form with relevant sections shown together.
- Saving the routine creates multiple individual journal events.
- Each created event appears independently in the journal.
- Event timestamps may be shared or individually adjusted.
- The form is fast and usable for real-time logging on desktop and mobile.

### Batch entry

**Purpose:** Enter historical data from handwritten notes, glucometer memory, or other offline records. This is distinct from spreadsheet import, which is handled by the Import feature.

Requirements:

- Implemented as a dedicated page, not a dialog.
- Optimised primarily for desktop usage.
- Uses a spreadsheet-like, keyboard-first interface.
- Supports efficient `Tab` and `Enter` navigation.
- Supports adding, removing, and editing rows before saving.
- Validates all rows before submission without discarding entered data.
- Saves all rows in one user action.
- Supports shared/default values, such as a default date or default event type for newly created rows.
- Is designed for entering dozens of records quickly.

This workflow replaces the manual spreadsheet workflow many users currently rely on.

### Editing existing events

Every journal entry can be edited.

Requirements:

- Every journal row provides an Edit action.
- Edit opens the same modal form used for Single Event creation, pre-populated with existing event data.
- After saving, the journal updates immediately.
- The user returns to the same journal view with filters, sorting, and scroll position preserved.
- One event-form component supports both Create and Edit modes, avoiding duplicated UI and validation logic.
- The application displays when an event was last updated.
- Users must not lose data because of a browser refresh while editing.

### Deleting events

Every journal entry can be deleted.

Requirements:

- Every journal row provides a Delete action.
- Deletion requires confirmation or provides a short undo period.
- Deleting removes only the selected journal event.
- Deletion uses a recoverable/soft-delete approach until the data-retention policy is established.

### Medication flow

The first medication administration may be recorded by entering a free-text name and a dose.

- The owner can optionally record a dose unit and notes.
- Once saved, the medication becomes a reusable medication option for that pet.
- On later entries, the owner can select a known medication from a dropdown or enter a new free-text medication.
- Selecting a known medication may prefill its most recently used dose and unit, but the user must confirm or change them before saving.
- Medication names are pet-scoped; a medication used by one pet must not automatically appear for another pet.
- Renaming a reusable medication must not rewrite the original text on past administrations without an explicit historical-edit decision.

### Weight flow

- Initial weight is requested during pet setup but can be skipped if unknown.
- Every later weight update creates a weight event with a measurement time and unit.
- The Journal and any basic Reports view show the latest active weight and when it was measured where relevant.
- Weight values must not be silently converted or mixed with a different display unit in charts or exports.

### Glucose and insulin flow

- A glucose record requires either a numeric value or the special glucometer result `Hi`, plus unit and measurement time. `Hi` is stored and displayed as a distinct above-measurable-range result, never as an invented numeric value.
- An insulin record requires insulin name, numeric dose, dose unit, and administration status.
- Status must support at least given, skipped, and uncertain. A skipped dose requires an optional reason and must not be shown as a zero-dose administration.
- The MVP records owner-entered facts only; it never calculates or recommends a dose.

### Import

CSV import accepts one documented Sweet Paws CSV format and follows this flow: select pet, download/view template if needed, upload, complete-file validation, error review, confirmation, import, and result.

- The import screen provides a downloadable template, a small format example, and brief explanations of accepted values.
- The importer must show the selected pet timezone before importing local CSV timestamps.
- It must report valid, invalid, and duplicate candidate rows.
- Invalid rows must not be silently imported; errors identify row number and affected field.
- Imported events carry source metadata linking them to their import batch.

### Export

- Export opens as a short-lived modal dialog rather than a dedicated page.
- CSV export supports the full journal or selected event types and date range.
- JSON backup export includes the data necessary for future restore/import and a format version.
- Export files state their timezone, units, filters, and generation time.

## Non-functional requirements

- Mobile-first responsive interface, usable on a small phone and a desktop browser.
- Keyboard-accessible controls, visible focus states, adequate colour contrast, and text alternatives for icons.
- The app must be usable in English and Ukrainian; no date, time, or unit format may be hardcoded in UI text.
- The app must show its online/offline and pending-sync state in a comprehensible way.
- Sensitive data must be accessible only to authorised users through backend authentication and authorisation.
- Errors must be logged for diagnosis without exposing private journal content in user-visible messages.
- The application should be designed so that a typical household (one or a few pets shared by a small number of users, such as family members and a veterinarian) can use it comfortably within relevant MongoDB Atlas and Render free-tier limits under normal usage patterns. This is a design objective, not a guarantee about provider pricing or quotas.

## Acceptance scenarios

### Morning routine

Given a pet has been selected, an owner can record a glucose value, insulin administration, and feeding in sequence. The three entries appear in time order in the same daily journal.

### New medication

Given no saved medication exists for a pet, an owner enters `Gabapentin`, a dose, and a dose unit. On the next medication entry for that pet, `Gabapentin` is available in the medication selector.

### Weight update

Given a pet has an initial weight, an owner records a new weight. The Journal displays the update at the chosen time and any latest-weight summary updates where shown.

### Timezone-confirmed import

Given a CSV has local timestamps without timezone information, the importer shows the assumed timezone and requires explicit confirmation before converting those values to stored UTC instants.

### Offline entry

Given the device is offline and local storage is available, an owner saves an event. The UI marks it pending and synchronises it when connectivity returns; it does not show it as definitely synced before that occurs.
