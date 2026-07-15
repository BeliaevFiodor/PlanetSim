# Agent Work Brief

This repository is an AI-first planetary simulation project.

## What the next agent must assume

- The project is still in documentation-first mode.
- The primary goal is not UI polish.
- The core goal is a deep planetary simulation with manual parameter control.
- The simulation should be deterministic for a fixed seed and fixed inputs.
- The first playable target is a single star and a single planet.

## Preferred Technical Direction

- Language: C#
- Runtime: .NET 8 LTS
- UI: minimal shell only
- Persistence: SQLite or a similarly practical local store
- Configuration: JSON for scenarios and metadata

## Required Architectural Separation

The next agent should preserve the following boundaries:

- simulation core,
- domain systems,
- scheduler/time model,
- event pipeline,
- persistence,
- presentation shell,
- AI helper layer.

## Required Simulation Concerns

The next agent must account for:

- star and orbit,
- illumination and seasons,
- geochemical reservoirs and elemental cycling,
- atmosphere,
- climate,
- oceans and ice,
- geology,
- biosphere,
- civilization/history,
- catastrophic events,
- explainable cause and effect.

## Working Rule

Do not start implementation by wiring UI screens. First formalize the world model, system boundaries, and MVP scope.

## Immediate Next Deliverable

A concrete architecture note or implementation plan that turns the current documentation into a repo structure and first code scaffold.

## First Implementation Slice

Start with the smallest useful deterministic model:

- orbit and illumination,
- heat balance,
- tectonics and surface change.

Treat that as the base layer for adding geochemistry, hydrology, biosphere, and other optional systems later.
