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

## High-Level WorldState Contract

The first scaffold should define `WorldState` as a composition of a small number of explicit top-level sections rather than as a flat bag of fields.

Recommended top-level sections:

- `WorldIdentity`
- `SimulationContext`
- `SourceParameters`
- `StarState`
- `OrbitState`
- `PlanetState`
- `AtmosphereState`
- `HydrosphereState`
- `CryosphereState`
- `GeologyState`
- `DerivedState`
- `EventState`

The exact class names can change later, but the separation of responsibilities should remain stable.

### Section Roles

#### `WorldIdentity`

Purpose:

- stable world identifier,
- scenario identifier,
- model version,
- save format version.

Data type:

- metadata only.

Ownership:

- simulation core,
- later persistence layer for serialization concerns.

#### `SimulationContext`

Purpose:

- current simulation time,
- tick counter,
- seed and deterministic randomization context,
- active scenario overrides,
- temporary interventions that are still in effect.

Data type:

- mutable simulation control state.

Ownership:

- scheduler and simulation core.

#### `SourceParameters`

Purpose:

- player-editable base inputs,
- initial star settings,
- initial orbit settings,
- initial planetary composition and size,
- editable climate and tectonic intensity knobs,
- catastrophe frequency controls when those are introduced.

Data type:

- source parameters only.

Ownership:

- simulation core as the authoritative container,
- edited by presentation and scenario tooling later,
- read by all domain systems.

#### `StarState`

Purpose:

- luminosity,
- spectral characteristics,
- variability profile,
- stellar age,
- current effective radiation output.

Data type:

- mostly source-backed state with some mutable or computed runtime values if variability is modeled.

Ownership:

- star and illumination system.

#### `OrbitState`

Purpose:

- semi-major axis,
- eccentricity,
- inclination,
- axial tilt,
- rotation period,
- orbital period,
- current orbital position,
- current seasonal and day-cycle phase.

Data type:

- mixed source and mutable state.

Ownership:

- orbit and illumination system.

#### `PlanetState`

Purpose:

- mass,
- radius,
- gravity,
- internal heat budget,
- magnetic field strength,
- land/ocean distribution baseline,
- planetary surface configuration that does not belong exclusively to atmosphere or water systems.

Data type:

- mixed source and mutable state.

Ownership:

- shared container owned by the simulation core,
- geology and tectonics system owns the mutable surface-shape slice,
- other systems may read but should not rewrite unrelated slices.

#### `AtmosphereState`

Purpose:

- composition,
- pressure,
- greenhouse forcing,
- aerosol load,
- cloud properties,
- temperature fields and climate values that are treated as mutable state.

Data type:

- mutable domain state plus some source-backed configuration.

Ownership:

- atmosphere and climate system.

#### `HydrosphereState`

Purpose:

- ocean coverage,
- sea level,
- salinity,
- circulation values,
- evaporation and precipitation balance.

Data type:

- mutable domain state plus some source-backed quantities.

Ownership:

- oceans and ice system for ocean behavior,
- climate systems may influence it through defined inputs rather than direct uncontrolled mutation.

#### `CryosphereState`

Purpose:

- ice coverage,
- glacial extent,
- snow albedo,
- freeze and thaw state.

Data type:

- mutable domain state.

Ownership:

- oceans and ice system.

#### `GeologyState`

Purpose:

- tectonic activity,
- volcanic activity,
- crust and mantle exchange summaries,
- uplift, erosion, and major surface reshaping indicators,
- long-timescale heat and surface change state.

Data type:

- mutable domain state plus some source-backed intensity settings.

Ownership:

- geology and tectonics system.

#### `DerivedState`

Purpose:

- values that are computed from other sections,
- habitability indicators,
- climate summaries,
- stability scores,
- explanation-ready aggregate indicators.

Data type:

- derived values only.

Ownership:

- simulation core as the storage boundary,
- populated by the system or systems that compute each value.

Rule:

- nothing in `DerivedState` should be the sole source of truth for underlying simulation logic.

#### `EventState`

Purpose:

- recent emitted simulation events,
- significant transitions,
- explanation anchors for replay and AI summaries.

Data type:

- mutable event history and event queue state.

Ownership:

- event layer, with domain systems emitting into it.

### State Classification Rule

Every field placed in `WorldState` should be classified as one of:

- source parameter,
- mutable simulation state,
- derived value,
- metadata.

That classification should be obvious from the surrounding section so future agents do not need to infer whether a value is editable, simulated, or computed.

### Ownership Rule

Each mutable slice of the world should have one primary owner.

For the first scaffold, ownership should be interpreted like this:

- orbit system owns orbital progression and light-cycle progression,
- climate system owns atmosphere and temperature evolution,
- oceans and ice system owns ocean and ice evolution,
- geology system owns tectonics and long-term surface reshaping,
- simulation core owns world assembly, lifecycle coordination, and cross-cutting metadata,
- event layer owns event storage and publication mechanics.

Systems may read across boundaries, but cross-system writes should happen only through explicit contracts rather than ad hoc mutation.

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

## Scheduler Contract

The scheduler is the mechanism that advances one global simulation clock and decides which systems execute on each step.

### Scheduler Responsibilities

For the first scaffold, the scheduler should be responsible for:

- advancing canonical simulation time,
- advancing the global tick counter,
- selecting which systems are due to run,
- invoking systems in a deterministic order,
- passing a consistent tick context to each system,
- ensuring skipped systems preserve prior state until their next due execution,
- routing event-triggered executions through explicit rules instead of hidden side effects.

The scheduler should not contain domain logic for climate, geology, oceans, or other world behavior. Its role is coordination, not simulation.

### Global Clock Rule

There should be one authoritative simulation clock for the whole world.

That means:

- no per-system clocks as separate sources of truth,
- no duplicated world timelines,
- no independent local times that can drift from the canonical world time.

Systems may reason about cadence or elapsed time since their last execution, but they must do so from scheduler-provided context derived from the same global clock.

### Base Tick Rule

The scheduler should advance the simulation using one smallest base step for the current model.

That base step is the unit that:

- increments the world clock,
- increments the tick counter,
- determines when cadence groups become due.

Fast systems may run every base tick. Medium and slow systems may run only on selected ticks derived from that same base sequence.

### Cadence Group Rule

For the first scaffold, cadence should be expressed as named groups rather than fully custom intervals on every system.

Recommended groups:

- `Fast`
- `Medium`
- `Slow`

Interpretation:

- `Fast` systems run every base tick,
- `Medium` systems run on a fixed recurring subset of ticks,
- `Slow` systems run on a less frequent fixed recurring subset of ticks.

The exact numeric ratios can remain configurable later, but the initial implementation should keep them explicit and simple enough to unit test.

### Execution Rules

The scheduler should follow these execution rules:

1. Advance the canonical clock once per base tick.
2. Determine which cadence groups are due for that tick.
3. Build the ordered list of systems that are due.
4. Execute those systems in a fixed deterministic order.
5. Allow each system to update only its owned slice of `WorldState`.
6. Collect emitted events.
7. Make resulting state available for the next base tick.

Important execution constraints:

- system order must not depend on hash order, registration timing accidents, or thread scheduling,
- systems that are not due must not execute partial logic,
- systems should observe the same tick context for the same scheduled step,
- repeated runs with the same seed and inputs must produce the same execution schedule and resulting state transitions.

### Event-Triggered Rule

Some major events may require immediate handling outside ordinary cadence boundaries.

For the first scaffold, this should still remain deterministic:

- event-triggered execution must be explicit,
- event rules must be ordered,
- event-triggered work must still use the same world state and canonical clock,
- the scheduler must record when an out-of-band execution occurred and why.

This prevents the event mechanism from becoming a hidden second scheduler.

### Schedulable System Contract

To participate in scheduling, a system should expose at least the following concepts:

- stable system identity,
- declared cadence group,
- declared execution order or ordering key,
- a pure update entry point that accepts the current `WorldState` and tick context,
- emitted events or state transition outputs in a structured form.

At the documentation level, every schedulable system must answer:

- what slice of `WorldState` it owns,
- which other sections it may read,
- which cadence group it belongs to,
- whether it can also react to explicit events,
- what deterministic inputs it depends on.

### Tick Context

Each execution should receive a scheduler-provided context containing enough information to behave deterministically.

That context should include:

- current canonical time,
- current global tick index,
- current cadence group being executed,
- elapsed time since the system last ran,
- seed-derived deterministic randomness context if randomness is used,
- any explicit event trigger information for this execution.

The purpose of this context is to avoid hidden dependencies on external clocks or ambient state.

### Deterministic Ticking Rule

Deterministic ticking means that the same:

- starting `WorldState`,
- source parameters,
- scenario overrides,
- seed,
- scheduler configuration,
- system registration list,
- system execution order

must produce the same:

- sequence of due systems,
- event emissions,
- resulting world states.

To preserve that property, the first scaffold should avoid:

- wall-clock time,
- unordered iteration for system scheduling,
- non-seeded randomness,
- asynchronous mutation of shared state during a tick,
- hidden writes outside the scheduler-controlled update cycle.

### First-Slice Scheduling Assignment

For the first executable slice, the initial assignment should be:

- orbit and illumination: `Fast`,
- heat balance: `Fast`,
- tectonics and long-term surface change: `Slow`.

This is sufficient to prove that:

- different systems can run at different cadences,
- one shared `WorldState` can evolve safely,
- the scheduler can be unit tested independently from domain details.

### Testing Expectations for the Scheduler

The first unit tests around the scheduler should verify:

- due-system selection by cadence group,
- fixed execution order,
- correct advancement of canonical time,
- correct skipped execution behavior for non-due systems,
- deterministic reproduction of multi-tick runs,
- deterministic handling of explicit event-triggered executions.

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
