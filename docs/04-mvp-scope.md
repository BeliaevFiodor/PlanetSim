# MVP Scope

## MVP Goal

Prove that the simulation loop works for one planet under one star with visible cause and effect.

## MVP In Scope

- single star,
- single planet,
- orbit and illumination,
- basic climate response,
- ocean and ice response,
- manual parameter editing,
- deterministic simulation seed,
- event log,
- save and load,
- basic charts or textual summaries.

## MVP Out of Scope

- full civilization simulation,
- advanced tectonic realism,
- multi-star systems,
- procedural 3D rendering,
- complex UI polish,
- multiplayer,
- modding platform,
- complete scientific fidelity.

## MVP Success Criteria

- A user can create a planet and see it evolve over time.
- A user can change a parameter and observe a meaningful change in the simulation.
- The system can explain the main reason a change happened.
- A save can be restored and continue from the same state.

## Recommended Build Order

1. Define world model and documentation.
2. Implement deterministic simulation core.
3. Implement orbit and illumination.
4. Implement heat balance and first-order climate response.
5. Implement tectonics and surface evolution.
6. Implement save/load and event log.
7. Add a minimal UI shell.
8. Add AI explanation and scenario support.

## Initial Simulation Slice

The first expandable slice should be deliberately small:

- orbit and illumination,
- surface heating and temperature distribution,
- tectonic and surface reshaping effects.

That slice is the foundation for later layers such as geochemistry, hydrology, biosphere, and civilization.
