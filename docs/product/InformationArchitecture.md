# Information Architecture

## Purpose

This document defines how owners move through Sweet Paws. Navigation should make the pet's journal the centre of the application while keeping frequent recording actions within easy reach.

## Principles

- A selected pet provides the context for all daily care views.
- The Journal is the primary record; the Dashboard is a summary and launch point.
- Frequent actions are reachable in one or two interactions on a phone.
- Complex, occasional workflows such as imports and historical Batch Entry have dedicated pages.
- Navigation remains understandable when an account has one pet, while scaling cleanly to multiple pets later.

## Application areas

| Area | Purpose | Primary audience/use frequency |
| --- | --- | --- |
| Authentication | Sign in, create account, recover access. | Before normal use. |
| Onboarding | Create the first pet and establish units/timezone. | First use or new pet. |
| Pet context | Select, create, edit, or archive a pet. | Occasional; selection is persistent. |
| Dashboard | See current summary, next reminder, and today's care. | Frequent overview. |
| Journal | Review, filter, search, edit, and delete the complete timeline. | Core daily history view. |
| Single Event | Record one isolated or forgotten event. | Frequent quick action. |
| Routine Entry | Record a live diabetes-care routine. | Frequent, typically morning/evening. |
| Batch Entry | Transcribe many historical records efficiently. | Occasional desktop task. |
| Reminders | Manage planned-care reminders. | Occasional setup/review. |
| Import / Export | Bring in history and create backups/reports. | Occasional data-management task. |
| Settings | Manage account, locale, theme, and pet-specific preferences. | Occasional. |

## Navigation model

### Global navigation

The authenticated application has a persistent, responsive navigation shell:

- **Mobile:** a compact bottom navigation for Dashboard, Journal, and more/settings; a floating `+` action is always available in a selected pet context.
- **Desktop:** a sidebar or top navigation exposing Dashboard, Journal, Routine Entry, Reminders, Import/Export, and Settings.
- **Pet switcher:** always visible in the authenticated shell when the user has one or more pets. It clearly identifies the currently selected pet.

The exact visual pattern may evolve during design, but every route must make current pet context obvious before allowing a pet-scoped write.

### Primary paths

```text
Sign in
  └─ No pets → First-pet onboarding → Dashboard
  └─ Has pets → Last selected pet's Dashboard

Dashboard ↔ Journal
    ├─ Floating + → Single Event
    ├─ Routine Entry
    ├─ Batch Entry
    ├─ Reminders
    └─ Import / Export
```

Single Event and Routine Entry return the owner to the originating context after a successful save. Editing returns the owner to the same Journal view with filters, ordering, and scroll position preserved.

## Route responsibilities

Routes below are conceptual and do not prescribe exact URL strings.

| Route/page | Required context | Main responsibilities |
| --- | --- | --- |
| Sign in | None | Authenticate and guide a new user to account setup. |
| First-pet onboarding | Authenticated user | Create pet, confirm timezone, select units, record optional initial weight. |
| Pet dashboard | Selected pet | Show summary, today's timeline, latest values, next reminder, and entry shortcuts. |
| Journal | Selected pet | Display oldest-first timeline at latest position; filter, search, paginate, edit, delete. |
| Single Event | Selected pet | Create one type-specific journal event; also serves Edit mode. |
| Routine Entry | Selected pet | Record several related real-time events in a card-based form. |
| Batch Entry | Selected pet | Enter historical records in a desktop keyboard-first grid. |
| Reminders | Selected pet | View and manage planned care reminders and permission state. |
| Import | Selected pet | Map, validate, preview, and import CSV history. |
| Export | Selected pet or account | Configure CSV export or generate a JSON backup. |
| Pet settings | Selected pet | Update profile, timezone, units, and archive state. |
| Account settings | Authenticated user | Locale, theme, account-level preferences, and sign-out. |

## Entry-point rules

- The floating `+` opens Single Event, not a generic overloaded dialog.
- Routine Entry is a named primary action on Dashboard and Journal because it is a frequent workflow.
- Batch Entry is discoverable from Journal and data-management navigation, but is not promoted as the default daily action.
- Import and Export are grouped under data management on desktop and the More/settings area on mobile.
- A user can change selected pet before starting any entry flow; changing it while a form has unsaved work requires confirmation.

## Page state expectations

Each page specification must cover:

- Loading, empty, error, and offline/stale states.
- Permission/authorisation failure state without revealing another pet's data.
- No-pet state and archived-pet behaviour.
- Pending-sync state for unsynchronised journal writes.
- Mobile and desktop behaviour, particularly Batch Entry's desktop-first constraints.

## Deferred areas

- Caregiver/veterinarian sharing pages.
- Veterinary report pages and PDF exports.
- Telegram surfaces.
- AI insights and alerts.

These must fit into the existing selected-pet context rather than becoming alternate journal models.
