# Architecture Implementation Plan

## Purpose

This document turns the existing project brief, simulation principles, data model, and MVP notes into a concrete implementation direction for the first code scaffold.

## AI-First Working Rule

Before implementation starts, the repository should explain enough that another agent can continue without guessing architecture, boundaries, priorities, or near-term sequencing.

That means the first implementation must optimize for:

- determinism,
- inspectability,
- explainability,
- documentation clarity,
- small testable boundaries,
- minimal accidental complexity.

## Confirmed Decisions

The following decisions are now treated as working assumptions for the first implementation:

1. The simulation uses one global simulation time.
2. Each domain layer uses its own update cadence through a scheduler rather than through separate world copies.
3. The simulation keeps one canonical world state.
4. Unit tests come before integration work.
5. The first implementation stays focused on the simulation core rather than UI or broad infrastructure.
6. The first playable target remains one star and one planet.

## Core Architectural Shape

The first code scaffold should be organized around:

- a pure simulation core,
- explicit domain systems,
- a scheduler/time model,
- a shared event stream,
- later persistence and presentation layers.

The simulation core must remain free of UI concerns and direct file access.

## Canonical World State

The architecture should keep only one authoritative `WorldState`.

That world state should contain:

- source parameters,
- mutable simulation state,
- simulation metadata,
- derived values that are clearly distinguishable from source values,
- enough identifiers and version markers for future serialization.

Additional copies of the world should be avoided during normal ticking. New snapshots should exist only for:

- save and load,
- replay,
- history,
- deterministic test fixtures,
- debugging when explicitly needed.

## Domain System Boundaries

Each domain system should own a well-defined slice of the shared world state.

Initial boundaries:

- star and orbit,
- illumination and heat balance,
- atmosphere and climate,
- oceans and ice,
- geology and tectonics.

Deferred boundaries for later phases:

- geochemistry,
- biosphere and evolution,
- civilization and history,
- advanced catastrophe modeling.

Each system should:

- read the shared world state,
- update only its owned slice,
- expose deterministic behavior for fixed inputs,
- emit structured events when major transitions happen.

## Scheduler and Tick Model

The project should not force all systems onto one fixed tick rate.

Instead:

- the simulation keeps one canonical clock,
- the scheduler decides which systems run on each step,
- each system declares its update cadence,
- major events can bypass ordinary cadence rules when needed.

For the MVP, cadence should stay simple and grouped rather than fully arbitrary.

Recommended cadence groups:

- fast cadence for orbit, illumination, and temperature response,
- medium cadence for atmosphere drift, oceans, and ice,
- slow cadence for tectonics, long geologic change, and other deep-time systems.

This keeps the model simple enough for testing while still matching the requirement that different layers evolve on different timescales.

## First Executable Slice

The first executable simulation slice should stay intentionally small:

- orbit and illumination,
- heat balance,
- tectonic surface change.

This slice is enough to prove:

- deterministic progression,
- scheduler-driven cadence,
- shared world-state updates,
- visible cause and effect.

Oceans, ice, geochemistry, biosphere, and persistence should be planned now but implemented later.

## Testing Strategy

Testing should start with unit tests only.

First test targets:

- scheduler cadence behavior,
- deterministic advancement for fixed seeds and inputs,
- isolated domain system updates,
- preservation of one shared world state through multiple ticks,
- event emission for meaningful state changes.

Integration tests should be deferred until:

- multiple systems are implemented,
- persistence exists,
- cross-system workflows become meaningful enough to justify them.

## Suggested First Repo Structure

When implementation begins, the repository should grow in this direction:

- one solution for the project,
- one simulation core project,
- one unit test project,
- later projects for persistence, presentation, and integration tests.

The important rule is separation of concerns, not the exact folder names.

## Near-Term Implementation Order

1. Finalize documentation needed for the first scaffold.
2. Create the solution and pure simulation core.
3. Define `WorldState` and system contracts.
4. Implement the scheduler and global simulation clock.
5. Implement the first three systems: orbit, heat balance, tectonics.
6. Add unit tests around determinism and cadence.
7. Add event logging and explanation hooks.
8. Add persistence only after the core model stabilizes.
9. Add thin presentation and AI helper layers later.

## Non-Goals for the First Scaffold

The first scaffold should explicitly avoid:

- UI-first implementation,
- multiple planets or stars,
- advanced tectonic realism,
- broad integration wiring,
- premature persistence complexity,
- agent-specific hidden assumptions that are not written in docs.

## Definition of Documentation Ready

Implementation should begin only when the documentation set clearly answers:

- what the first world contains,
- which systems exist first,
- how time advances,
- which layers update at which cadence group,
- what is deferred,
- what must be unit tested before expansion.

This document is intended to serve as that bridge between high-level design and the first code scaffold.
