# Development View

Code organization: modules, packages, dependencies, and build artifacts.

## Code organization

```
project/
├── backend/
├── frontend/
├── infra/
└── docs/
```

## Modules

| Module | Responsibility | Dependencies |
| --- | --- | --- |
| _(name)_ | _(contents)_ | _(modules)_ |

## Module dependencies

```mermaid
graph LR
    A[Module A] --> B[Module B]
    B --> C[Module C]
```

## Technologies

| Layer | Technology | Version |
| --- | --- | --- |
| _(backend)_ | _(framework)_ | _(x.y)_ |
| _(frontend)_ | _(framework)_ | _(x.y)_ |
| _(database)_ | _(engine)_ | _(x.y)_ |

## Conventions

- **Branching:** _(git flow, trunk-based)_
- **Versioning:** _(SemVer, etc.)_
- **Testing:** _(unit, integration, e2e)_
- **CI/CD:** _(pipeline)_
