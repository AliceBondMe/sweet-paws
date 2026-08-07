# Information Architecture

## Purpose

This document defines how owners move through Sweet Paws. Navigation should make the pet's journal the centre of the application while keeping frequent recording actions within easy reach.

## Principles

- A selected pet provides the context for all daily care views.
- The Journal is the primary record, home destination, and launch point for daily work.
- Frequent actions are reachable in one or two interactions on a phone.
- Complex, occasional workflows such as imports and historical Batch Entry have dedicated pages.
- Navigation remains understandable when an account has one pet, while scaling cleanly to multiple pets later.

## Application areas

| Area | Purpose | Primary audience/use frequency |
| --- | --- | --- |
| Authentication | Sign in, create account, recover access. | Before normal use. |
| Onboarding | Create the first pet and establish units/timezone. | First use or new pet. |
| Pet context | Select, create, edit, or archive a pet. | Occasional; selection is persistent. |
| Journal | Review, filter, search, edit, and delete the complete timeline. | Core daily history view. |
| Single Event | Record one isolated or forgotten event. | Frequent quick action. |
| Routine Entry | Record a live diabetes-care routine. | Frequent, typically morning/evening. |
| Batch Entry | Transcribe many historical records efficiently. | Occasional desktop task. |
| Reminders | Manage planned-care reminders. | Occasional setup/review. |
| Import | Bring in history from the documented CSV format. | Occasional data-management task. |
| Reports | Review basic glucose trends and future analytics. | Occasional review. |
| Settings | Manage account, locale, theme, and pet-specific preferences. | Occasional. |

## Navigation model

### Global navigation

The authenticated application has a persistent, responsive navigation shell:

- **Mobile:** a side drawer contains Journal, Reports, Reminders, Pet Settings, and Account Settings; a floating `+` action is available in Journal context.
- **Desktop:** a header exposes selected pet, primary navigation, language/theme controls, and the account menu.
- **Pet switcher:** always visible in the authenticated shell when the user has one or more pets. It clearly identifies the currently selected pet.

The exact visual pattern may evolve during design, but every route must make current pet context obvious before allowing a pet-scoped write.

### Primary paths

```text
Sign in
  └─ No pets → First-pet onboarding → Journal
  └─ Has pets → Last selected pet's Journal

Journal
    ├─ Floating + → Single Event modal
    ├─ Routine Entry page
    ├─ Batch Entry page
    ├─ Import page
    └─ Export modal
```

Single Event/Edit and Export are modal interactions that close back to Journal context. Routine and Batch Entry return the owner to Journal after a successful save.

## Destination and surface responsibilities

Persistent pages below are conceptual and do not prescribe exact URL strings. Modal workflows are listed separately so they are not mistaken for routes.

| Persistent page | Required context | Main responsibilities |
| --- | --- | --- |
| Sign in | None | Authenticate and guide a new user to account setup. |
| First-pet onboarding | Authenticated user | Create pet, confirm timezone, select units, record optional initial weight. |
| Journal | Selected pet | Display oldest-first timeline at latest position; filter, search, paginate, edit, delete. |
| Routine Entry | Selected pet | Record several related real-time events in a card-based form. |
| Batch Entry | Selected pet | Enter historical records in a desktop keyboard-first grid. |
| Reminders | Selected pet | View and manage planned care reminders and permission state. |
| Import | Selected pet | Map, validate, preview, and import CSV history. |
| Reports | Selected pet | Basic glucose trend and future analytics. |
| Pet settings | Selected pet | Update profile, timezone, units, and archive state. |
| Account settings | Authenticated user | Locale, theme, account-level preferences, and sign-out. |

Modal surfaces: Single Event/Edit creates or changes one journal event without leaving Journal; Export configures and downloads CSV/JSON without a dedicated page.

## Entry-point rules

- The floating `+` opens Single Event, not a generic overloaded dialog.
- Routine Entry is a named primary action on Journal because it is a frequent workflow.
- Batch Entry is discoverable from Journal and data-management navigation, but is not promoted as the default daily action.
- Import and Batch Entry are available through Journal data-management actions; Export opens as a modal from Journal.
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
