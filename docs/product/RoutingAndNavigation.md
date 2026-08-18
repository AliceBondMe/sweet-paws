# Routing and Navigation

## Purpose

This document defines route ownership, navigation behaviour, and return-state rules. It refines `InformationArchitecture.md` without prescribing exact URL syntax or implementation code.

## Route groups

| Group | Pages | Access rule |
| --- | --- | --- |
| Public | Sign in, account recovery. | Unauthenticated only where appropriate. |
| Onboarding | First-pet setup. | Authenticated user with no selected active pet. |
| Pet workspace | Journal, Routine Entry, Batch Entry, Import, Reports, Reminders, Pet settings. | Authenticated and authorised for selected pet. |
| Account | Account settings. | Authenticated. |
| System | Not found, unauthorised, maintenance/fatal error when needed. | Context-dependent. |

## Selected-pet route context

Every pet-workspace route is explicitly tied to a pet identifier in navigation state/URL. The application validates membership through backend API responses; the backend independently enforces authorisation before returning pet data.

- An invalid, archived, or unauthorised pet context opens an appropriate safe state rather than a blank page.
- The selected pet is persisted as a convenience only; it is never trusted as authorisation.
- Switching pets changes the workspace context. It does not carry unsaved form data across pets.

## Main navigation behaviour

### Mobile

- A hamburger side drawer contains Journal, Reports, Reminders, Pet Settings, and Account Settings.
- A floating `+` opens the Single Event modal for the selected pet from Journal.
- Routine Entry remains a visible named action on Journal.
- Batch Entry and Import are reached through Journal data-management actions; Export opens as a modal.

### Desktop

- The header contains the selected pet switcher, primary navigation, language/theme controls, and account menu.
- Persistent navigation exposes Journal, Reports, Reminders, settings, and the relevant Routine/Batch/Import actions.
- Batch Entry is discoverable from Journal and the data-management area.
- The pet switcher remains visible independently of individual page content.

## Return-state rules

| Origin | Child workflow | Return behaviour after success |
| --- | --- | --- |
| Journal | Edit Event | Restore filters, ordering, pagination position, and scroll position. |
| Journal | Single Event/Edit modal | Close to the unchanged Journal route and show the created/updated/pending event in context where feasible. |
| Journal | Routine Entry | Return to Journal with routine result/undo state. |
| Journal | Batch Entry | Return to Journal with saved entries discoverable in the active date context. |
| Import | Import result | Offer open Journal filtered to imported time range/batch when practical. |
| Reminder prompt | Event/Routine Entry | Return to reminder context without fabricating completion. |

Browser back navigation must not cause duplicate creation after a successful save. Completed pages replace or clearly resolve transient create/edit routes so stale forms cannot be resubmitted accidentally.

## Unsaved work

- Form pages track meaningful dirty state.
- Navigating away, switching pets, closing the route, or using browser back prompts for confirmation when unsaved data would be lost.
- Pending synchronisation is not treated as unsaved form state; it has separate visible status and retry behaviour.

## Deep links and errors

- Authenticated deep links to an authorised pet page open the requested page.
- Unauthenticated deep links preserve the intended destination only when safe, then return after sign-in.
- An unauthorised pet link produces a generic access-unavailable state without confirming private data.
- Unsupported/unknown routes lead to a safe not-found page with a route back to the user's Journal or account context.

## Accessibility

- Navigation landmarks, link labels, selected-state semantics, and focus handling are required.
- Route transitions move focus to the main page heading unless preserving focus in a narrow interaction is more appropriate.
- Dialogs and sheets trap focus, announce their purpose, and return focus to their trigger on close.
