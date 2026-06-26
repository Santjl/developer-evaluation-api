# `.github/copilot-instructions.md`

```md
# Repository Instructions for GitHub Copilot

## Project Context

This repository is a .NET 8.0 API project.

The API has integration dependencies on:

- Kafka
- SQL Server
- MongoDB

These dependencies are already configured through `docker-compose`.

The integration test infrastructure already has fixtures configured to bootstrap and manage the required external dependencies. Do not introduce a new test infrastructure unless there is a clear architectural reason and the change is explicitly justified.

## Testing Context

The main focus for testing work is the `IntegratedTests` project.

When analyzing or modifying tests:

- Prefer integration tests over excessive mocking when the behavior depends on Kafka, SQL Server, MongoDB, API routing, serialization, persistence, or external message flow.
- Reuse the existing fixtures and docker-compose setup.
- Do not replace real dependency coverage with mocks unless the test scope is explicitly unit-level.
- Keep tests deterministic, isolated, and repeatable.
- Avoid tests that depend on execution order.
- Be careful with shared state across SQL Server, MongoDB, and Kafka.
- Prefer clear Given/When/Then structure.
- Prefer test data builders, factories, and explicit seed methods over duplicated inline setup.
- When testing Kafka behavior, account for async/eventual consistency using bounded retries or polling helpers instead of arbitrary long delays.
- When testing SQL Server or MongoDB persistence, assert observable behavior through the API when possible, and inspect storage only when needed to validate side effects.
- Keep test names descriptive and business-oriented.

## Coverage Analysis Expectations

When asked to review integration test coverage:

1. Identify all API endpoints, commands, queries, consumers, producers, handlers, application services, and persistence flows that should be covered by integration tests.
2. Compare them against the current tests inside `IntegratedTests`.
3. Produce a coverage matrix.
4. Clearly classify each item as:
   - Covered
   - Partially covered
   - Not covered
   - Unclear / needs manual confirmation
5. Identify missing scenarios:
   - happy path
   - validation errors
   - not found
   - conflict / duplicate
   - authorization / authentication
   - persistence side effects
   - Kafka publish / consume behavior
   - SQL Server behavior
   - MongoDB behavior
   - failure or retry behavior, when applicable
6. Propose an incremental attack plan before writing tests.

## Architecture Review Expectations

When reviewing test architecture and fixtures:

- Inspect how fixtures start and configure the API under test.
- Inspect how docker-compose is used.
- Inspect how Kafka, SQL Server, and MongoDB are reset or isolated between tests.
- Inspect how test data is seeded.
- Inspect whether collections, topics, databases, schemas, or transactions are isolated.
- Inspect whether tests can safely run in parallel.
- Inspect whether the fixture design is too coupled, too slow, or too hard to extend.
- Propose improvements before changing implementation.

## Change Policy

Before implementing test changes:

- First produce a plan.
- Separate coverage gaps from fixture/architecture problems.
- Avoid large rewrites.
- Prefer small, reviewable PRs.
- Preserve existing tests unless they are incorrect or duplicated.
- When adding tests, explain which gap each test closes.
- When changing fixtures, explain why the current fixture is insufficient.
```
