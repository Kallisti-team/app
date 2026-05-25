# Kallisti App Constitution

## Core Principles

### I. Monorepo with Three Domains

The project is organized into three root-level directories with strict responsibilities:

- **`frontend/`**: UI layer built with Vite + React + TypeScript. Must not contain business logic or direct data access.
- **`backend/`**: API layer built with Rust using Hexagonal Architecture (Ports & Adapters). Must not contain presentation logic.
- **`IaC/`**: Infrastructure as Code using Docker Compose. Must not contain application code.

### II. Hexagonal Architecture (Backend)

The Rust backend MUST follow the Ports & Adapters (Hexagonal Architecture) pattern:

- **Domain**: Entities, value objects, and repository traits. Must not depend on external libraries or infrastructure.
- **Application**: Use cases and input ports. Must depend only on the domain layer.
- **Infrastructure**: Concrete adapters (HTTP, database, queues, etc.). May depend on domain and application layers.

Any external dependency (database, HTTP client, messaging system, etc.) MUST be injected through traits.

### III. Decoupled Frontend (NON-NEGOTIABLE)

The React + TypeScript frontend:

- MUST NOT contain business logic — only presentation logic and UI state management.
- Communication with the backend MUST occur through API contracts defined in `backend/contracts/`.
- Shared types MUST either be duplicated intentionally or extracted into a reusable `shared/` package when necessary.

### IV. Declarative Infrastructure

All runtime environments (development, staging, and production) MUST be defined inside `IaC/`:

- `docker-compose.yml` MUST be used for local development environments.
- Environment variables MUST be separated by environment.
- Infrastructure configuration MUST NOT be hardcoded inside `frontend/` or `backend/`.

### V. Layer-Based Testing Strategy

Each layer MUST have its own testing strategy:

- **Domain (Rust)**: Pure unit tests without mocking. Coverage MUST be greater than 90%.
- **Application (Rust)**: Tests using mocked traits. Coverage MUST be greater than 80%.
- **Infrastructure (Rust)**: Integration tests using real containers and services.
- **Frontend (React)**: Component tests with Testing Library and end-to-end tests with Playwright.
- **IaC**: `docker compose config` MUST be validated in CI pipelines.

### VI. Simplicity and YAGNI

Do not introduce abstractions, libraries, or architectural layers before they are necessary.

- Every external dependency MUST be justified in the corresponding PR.
- Prefer simple types (`struct`, `enum`) over complex frameworks whenever possible.
- If a solution can be implemented without a framework, it SHOULD be implemented without one.

## Technology Stack

| Layer          | Technology                      |
|----------------|---------------------------------|
| Frontend       | Vite + React + TypeScript       |
| Backend        | Rust (Edition 2024)             |
| Infrastructure | Docker Compose                  |
| Testing        | cargo test, Vitest, Playwright  |
| CI/CD          | GitHub Actions                  |

## Development Workflow

- **Branches**: `feature/<name>` from `main`. Pull Requests are mandatory.
- **Commits**: Must follow Conventional Commits (`feat:`, `fix:`, `refactor:`, `chore:`, `docs:`).
- **PR Gate**: Lint → Test → Build → Docker Compose validation must pass before merging.
- **Architecture Decision Records (ADR)**: Any significant architectural decision MUST be documented as an ADR in `docs/adr/`.
- **API Contracts**: Contracts between frontend and backend MUST be defined and agreed upon before implementing either side.

## Governance

- This constitution takes precedence over any undocumented practice.
- Amendments MUST be documented in a PR with justification, impact analysis, and a migration plan.
- Every PR MUST verify compliance with these principles.
- Violations of NON-NEGOTIABLE principles MUST block the merge automatically.

**Version**: 1.0.0 | **Ratified**: 2026-05-24 | **Last Amended**: 2026-05-24
