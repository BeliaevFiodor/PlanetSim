# Project Brief

## One-Sentence Goal

Build an AI-first planetary simulation game where the player can manually shape a planet's evolution across geological, climatic, biological, and historical timescales.

## Design Intent

This project is not a conventional city builder or strategy game. The primary object is a living planet model. The player acts more like an operator, scientist, or god-like editor who can inspect state, change parameters, and observe consequences over long spans of time.

## Core References

- SimEarth as a design reference for planetary-scale simulation and direct parameter manipulation.
- Scientific plausibility as a guiding principle, but not a requirement for full physical fidelity.
- AI-first development, meaning documentation, observability, and explainability come before implementation polish.

## Non-Negotiable Product Traits

- The simulation must support planetary-scale change over deep time.
- The player must be able to change parameters manually.
- The model must account for orbital and stellar illumination effects.
- The simulation should explain major causal chains instead of only rendering outcomes.
- The architecture must keep the simulation core isolated from the UI layer.

## Major Systems

- Star and orbit system.
- Illumination and seasonal forcing system.
- Atmosphere and climate system.
- Hydrosphere and cryosphere system.
- Geology and tectonics system.
- Biosphere and evolution system.
- Civilization and history system.
- Event and catastrophe system.

## Initial Scope Assumption

Start with a single planet and a single star. Add more complex astronomy only after the single-world model is stable.

## AI-First Requirement

The project must be documented so a second agent can understand:

- what the simulation is trying to represent,
- which parameters are editable,
- which systems depend on which other systems,
- what the MVP is,
- what is explicitly out of scope for the first implementation.
