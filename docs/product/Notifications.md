# Notifications and Reminders

## Purpose

Reminders help an owner remember planned care. They are not evidence that insulin, medication, food, or any other care was actually given. Actual care is recorded as a separate journal event.

## MVP scope

The MVP stores reminders and shows the next reminder in the dashboard and relevant pet views. A reminder supports a title, optional care category, scheduled time, recurrence or one-time schedule, pet timezone, active state, and user-facing status.

The product may request browser notification permission only after explaining its purpose and receiving a user action. Notification permission must never be assumed.

## Delivery model

Reliable delivery while the PWA is closed, the device is asleep, or the browser restricts background work cannot be guaranteed by client-side code alone.

Therefore:

- In-app reminder display is part of the direct-to-Firestore MVP.
- Browser/device notifications are an enhancement dependent on platform permissions and capabilities.
- Reliable scheduled push delivery requires trusted scheduled infrastructure, such as a narrowly scoped Cloud Function, and is introduced only if that feature is committed.
- Telegram delivery is a future optional integration and likewise requires server-side webhook/scheduling infrastructure.

The interface must never imply that a notification was delivered when the application only scheduled or attempted it.

## Reminder versus care status

A reminder may be shown as upcoming, acknowledged, dismissed, skipped, or completed according to the final UX. These states apply to the reminder instance, not automatically to the pet's medical journal.

If an owner records care from a reminder, the application creates a corresponding journal event through the normal Single Event or Routine Entry workflow. If the owner simply dismisses a reminder, no care event is fabricated.

## Timezone and recurrence

- Reminders use the pet's home IANA timezone and local wall-clock scheduling.
- The UI displays the next scheduled occurrence with its timezone.
- Daylight-saving transitions follow the rules in `../domain/TimeAndUnits.md`.
- Travel handling is deferred; the MVP retains the pet's home timezone.

## Safety and UX requirements

- Reminders are supportive prompts, not medical advice.
- Users can identify which pet and which care item a reminder concerns.
- A missed or unavailable notification must not become an alarming false claim that harm occurred.
- Notifications have a clear settings page, permission status, and a way to disable a reminder.
- The product should include appropriate wording that owners must follow veterinary guidance for treatment decisions.

## Future server-side requirements

When scheduled delivery is added, the server-side component must:

- Calculate due instances using the pet's IANA timezone.
- Avoid duplicates across retries and daylight-saving transitions.
- Keep delivery attempts and provider failures separate from care-completion records.
- Protect notification credentials and external integration secrets.
- Respect opt-in, revocation, and account/pet access changes.

## Open decisions

- Which reminder categories and recurrence patterns ship first.
- Whether acknowledgement/dismissal history is visible in the journal or only reminder history.
- Supported notification channels and browser/device support policy.
- Escalation, repeat-notification, and quiet-hours policies.
