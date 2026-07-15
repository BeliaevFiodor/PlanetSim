# Data Model

## Core Entities

### Star

- luminosity
- spectral characteristics
- variability profile
- age
- radiation output

### Orbit

- semi-major axis
- eccentricity
- inclination
- axial tilt
- rotation period
- orbital period
- precession parameters

### Planet

- mass
- radius
- gravity
- internal heat budget
- surface water coverage
- land/ocean distribution
- tectonic activity
- volcanic activity
- magnetic field strength
- atmospheric state

### Atmosphere

- composition
- pressure
- greenhouse effect
- aerosol load
- cloud properties
- temperature fields

### Geochemistry

- elemental reservoirs for the planetary crust, mantle, atmosphere, and ocean
- carbon budget
- oxygen budget
- hydrogen budget
- nitrogen budget
- sulfur budget
- phosphorus budget
- silicon budget
- iron budget as an aggregate rather than full atomic tracking
- trace element groups and mineral availability
- isotope ratios where they matter for long-term evolution
- exchange rates between reservoirs

## Chemistry Tracking Rule

Track critical light and biologically relevant elements explicitly where they drive climate, atmosphere, and crust formation. Track heavy elements mostly as aggregated reserves, mineral classes, or coarse geochemical pools unless a specific gameplay or scientific reason requires finer detail.

### Hydrosphere

- ocean coverage
- sea level
- salinity
- circulation values
- evaporation and precipitation balance

### Cryosphere

- ice coverage
- glacial extent
- snow albedo
- freeze/thaw dynamics

### Biosphere

- biomass
- biodiversity index
- biome distribution
- oxygenation level
- extinction events
- evolutionary pressure fields

### Civilization Layer

- population groups
- technology level
- cultural regions
- infrastructure
- resource use
- emissions and planetary impact

## World State Rules

- The world state must be serializable.
- The world state must be versioned.
- Derived values should be distinguishable from source parameters.
- Long-running simulations must preserve a history of significant events.

## Parameter Editing

The player should be able to edit a controlled set of source parameters directly. The model should distinguish between:

- base inputs,
- computed outputs,
- derived indicators,
- scenario overrides,
- temporary interventions.

## Save Philosophy

A save file should represent both the current state and enough metadata to understand how that state was produced.
