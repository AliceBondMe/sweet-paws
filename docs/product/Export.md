# Export and Backup

## Purpose

Exports give users control of their pet data and produce useful records for their own review or veterinary discussions. Export data is generated from domain models through repositories, not from an unfiltered raw database dump.

## MVP scope

The MVP supports:

- CSV export of the journal, with selected event types and date range.
- Complete versioned JSON backup export for the signed-in user's accessible data.

Excel and PDF exports are deferred. PDF generation may require optional server-side processing if it cannot be safely handled in the client.

## CSV journal export

CSV is a human-usable and spreadsheet-compatible export. The user selects a pet, date range, and event-type filters. The file includes enough common and type-specific columns to preserve the record's meaning.

Every CSV export includes:

- Pet identity/name as appropriate for the file format.
- Event type and occurrence timestamp.
- Timezone used for formatted timestamps.
- Original entered measurement values and units.
- Applied date range and event-type filters.
- Generation timestamp in UTC.

The format must clearly distinguish absent values from zero values, especially for skipped insulin.

## JSON backup export

JSON backup is intended for portability and recovery, not casual manual editing. It contains a format version and domain-oriented data needed to interpret and restore the user's records, including:

- Pets and their profile/unit/timezone settings.
- Journal events, including source values/units and timestamp context.
- Reusable medication definitions.
- Reminders and relevant user preferences.
- Necessary relationship metadata, excluding credentials or secrets.

The exact restore/import policy is a future decision. The JSON schema is versioned from its first release, and future importers must preserve or deliberately migrate older supported versions.

## Privacy and access

- Only an authorised owner may request a complete data export for a pet or their account.
- Export files are sensitive and the interface warns users to store/share them carefully.
- The MVP performs small exports client-side where appropriate. Large, protected, or generated-report exports may introduce narrow server-side processing later.
- The application does not retain export files in third-party object storage in the MVP.

## Correctness requirements

- Export must not silently convert or combine glucose/weight units.
- UTC storage, entry timezone, and display timezone remain distinguishable where relevant.
- Soft-deleted records are excluded from normal exports unless a future recovery/audit export expressly includes them.
- An export records its schema version and generation time so users and future importers can understand it.
- Export errors are explicit; a partial file is never presented as a complete backup.

## Open decisions

- JSON restore experience and conflict handling when restoring into an existing account.
- Whether users may export a selected pet versus only all account data in JSON.
- Exact CSV column specification and localisation rules.
- PDF and Excel export scope.
