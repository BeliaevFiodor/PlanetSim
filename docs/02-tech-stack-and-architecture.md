# Tech Stack and Architecture

## Recommended Stack

- Runtime: .NET 8 LTS.
- Language: C#.
- Desktop UI: Avalonia only if and when a real interface is needed.
- Storage: SQLite for saves, history, and telemetry.
- Config formats: JSON for scenarios and project metadata.
- Tests: xUnit.

## Architecture Style

Use a layered architecture with a strict simulation core.

### 1. Simulation Core

A pure library with no UI dependencies and no direct file access.

Responsibilities:

- planet state,
- system updates,
- time progression,
- deterministic randomization,
- event emission,
- derived measurements.

### 2. Domain Systems

Independent modules that read and update slices of the world state.

Examples:

- orbital mechanics and illumination,
- geochemistry and elemental cycles,
- atmosphere, temperature and climate,
- oceans and ice,
- geology and tectonics,
- life and evolution,
- civilization and history.

### 3. Scheduler

Coordinates update frequency by timescale.

It should allow different systems to run on different steps without forcing everything onto the same tick rate.

### 4. Event Layer

A shared event stream for catastrophes, discoveries, interventions, and major world transitions.

### 5. Persistence Layer

Handles save/load, scenario import/export, and replayable state history.

### 6. Presentation Shell

A thin client focused on observation and parameter editing.

### 7. AI Layer

AI should not replace the simulation. It should sit above it and help with:

- explanation,
- summarization,
- report generation,
- scenario drafting,
- parameter recommendation,
- test case generation.

## Why Not Start with ECS

A full ECS is not the right first abstraction unless the world becomes dominated by very large numbers of homogeneous entities.

For the initial planetary model, a domain-first design is easier to document, easier to test, and easier to explain to another agent.

## Core Rule

Keep the simulation core pure and deterministic. Treat the UI and AI tooling as consumers of the model, not as the model itself.
