# Import

## Purpose

Import lets an owner bring historical diabetic-pet records into the unified journal without manually recreating years of data. It is distinct from Batch Entry: Batch Entry transcribes a manageable set of handwritten or device records; import handles a prepared Sweet Paws CSV file.

The MVP is designed for owners migrating their own spreadsheets. It is not a universal spreadsheet importer: owners adapt their sheet to the documented Sweet Paws format before uploading it.

## MVP scope

The MVP supports CSV import only. Excel support is deferred until the canonical CSV workflow is proven successful.

An import creates normal journal events with source metadata. Imported records remain visible, editable, and exportable like manually created events while retaining their import provenance.

## Import workflow

1. Select the pet receiving the data.
2. Download the template or view the example, if needed.
3. Upload a CSV file in the canonical Sweet Paws format.
4. Validate the complete file.
5. Review row-level validation errors and possible duplicates.
6. Confirm import.
7. Create journal events.
8. Display a durable import summary.

The import screen includes a downloadable template, a small example table, and brief explanations of accepted event values and units. The application never tries to guess unknown headers, date formats, units, or event types.

## Canonical Sweet Paws CSV format

The first row must contain these headers in this exact order:

| Column | Required | Purpose |
| --- | --- | --- |
| `Date` | Yes | Local date on which the event occurred. |
| `Time` | Yes | Local time at which the event occurred. |
| `Event` | Yes | Event type. |
| `Value` | Conditional | Primary numeric measurement for glucose or weight; optional quantity for feeding. |
| `Unit` | Conditional | Unit associated with `Value`. |
| `Product` | Conditional | Insulin name, medication name, or food description. |
| `Dose` | Conditional | Numeric insulin or medication dose. |
| `Dose Unit` | Conditional | Unit associated with `Dose`. |
| `Status` | Conditional | Insulin-administration status. |
| `Notes` | No | Free-text context or note text. |

`Date`, `Time`, and `Event` are required on every row. Cells not relevant to the row's event type must be empty.

### Accepted values

| Event | Required fields | Allowed/expected values |
| --- | --- | --- |
| `glucose` | `Value`, `Unit` | Unit: `mmol/L` or `mg/dL`. `Value` is a non-negative number or exactly `Hi`. |
| `insulin` | `Product`, `Status` | `Status`: `given`, `skipped`, or `uncertain`. `Dose` and `Dose Unit` are required when status is `given`; `Value` and `Unit` are empty. |
| `feeding` | `Product` | `Value` and `Unit` are optional quantity and quantity unit. Supported quantity units: `g`, `oz`, `ml`, `cup`, `can`, `portion`. |
| `medication` | `Product`, `Dose` | `Dose Unit` is optional and is retained as entered. |
| `weight` | `Value`, `Unit` | Unit: `kg` or `lb`. |
| `note` | `Notes` | All measurement/product/dose fields are empty. |

`Event` and insulin `Status` values are lowercase and must match the table exactly. Numeric values use a period (`.`) as the decimal separator. For glucose, the special value is exactly `Hi` with capital `H` and lowercase `i`; it is not converted to a number. Negative values, blank required values, and unexpected values in conditionally empty columns are invalid.

### Date, time, and timezone

The canonical date format is `YYYY-MM-DD` and the canonical time format is 24-hour `HH:mm` (seconds are not supported in the MVP). Examples: `2026-08-07` and `08:05`.

The CSV contains local date/time values without timezone data. They are interpreted in the selected pet's home IANA timezone. The import review displays that timezone before the owner confirms import. If the pet has no valid home timezone, importing is blocked until the owner configures one.

The importer rejects impossible dates/times and local times that are invalid or ambiguous because of daylight-saving transitions. Timestamp storage and display follow `../domain/TimeAndUnits.md`.

### Example

```csv
Date,Time,Event,Value,Unit,Product,Dose,Dose Unit,Status,Notes
2026-08-01,07:55,glucose,8.2,mmol/L,,,,Before breakfast
2026-08-01,08:00,insulin,,,Lantus,1.5,U,given,
2026-08-01,08:02,feeding,28,g,Chicken wet food,,,,
2026-08-01,12:00,glucose,Hi,mmol/L,,,,Meter reported above range
2026-08-01,20:00,medication,,,Gabapentin,50,mg,,
2026-08-02,09:00,weight,4.8,kg,,,,,
2026-08-02,09:01,note,,,,,,Appetite was normal
```

The downloadable template contains the exact header row and a separate example/help view explains these rows. The template itself contains no pet data.

## Supported data

The canonical format supports glucose, insulin, feeding, medication, weight, and note events in one file. Each valid row is converted to the matching event schema defined in `../domain/EventSchemas.md`. Imports preserve source values and units where applicable.

## Validation and errors

- Validation happens before final submission and does not discard the uploaded file or the owner's import-review state.
- Errors identify the row number, affected column, and reason: missing required value, invalid event type, invalid date/time, invalid unit, invalid numeric value, or incompatible value for the event type.
- Invalid rows are not imported; the owner corrects the CSV and uploads it again. They are never silently coerced into a different clinical meaning.
- The preview shows row numbers and clear field-specific errors.
- The final result reports created, skipped-as-duplicate, invalid, and failed rows separately.
- If an import cannot complete atomically because of service limits, progress and final per-row outcome remain truthful and resumable; the app never reports success for unsaved rows.

## Duplicate handling

Duplicate detection is advisory and transparent. It may compare a pet, event type, timestamp, primary value, unit, and source metadata, but it must not silently discard a record merely because it resembles an existing event.

The user can review potential duplicates before import. Every created event stores its `importBatchId`, and the import batch records the selected source assumptions and result summary.

## Template and import help

The import page provides a downloadable canonical CSV template and a concise example/help panel. It explains the required headers, event values, date/time syntax, and supported units. This lets a non-technical owner follow the intended workflow: download or view the template, adjust their spreadsheet to match it, upload, fix any row-level errors, and import.

The MVP does not convert units during import. The uploaded unit is preserved only when it is valid for that event type.

## Privacy and file handling

The uploaded file is processed only for the import workflow. The MVP does not retain the original source file unless a future, explicitly documented storage need is approved. Import metadata stores only what is needed for audit, duplicate handling, and user-visible results.

## Open decisions

- CSV size limit and whether large files require optional server-side processing.
- User-facing rollback/reversal of a completed import batch.
- Exact timing and scope of Excel support.

## Future enhancements (out of scope)

- Arbitrary column mapping.
- Saved mapping profiles.
- Importing unknown spreadsheet layouts.
- Excel (`.xlsx`) support.
- Automatic header recognition.
- Advanced import transformations.
