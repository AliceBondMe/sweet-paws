# Sweet Paws Time and Units

## Purpose

Time and units are clinical context. This document defines how Sweet Paws preserves that context across entry, display, reminders, imports, and exports.

## Time model

### Canonical storage

Every journal event is stored as an instant in UTC in `occurredAtUtc`. UTC is used for sorting, sync, comparison, and cross-device consistency.

Every event also retains the IANA timezone in which it was entered or imported, such as `Europe/Kyiv`. When useful for import auditing, the original local date/time representation is retained as well.

The system must not store only a formatted local timestamp such as `23/07/2026 08:00`, because it is ambiguous without a timezone and date-format convention.

### Display

- By default, a pet's journal is displayed in its `homeTimezone`.
- The UI clearly identifies the timezone in settings, import confirmation, and exported reports.
- Users may later be allowed to choose another viewing timezone, but this must not change the underlying UTC instant or rewrite historical entries.
- Day grouping uses the timezone selected for the current journal view. In the MVP this is the pet's home timezone.

### Pet setup and timezone changes

- Pet setup requires the user to confirm a suggested IANA timezone.
- The suggestion may come from the device, but it must remain editable.
- Changing a pet's home timezone changes future display and reminder scheduling behaviour; it does not reinterpret existing `occurredAtUtc` values.
- Each historical event preserves its `enteredTimezone` for audit purposes.

### Backdated events

When recording a past event, the user enters a local date and time in the selected journal timezone. The application converts that local value to UTC before saving and displays the selected local date/time for confirmation.

### Daylight saving time

IANA timezones, not fixed UTC offsets, must be used.

- If a local time occurs twice during an autumn clock change, the application must request enough information to choose the intended occurrence or use an explicit offset in advanced input/import.
- If a local time does not exist during a spring clock change, the application must explain the problem and require a valid time.
- Reminder behaviour at daylight-saving transitions must be specified before reminders are released. The working default is that care schedules follow the pet's home timezone and local wall-clock time.

### Travel

For the MVP, a pet retains its home timezone while travelling. Events can still be entered in another timezone and preserve that entry timezone, while the journal default remains the home timezone. A future release may offer temporary travel timezone/schedule support.

## Import time rules

The MVP imports only the canonical Sweet Paws CSV format defined in `../product/Import.md`. It uses unambiguous `YYYY-MM-DD` dates and 24-hour `HH:mm` times; it does not infer alternate date formats or source timezones.

The selected pet's home IANA timezone is displayed during import review and used to convert each local timestamp to UTC. The importer validates daylight-saving ambiguities and invalid local times, then stores converted UTC instants with source/import metadata.

CSV files with explicit offsets or ISO 8601 timestamps are not part of the MVP canonical format.

## Reminders

Reminder schedules are not journal events. A reminder records planned care; a journal event records observed or administered care.

For MVP design, recurring reminder schedules are defined in the pet's home timezone and trigger at the intended local wall-clock time. Users must be able to see the next scheduled occurrence in that timezone.

The later notifications specification must define delivery retries, permissions, device limitations, missed notification behaviour, and what happens after timezone changes.

## Glucose units

Sweet Paws supports `mmol/L` and `mg/dL`.

- Every glucose event stores the unit actually entered or imported.
- A glucometer result of `Hi` is stored as a distinct non-numeric result with its reported unit; it is never converted to an arbitrary numeric value.
- A pet has a preferred display unit, selected during setup and changeable later.
- The application may provide an explicit display conversion, but must preserve the original value and unit.
- Charts and exports must label the unit. They must not silently combine readings that use different units.
- If conversion is offered, its formula, rounding, and labelling are documented and consistently applied.

## Weight units

Sweet Paws supports kilograms (`kg`) and pounds (`lb`).

- Every weight event stores its entered unit.
- A pet has a preferred display unit.
- Conversions are explicit; the source value and source unit remain preserved.
- Exports include the original unit and, if a converted display value is included, clearly identify it as converted.

## Dose and quantity units

Medication, insulin, and feeding quantities must always store their associated unit when a numeric value is present.

- Insulin doses must not rely on an unlabeled numeric field.
- Medication dose units remain free-text/selectable in the MVP to support varied formulations, while retaining exactly what the owner entered.
- Feeding quantity units are optional because many owners record a descriptive meal rather than a measured amount.

## Export requirements

All exports include:

- The export generation timestamp in UTC.
- Pet home timezone and the timezone used for formatted dates.
- Original units for readings and measurements.
- Selected date range and applied event-type filters.
- Format/schema version for JSON backups.

## Open decisions

- Whether chart display conversion is included in the MVP or charts only show readings already entered in one selected unit.
- Precision and rounding rules for each numeric measurement.
- Whether users can set a timezone per individual manually entered event in the MVP.
- Exact reminder behaviour when a pet is temporarily travelling.
