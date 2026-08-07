# Component Inventory

## Purpose

This is a planning inventory, not a requirement to build a design-system package upfront. Components are grouped by responsibility so pages can share consistent behaviour without prematurely abstracting one-off layouts.

## Reuse rules

- Reuse stable behaviour and semantics, not merely similar markup.
- Keep feature-specific composition close to the feature.
- Do not make generic components aware of Firestore, repositories, or event persistence.
- Create/Edit event forms share one event-form component and validation contract.
- A component becomes shared only after at least two real consumers or a clear cross-cutting accessibility need.

## Application-shell components

| Component | Responsibility | Consumers |
| --- | --- | --- |
| `AppShell` | Authenticated layout, responsive navigation regions, global status. | All authenticated pages. |
| `PrimaryNavigation` | Mobile/desktop navigation and current-route state. | `AppShell`. |
| `PetSwitcher` | Display/select current pet and start add-pet flow. | `AppShell` header. |
| `ConnectivityStatus` | Global online/offline and pending-sync summary. | `AppShell`. |
| `PageHeader` | Consistent title, context, back/action area. | Most pages. |
| `FloatingAddAction` | Opens Single Event for current pet. | Mobile pet workspace. |

## Journal components

| Component | Responsibility | Notes |
| --- | --- | --- |
| `JournalTimeline` | Pagination, day grouping, oldest-first ordering, latest initial position. | Journal. |
| `JournalDayGroup` | Semantic local-day heading and its event list. | Inside `JournalTimeline`. |
| `JournalEventRow` | Accessible summary, status, sync state, Edit/Delete actions. | Timeline. |
| `EventSummary` | Event-type-aware value/product/unit rendering. | Rows, routine result, Reports details. |
| `JournalFilters` | Date range, event types, search, clear state. | Journal. |
| `JournalEmptyState` | No-history/no-results guidance. | Journal. |
| `GlucoseResult` | Render numeric glucose or distinct `Hi` result. | Event row, charts/tooltips, forms. |

## Event-entry components

| Component | Responsibility | Notes |
| --- | --- | --- |
| `EventFormDialog` | Create/Edit modal shell, event-type selection, shared submit/cancel/dirty logic. | Single Event and Edit Event. |
| `EventTypeFields` | Selects type-specific field section. | `EventFormDialog`; may be composed in Routine Entry. |
| `GlucoseFields` | Numeric or `Hi` entry, unit, measurement context. | Event form, Routine Entry, Batch row editor. |
| `InsulinFields` | Product, dose, unit, status, skip/uncertainty reason. | Event form, Routine Entry, Batch row editor. |
| `FeedingFields` | Food description and optional quantity/unit. | Event form, Routine Entry, Batch row editor. |
| `MedicationFields` | Known medication selector/free text, dose and unit. | Event form, Routine Entry, Batch row editor. |
| `WeightFields` | Weight and unit. | Event form, Batch row editor, onboarding. |
| `NoteFields` | Free-text note. | Event form, Routine Entry, Batch row editor. |
| `OccurrenceDateTimeFields` | Pet-timezone date/time with clear backdating support. | All entry workflows. |
| `RoutineEntryForm` | Card composition, shared time, per-card override, one routine submission. | Routine Entry page only. |
| `BatchEntryGrid` | Keyboard-first rows, defaults, validation and grid focus management. | Batch Entry page only. |
| `BatchEntryRow` | Event-specific editable row. | `BatchEntryGrid`. |

## Data-management components

| Component | Responsibility | Consumers |
| --- | --- | --- |
| `CsvTemplateDownload` | Download canonical template and show format help. | Import. |
| `CsvFormatExample` | Compact accessible expected-format example. | Import. |
| `ImportValidationResults` | Row/field errors, duplicate candidates, summary. | Import. |
| `ExportDialog` | CSV/JSON options, sensitive-data guidance, and download success/failure state. | Journal/global action. |

## Shared interaction components

| Component | Responsibility |
| --- | --- |
| `ConfirmDialog` | Destructive/leave-with-unsaved-work confirmation. |
| `UndoToast` | Short, accurate undo for supported operations. |
| `SyncStateBadge` | Pending/synchronised/failed state with accessible label. |
| `InlineFieldError` | Field-level validation message and association. |
| `EmptyState` | Reusable heading, explanation, and action layout. |
| `ErrorState` | Recoverable page/action error presentation. |
| `LoadingState` | Accessible loading layout/skeleton where useful. |

## Chart components

| Component | Responsibility |
| --- | --- |
| `GlucoseTrendChart` | Date-bounded glucose trend with unit labels. |
| `GlucoseChartPoint` | Tooltip/accessible representation of numeric or `Hi` reading. |

Chart implementation must preserve missing data and display `Hi` as a non-numeric high/out-of-range result, never as a guessed point.

## Deliberately not shared initially

- A universal “data table” for both Journal and Batch Entry.
- A generic form builder.
- A large internal design-system package.
- Firebase-aware UI components.

These would hide meaningful differences between medical timeline review, real-time routine entry, and desktop historical transcription.
