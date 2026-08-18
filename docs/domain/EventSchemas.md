# Sweet Paws Event Schemas

## Status

This is the canonical domain contract for the MVP. Field names are conceptual rather than implementation code; database document structure may refine storage and indexing without changing their meaning.

## Shared event envelope

Every active journal entry has the following common information:

| Field | Meaning |
| --- | --- |
| `id` | Stable event identifier. |
| `petId` | Pet to which the event belongs. |
| `eventType` | `glucose`, `insulin`, `feeding`, `medication`, `weight`, or `note`. |
| `occurredAtUtc` | The instant the care occurred, stored in UTC. |
| `enteredTimezone` | IANA timezone used when the time was entered or imported, for example `Europe/Kyiv`. |
| `enteredLocalDateTime` | Original local date/time text or normalised local representation when preserving it aids audit/import review. |
| `source` | `web`, `pwa-offline`, `import`, or future channels such as `telegram`. |
| `note` | Optional free-text contextual note. |
| `createdAt`, `updatedAt` | Server-managed audit timestamps. |
| `createdBy` | User identifier of the creator. |
| `isDeleted`, `deletedAt`, `deletedBy` | Soft-delete state. |
| `importBatchId` | Present only for imported events. |
| `idempotencyKey` | Optional client/source key used to avoid duplicate retries. |

`occurredAtUtc` is the timeline's authoritative sort value. The user interface displays it in the relevant selected timezone; it does not treat a formatted display string as the stored source of truth.

## Pet profile

A pet has a species, home timezone, display preferences, and lifecycle state. Relevant fields include:

- `id`, `name`, `species` (`cat` or `dog`), optional photo and birth date.
- `homeTimezone` as an IANA timezone.
- `glucoseUnitPreference` (`mmol/L` or `mg/dL`).
- `weightUnitPreference` (`kg` or `lb`).
- `status` (`active` or `archived`).
- Initial weight is stored as a normal `weight` event created during setup, not only as a mutable profile field.

## Event payloads

### Glucose

Required:

- `resultKind`: `numeric` or `hi`.
- `unit` (`mmol/L` or `mg/dL`).

Required when `resultKind` is `numeric`:

- Numeric `value`.

When `resultKind` is `hi`, `value` is absent and the journal displays `Hi`. `Hi` records the glucometer's above-measurable-range result; it is not converted into an invented numeric value.

Optional MVP context:

- `measurementContext`, such as before insulin, after meal, curve, or other.
- `deviceName`.

The original entered unit is retained. Conversion for display or charts, if enabled, must be explicit and must not replace the original measurement. Charts and summaries must not silently substitute a numeric value for `Hi`; they display it as a distinct high/out-of-range observation according to the final chart design.

### Insulin

Required:

- `insulinName`.
- `status`: `given`, `skipped`, or `uncertain`.

Required when status is `given`:

- Numeric `dose`.
- `doseUnit` (normally `U`, but treated as a displayed/validated unit rather than an implicit assumption).

Optional:

- `skipReason` or `uncertaintyReason`.
- Injection-site details and regimen reference in future releases.

### Feeding

Required:

- `foodDescription`.

Optional:

- Numeric quantity and `quantityUnit`.

Food products and meal templates are deferred. The MVP preserves free-text descriptions rather than enforcing a catalogue.

### Medication

Required:

- `medicationNameSnapshot`: exact name shown at the time of administration.
- Numeric `dose`.

Optional:

- `doseUnit`.
- `medicationDefinitionId` pointing to a reusable, pet-scoped medication option.

A reusable medication definition contains a display name and optional last-used/default dose information. Historical administration events keep their name snapshot even if the definition is later renamed or archived.

### Weight

Required:

- Numeric `value`.
- `unit` (`kg` or `lb`).

The measurement time uses the shared event timestamp. A weight event is never overwritten merely because it is no longer the current weight.

### Note

Required:

- `text`.

Notes may carry the shared optional `note` field only when it has a distinct purpose; otherwise the primary note text is `text`.

## Event validation principles

- Values are validated according to event type, unit, and sensible numeric precision.
- Validation must reject invalid values without inventing a clinical interpretation.
- Unknown historical values from import may be retained only when clearly marked and when they do not break the event schema; otherwise they appear as validation errors for user resolution.
- A created event is immutable in identity. Edits modify documented fields and update `updatedAt`; deletion uses the shared soft-delete fields.

## Medication definitions

Medication definitions are pet-scoped convenience data, not medical prescriptions.

| Field | Meaning |
| --- | --- |
| `id` | Stable definition identifier. |
| `petId` | Pet to which the dropdown option belongs. |
| `displayName` | Current name in the dropdown. |
| `lastDose` / `lastDoseUnit` | Optional convenience defaults. |
| `isArchived` | Hides an option from normal selection without changing history. |
| `createdAt`, `updatedAt` | Audit timestamps. |

## Future-compatible fields

Future event types, such as ketones or Libre/CGM observations, must use the same shared envelope. New data should be added by versioned event payload rather than overloading existing glucose records with incompatible meanings.
