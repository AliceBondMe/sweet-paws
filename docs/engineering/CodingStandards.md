# Coding Standards

## Purpose

These standards keep Sweet Paws understandable, safe to change, and consistent as the product grows. They apply to frontend/backend code, tests, deployment configuration, and documentation.

## Language and formatting

- Write application code in TypeScript with strict compiler settings.
- Use Prettier for formatting and ESLint for static analysis; do not manually fight their output.
- Use English for code, identifiers, comments, commit messages, and technical documentation. User-facing text is translated through i18next.
- Prefer clear names over abbreviations. Avoid unexplained acronyms in domain code.

## Components and UI

- Prefer small, focused React components with explicit props.
- Components render and coordinate interaction; they do not contain scattered REST calls.
- Use custom hooks for reusable UI/data behaviour and frontend API repositories for persistence.
- Create/Edit event forms share one event-form component and shared validation logic, as required by `../product/ProductRequirements.md`.
- Design mobile-first and verify keyboard navigation, focus order, and screen-reader labels.
- Use CSS Modules for component styles and CSS custom properties for reusable tokens. Do not introduce global styles except for base, token, and theme concerns.

## Domain and persistence

- Domain types and Zod schemas are the source of truth for journal-event shapes.
- Preserve source values, units, UTC instants, and timezone context; do not replace clinical history with display conversions.
- Repository interfaces use domain-oriented inputs and outputs. HTTP and MongoDB document mapping remain inside their respective infrastructure implementations.
- Backend authorisation is mandatory for access control; client checks are never treated as authorisation.
- Every new database query documents its intended bounds and required index/test coverage.

## Error handling

- Present errors in plain language with an actionable next step when possible.
- Preserve form values after validation or transient network failure.
- Never silently discard journal data, silently change units, or fabricate missing medical values.
- Log diagnosable technical context without exposing private medical data in user-visible errors or unsafe telemetry.

## Testing

- Test observable behaviour rather than component implementation details.
- Add or update tests for every meaningful change in event validation, time/unit handling, repositories, or security rules.
- Include edge cases for DST, timezone parsing, unit labels/conversion, the non-numeric glucose result `Hi`, skipped insulin, offline/pending writes, and duplicate retries when relevant.
- Use isolated MongoDB test data and API integration tests for repository/authorisation coverage.

## Dependencies and architecture changes

- Prefer native platform and existing stack capabilities before adding a package.
- Any significant dependency, managed service, or new infrastructure needs a documented problem statement, security/privacy/cost assessment, and entry in `TechDecisions.md`.
- Do not add a separate service merely to proxy ordinary backend API work.
- Avoid Redux unless a concrete, documented application-state problem warrants it.

## Documentation

- Update affected documentation in the same change as a material product or architecture decision.
- Record material decisions in `DecisionLog.md`; keep `TechDecisions.md` current.
- Link requirements to domain contracts when a change affects stored data, timezone handling, import/export, or permissions.
