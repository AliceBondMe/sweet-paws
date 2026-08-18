# Sweet Paws Documentation

This documentation is organised by the question each document answers. It remains complete here even though the Node.js/Express backend source lives in a separate companion repository.

## Product

Product scope, user workflows, and feature behaviour.

- `product/MVPDefinition.md` — first-release scope and boundaries.
- `product/ProductRequirements.md` — functional and non-functional requirements.
- `product/InformationArchitecture.md` — navigation, routes, and page responsibilities.
- `product/UserFlows.md` — critical end-to-end user journeys.
- `product/NavigationAndInteractionModel.md` — persistent pages, modal workflows, and global UI.
- `product/RoutingAndNavigation.md` — route groups and navigation/return-state rules.
- `product/ComponentInventory.md` — planned reusable UI components.
- `product/Import.md` and `product/Export.md` — historical-data portability workflows.
- `product/Notifications.md` — reminder and notification behaviour.

## Domain

The product's durable medical-journal concepts and rules.

- `domain/EventSchemas.md` — shared event contract and type-specific payloads.
- `domain/TimeAndUnits.md` — timezone, timestamp, and measurement-unit policy.

## Architecture

System boundaries, persistence, security, and synchronisation.

- `architecture/BackendArchitecture.md` — REST API, backend layers, repositories, and hosting.
- `architecture/MongoDB.md` — collection, query, and persistence principles.
- `architecture/PermissionsAndSharing.md` — ownership and future sharing model.
- `architecture/OfflineAndSync.md` — offline and sync contract.

## Engineering

Technology decisions and development conventions.

- `engineering/TechStack.md` — selected technologies and responsibilities.
- `engineering/TechDecisions.md` — active technical decisions.
- `engineering/DecisionLog.md` — decision history.
- `engineering/CodingStandards.md` — code, test, and documentation standards.
- `engineering/RepositoryStructure.md` — planned React/Express/MongoDB source layout.
