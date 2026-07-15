# PlanetSim

AI-first planetary simulation game project.

## Current Direction

- Core language and runtime: C# on .NET 8 LTS.
- UI goal: minimal shell, not the focus of the project.
- Primary product goal: a deeply simulated planet with manual player control over planetary parameters.
- Reference spirit: SimEarth, but with more explicit modeling, better documentation, and an AI-first workflow.

## Documentation First

Before writing implementation code, the project should be documented in enough detail that another agent can work from the specification without guessing intent.

Start here:
- [docs/00-project-brief.md](docs/00-project-brief.md)
- [docs/01-simulation-principles.md](docs/01-simulation-principles.md)
- [docs/02-tech-stack-and-architecture.md](docs/02-tech-stack-and-architecture.md)
- [docs/03-data-model.md](docs/03-data-model.md)
- [docs/04-mvp-scope.md](docs/04-mvp-scope.md)
- [docs/05-open-questions.md](docs/05-open-questions.md)
- [docs/06-agent-work-brief.md](docs/06-agent-work-brief.md)
- [docs/07-architecture-impl.md](docs/07-architecture-impl.md)

## Working Rules

- Keep the simulation core deterministic for a fixed seed and fixed inputs.
- Separate simulation logic from UI, persistence, and AI helpers.
- Prefer explicit system boundaries over a broad, mixed-purpose codebase.
- Capture assumptions and unknowns in documentation before implementation.
