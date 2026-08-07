# User Flows

## Purpose

These flows define the expected behaviour of the most important Sweet Paws journeys. They are product acceptance references for later page specifications, design, and tests.

## 1. First pet setup

**Goal:** Start using the app with a correctly configured pet context.

1. A newly authenticated user without pets is directed to first-pet onboarding.
2. The user enters pet name and species.
3. The app suggests the device timezone; the user reviews and confirms or changes the pet's home IANA timezone.
4. The user selects glucose and weight display units.
5. The user may enter initial weight and its measurement time, or skip it if unknown.
6. The app creates the pet and, when supplied, the initial weight as a normal journal event.
7. The user arrives at that pet's Journal with clear next actions.

**Success:** The owner can record care without ambiguous timezone or unit defaults.

## 2. Record a morning routine

**Goal:** Record related care quickly while it happens.

1. From Journal, the owner chooses Routine Entry.
2. The card-based form presents glucose, insulin, feeding, and optional medication/note sections together.
3. The owner enters relevant values; the routine time defaults to now in the selected pet's home timezone.
4. The owner can adjust the shared time or an individual event time.
5. The app validates all included event sections.
6. The owner saves once.
7. The app creates separate journal events and returns to the originating view with confirmation/undo.
8. If offline, entries are visibly pending until synchronised.

**Success:** The Journal shows each event independently and in chronological order; no care event is inferred merely from opening the form.

## 3. Record one isolated or forgotten event

**Goal:** Add a single event with minimal friction.

1. The owner taps the floating `+` action.
2. They select the event type.
3. The Single Event form shows only relevant fields.
4. The time defaults to now, or the owner selects a past time.
5. The owner saves exactly one event.
6. The app confirms save and offers a short undo period; failed/offline state is shown accurately.

**Success:** A glucose reading, insulin action, food, medication, weight, or note can be recorded without navigating through unrelated fields.

## 4. Add a new medication, then reuse it

**Goal:** Avoid repeated typing without altering medication history.

1. The owner opens a medication section in Single Event or Routine Entry.
2. For a new medication, they enter a free-text name, dose, and optional dose unit/note.
3. On save, the administration becomes a journal event and a pet-scoped reusable medication option is created or updated.
4. On a later entry, the owner chooses the medication from the dropdown.
5. The app may prefill prior dose/unit but the owner reviews it before saving.

**Success:** Historical events retain the original medication-name snapshot if the reusable option is later renamed or archived.

## 5. Review and correct the journal

**Goal:** Find an event, correct it safely, and preserve context.

1. The owner opens the selected pet's Journal.
2. The journal displays oldest-first and starts at the latest entries.
3. The owner filters by event type/date range or searches relevant free text.
4. They select Edit for a journal row.
5. The shared Single Event form opens in Edit mode with the existing values.
6. After save, the journal updates immediately and restores filter, ordering, and scroll position.
7. To remove an entry, the owner selects Delete and confirms or uses the short undo period.

**Success:** Editing/deleting one event does not alter unrelated events or lose the owner's location in the journal.

## 6. Transcribe historical records

**Goal:** Enter many records from handwritten notes or device memory efficiently.

1. From Journal/data management, the owner opens Batch Entry on a desktop device.
2. They optionally set defaults such as date or event type.
3. They add/edit rows using keyboard-first `Tab` and `Enter` navigation.
4. They remove incorrect rows before submission.
5. The app validates all rows and points to any error without discarding data.
6. The owner saves the rows in one action.
7. The app reports accurate saved/pending/failed outcome and the events appear individually in the Journal.

**Success:** The owner can transcribe dozens of records faster than creating isolated events one by one. This does not replace CSV import.

## 7. Import a historical CSV

**Goal:** Import structured past data without silently corrupting time, units, or event meaning.

1. The owner opens Import for the selected pet and chooses a CSV file.
2. They download/view the canonical template if needed, then upload a CSV that follows it.
3. The app validates required headers, event values, date/time syntax, units, and numeric values using the selected pet's home timezone.
4. The app shows valid, invalid, and possible duplicate rows with row-level errors.
5. The owner corrects their CSV if needed, re-uploads it, and confirms import.
6. The app creates normal journal events with import provenance and shows a durable result summary.

**Success:** Ambiguous local timestamps are not converted to UTC without user confirmation.

## 8. Export data and create a backup

**Goal:** Keep an owner in control of their data.

1. The owner opens the Export modal from Journal.
2. For CSV, they select pet, date range, and event types.
3. For JSON backup, they review that the export contains complete account/pet data and is sensitive.
4. The app generates the file or clearly reports an error.
5. The owner saves the file outside the application.

**Success:** The exported data preserves source units, timezone context, and schema version where needed.

## 9. Create and respond to a reminder

**Goal:** Plan care without treating a prompt as evidence of care.

1. The owner opens Reminders and creates a one-time or recurring reminder in the pet's home timezone.
2. The Reminders page displays the next occurrence; a future Journal summary may surface it without replacing reminder management.
3. If the owner opts in and the platform supports it, the app may request notification permission.
4. When prompted, the owner can open an appropriate logging workflow.
5. Recording care creates a normal journal event; dismissing a reminder does not create one.

**Success:** Reminder status and journal evidence remain distinct.
