# Logical View

Components, responsibilities, interfaces, and relationships, independent of deployment.

## Design principles

- Separation of concerns
- Low coupling, high cohesion

## Components

| Component | Responsibility | Depends on |
| --- | --- | --- |
| _(name)_ | _(what it does)_ | _(components)_ |

## Domain model

| Entity | Description | Key attributes |
| --- | --- | --- |
| _(entity)_ | _(description)_ | _(attributes)_ |

## Component diagram

```mermaid
graph TD
    A[Component A] --> B[Component B]
    A --> C[Component C]
```

## Patterns and decisions

- **Pattern:** _(layered, hexagonal, etc.)_ — _(rationale)_
- **Related ADRs:** _(links)_
