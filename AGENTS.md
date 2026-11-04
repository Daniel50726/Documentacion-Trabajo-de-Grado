# AGENTS.md — Agent Guide

Single source of truth for any AI agent working in this repository, regardless of model or provider.

## Project

Thesis documentation for **AnomaPulse**: a BLE wristband, mobile app, and ML backend for biometric monitoring. No production code yet; the focus is architecture and feature specifications.

## Sources (read before acting)

| Topic | Path |
| --- | --- |
| Architecture | [`architecture/README.md`](architecture/README.md) |
| System context | [`architecture/context-system/context.md`](architecture/context-system/context.md) |
| Logical view | [`architecture/logical-view/view.md`](architecture/logical-view/view.md) |
| Process view | [`architecture/process-view/view.md`](architecture/process-view/view.md) |
| Development view | [`architecture/development-view/view.md`](architecture/development-view/view.md) |
| Physical view | [`architecture/physical-view/view.md`](architecture/physical-view/view.md) |
| Scenarios | [`architecture/scenarios/view.md`](architecture/scenarios/view.md) |
| Specs index | [`specs/README.md`](specs/README.md) |
| Features | `specs/features/<NNN-slug>/` |
| Work in progress | `specs/work-in-progress/` |

## Rules

1. Read `architecture/README.md` and the affected `functional.md` / `technical.md` before changing anything.
2. Do not duplicate information; link to the existing source.
3. New features follow this structure and are registered in `specs/README.md`:
   ```
   specs/features/NNN-slug/
   ├── functional.md
   └── technical.md
   ```
4. Incomplete specs are titled with `[WIP]`; blocked or under-review specs move to `specs/work-in-progress/`.
5. Scenarios in `architecture/scenarios/view.md` must reference logical components and physical nodes.
6. Diagrams use Mermaid inside ` ```mermaid ` blocks.
7. Documentation is written in English; technical identifiers keep their original form.
8. Do not invent build, test, or lint commands; there is no runnable code yet.
9. Keep the 4+1 views in sync when a feature adds components, processes, dependencies, or infrastructure.
10. Do not commit, branch, or open PRs unless explicitly requested.

## Adding a feature

1. Create `specs/features/NNN-slug/`.
2. Write `functional.md`: user story, description, acceptance criteria.
3. Write `technical.md`: data flow, per-platform implementation, API contracts, data schema.
4. Verify consistency with `architecture/`.
5. Add the feature to `specs/README.md` with its status.
6. Update `architecture/scenarios/view.md` and the affected view if needed.

## Status

| Status | Meaning |
| --- | --- |
| `WIP` | Incomplete or under review |
| `Draft` | Complete, not validated |
| `Ready` | Validated, ready to implement |

## Multi-agent compatibility

`AGENTS.md` is canonical and agnostic to model and provider. Any AI assistant working in this repository should adhere to this guide.
