# Sweet Paws MVP Definition

## Purpose

The first release of Sweet Paws is a reliable, mobile-friendly medical journal for owners of diabetic pets. It prioritises fast, accurate recording and review of day-to-day care over advanced automation or clinical interpretation.

Sweet Paws records what an owner observed or administered. It does not diagnose a pet, prescribe treatment, or recommend insulin doses.

## Target user

The MVP is designed primarily for an owner of a diabetic cat. The domain model must support cats and dogs from the beginning, but the MVP does not need species-specific workflows beyond configurable units, labels, and vet-defined settings.

## In scope

### Account and pets

- Sign in and sign out.
- Create, view, edit, and archive pets.
- Support more than one pet per account.
- Pet profile with name, species (`cat` or `dog`), date of birth if known, photo if available, home timezone, preferred glucose unit, preferred weight unit, and initial weight.
- An initial weight is captured on the profile during setup and represented as the first weight measurement in the pet's journal.

### Unified journal

- One chronological journal for each pet.
- Event types: glucose, insulin, feeding, medication, weight, and note.
- Create, view, edit, and remove events.
- Group events by local calendar day.
- Filter by event type and date range.
- Search free-text notes and medication names.
- Show clear pending-sync state when an event was saved offline.

### Data entry

- Mobile-first quick entry from anywhere in the pet journal.
- Routine entry for recording a common diabetes-care routine in one submission.
- Desktop-oriented batch entry for manually transcribing historical records.
- Record an event at the current time or select an earlier time.
- Reuse recently used values where safe and unambiguous.
- Provide immediate undo after creating an event.
- Medication entry supports a free-text medication name and dose. After a medication has been entered, it is available for selection from that pet's medication dropdown on later entries.
- Weight can be entered at setup and as a later journal event.

### Review

- Dashboard per selected pet with latest glucose, latest insulin, latest weight, next reminder, and today's timeline.
- A basic glucose chart for a selected date range.
- Journal filters and a readable chronological view on mobile and desktop.

### Reminders

- Create recurring and one-time reminders for insulin, medication, feeding, or a custom task.
- Show upcoming reminders in the application.
- Deliver notifications only after the user has granted the required device permissions.
- Record whether a reminder was completed, skipped, or dismissed where applicable.

### Import, export, and installability

- CSV import using a documented Sweet Paws template format, with validation and timezone review.
- CSV journal export and versioned JSON backup export.
- Responsive web application that can be installed as a PWA.
- Offline access to the application shell and recently used pet data; event creation queues for sync when feasible.

### Internationalisation

- The user interface supports English and Ukrainian from the first release.
- All user-facing text is translatable; dates, times, and units respect the selected locale and pet preferences.
- The architecture supports adding languages without rewriting features.

## Explicitly out of scope for the first release

- Insulin dose recommendations or other treatment recommendations.
- Automatic diagnosis, anomaly detection, or AI-generated medical advice.
- Telegram bot.
- Caregiver invitations, roles, and vet portals.
- Excel import/export and PDF reports.
- Attachments, lab results, ketones, vomiting, appetite, water intake, Libre/CGM ingestion, and vet visits.
- Advanced chart overlays, recurring care-plan templates, and integrations with veterinary systems.

These capabilities may be added later without changing the journal's core event contract.

## Success criteria

The MVP is successful when an owner can:

1. Set up a pet in a few minutes, including its starting weight, units, and timezone.
2. Record common daily events in a few taps on a phone.
3. See a trustworthy, chronological account of a day of care.
4. Review glucose readings over time without mixing units or losing their context.
5. Import a prior CSV history without silently changing the intended dates or times.
6. Export their own data without depending on Sweet Paws for permanent access to it.

## Product guardrails

- A user must be able to distinguish scheduled care from care actually recorded as completed.
- The interface must not make a missed dose look like an administered dose.
- Important edits and deletions must not silently destroy clinical context.
- The product must state clearly that urgent concerns require a veterinarian or emergency clinic.
- The MVP favours reliable manual recording over clever automation.

## Decisions deferred

- Exact reminder delivery channels and escalation rules.
- Full offline conflict-resolution policy.
- Caregiver and veterinarian sharing model.
- Chart warning bands and pet-specific vet-configured target ranges.
- Supported locales beyond English and Ukrainian.
