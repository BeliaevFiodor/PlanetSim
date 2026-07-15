# Simulation Principles

## Modeling Philosophy

The simulation should model the planet as a set of interacting systems with different timescales, rather than as a single monolithic world state.

## Timescale Layers

- Short scale: illumination, day/night cycle, seasons, weather-like variation.
- Medium scale: climate drift, hydrology, biosphere shifts, population change.
- Long scale: plate motion, mountain building, erosion, carbon cycle, evolution, mass extinction.
- Chemical scale: elemental reservoirs, isotope drift, mantle-crust-atmosphere exchange, nutrient availability.
- Event scale: asteroid impacts, supervolcanoes, orbital perturbations, manual interventions.

## Required Determinism

For a fixed seed, fixed input parameters, and fixed version of the simulation rules, the same world state progression should be reproducible.

This is important for:

- testing,
- debugging,
- AI explanation,
- historical replay,
- scenario comparison.

## Illumination and Orbit

The simulation should account for at least these factors:

- stellar luminosity,
- orbital distance,
- orbital eccentricity,
- axial tilt,
- day length,
- year length,
- albedo,
- atmospheric absorption,
- greenhouse forcing.

The first version should assume one star and one planet. Multi-star systems can be treated as a later expansion.

## Player Control Surface

The player should be able to edit key parameters directly, at least in early prototype form:

- stellar properties,
- orbit shape and scale,
- axial tilt and rotation,
- elemental budgets and reservoir exchange rates,
- atmosphere composition,
- ocean coverage,
- volcanic and tectonic intensity,
- catastrophe frequency,
- life emergence and evolution settings,
- civilization initialization and progression knobs.

## Explainability

When the world changes significantly, the simulation should be able to explain the main chain of causes in plain language. This is especially important because the project is AI-first and should be inspectable rather than opaque.
