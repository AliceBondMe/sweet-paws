# Technical Decisions

## Purpose

This document records active architectural decisions and their rationale. It is intentionally concise; implementation details belong in the relevant architecture documents. Decisions that change later are superseded in `DecisionLog.md` rather than silently rewritten.

| ID | Decision | Status | Rationale |
| --- | --- | --- | --- |
| TD-001 | Use React 19, TypeScript, and Vite for the web application. | Accepted | Modern, typed, lightweight frontend foundation. |
| TD-002 | Use React Router for application navigation. | Accepted | Provides URL-based routes, protected route composition, and familiar React integration. |
| TD-003 | Use CSS Modules and modern CSS rather than Sass or CSS-in-JS. | Accepted | Scoped styles with low tooling complexity and native design-token support. |
| TD-004 | Use React Hook Form with Zod for forms and validation. | Accepted | Efficient forms and reusable typed event-validation rules. |
| TD-005 | Support English and Ukrainian through i18next and react-i18next. | Accepted | Makes localisation a first-release requirement instead of a later rewrite. |
| TD-006 | Use Firebase as a Backend-as-a-Service for the MVP. | Accepted | Minimises operations while supporting authentication, direct secure data access, and hosting. |
| TD-007 | Standard application operations communicate directly with Firestore through repositories. | Accepted | Avoids an unnecessary custom API while retaining a persistence boundary. |
| TD-008 | Use repository interfaces between UI/application code and Firestore. | Accepted | Keeps Firebase-specific mapping/query code out of components and supports future portability. |
| TD-009 | Do not include Cloud Functions in the core MVP architecture. | Accepted | Server-side infrastructure is introduced only for trusted, scheduled, or integration-specific work. |
| TD-010 | Use Firebase Authentication for identity and Firestore Security Rules for authorisation. | Accepted | Firebase-native security model suitable for direct client CRUD. |
| TD-011 | Store journal event instants in UTC and preserve entered IANA timezone. | Accepted | Correct chronology, import handling, daylight-saving behaviour, and cross-device display. |
| TD-012 | Use one unified journal event stream with type-specific payloads. | Accepted | Matches owners' chronological mental model and keeps filtering/export consistent. |
| TD-013 | Default journal order is oldest-first, initially positioned at the latest entries. | Accepted | Reads naturally as a timeline while opening at the most relevant data. |
| TD-014 | Provide Single Event, Routine Entry, and desktop Batch Entry workflows. | Accepted | Optimises isolated records, real-time routines, and manual historical transcription separately. |
| TD-015 | Include medications and weight in the MVP. | Accepted | Both are essential to a useful diabetic-pet journal. |
| TD-016 | Treat Firebase Storage as optional in the MVP. | Accepted | The initial journal does not require attachment storage. |
| TD-017 | Provide complete versioned JSON export for backup and portability. | Accepted | Gives users control over data and reduces practical vendor lock-in. |
| TD-018 | Avoid Redux unless a demonstrated need arises. | Accepted | Context, custom hooks, and Firestore subscriptions are adequate for the planned MVP. |

## Decision process

Before adding a significant dependency or managed service, document:

1. The product problem it solves.
2. Alternatives considered.
3. Privacy, security, operational, and cost implications.
4. Whether it changes data portability or the direct-to-Firestore MVP principle.

New decisions are added here and material changes are recorded in `DecisionLog.md`.
