# `.github/prompts/review-integration-test-coverage.prompt.md`

```md
---
agent: 'agent'
description: 'Review .NET 8 integration test coverage, identify gaps in IntegratedTests, call the integration-test-architect agent, and produce a coverage attack plan.'
tools: ["read", "search", "runSubagent"]
---

# Goal

Review the integration test coverage of this .NET 8 API repository.

The main integration test project is `IntegratedTests`.

The API depends on:

- Kafka
- SQL Server
- MongoDB

These dependencies are already configured through `docker-compose` and already wired through the existing test fixtures.

Your goal is to understand what is currently covered, what is not covered, and propose an attack plan to increase integration test coverage.

Do not implement tests yet unless explicitly requested after the plan.

# Important Instruction

Use the `integration-test-architect` subagent to review the fixture architecture and determine whether the current fixture setup supports the proposed coverage plan.

You may invoke it directly as:

Use the `integration-test-architect` subagent to review the integration test fixture architecture, docker-compose usage, Kafka, SQL Server and MongoDB setup, and return a plan for improving fixture design to support the missing coverage.

# Step 1 — Discover the Solution Structure

Inspect the repository and identify:

- solution file
- API project
- application/core projects
- infrastructure projects
- test projects
- the `IntegratedTests` project
- docker-compose files
- fixture classes
- base integration test classes

Find relevant files such as:

- `*.sln`
- `*.csproj`
- `docker-compose*.yml`
- `*Fixture*.cs`
- `*WebApplicationFactory*.cs`
- `*IntegratedTests*.csproj`
- `*IntegrationTest*.cs`
- API controllers or minimal API endpoints
- command/query handlers
- Kafka consumers
- Kafka producers
- SQL Server repositories
- MongoDB repositories

# Step 2 — Map Production Behaviors That Should Have Integration Coverage

Create a list of integration coverage candidates.

Include:

- API endpoints
- request/response contracts
- validation behavior
- authentication/authorization behavior
- command handlers
- query handlers
- SQL Server persistence flows
- MongoDB persistence flows
- Kafka producers
- Kafka consumers
- background/event-driven flows
- idempotency or duplicate handling
- error scenarios
- retry/failure behavior, if present

For each candidate, identify:

- production files involved
- expected behavior
- dependency touched: API, SQL Server, MongoDB, Kafka
- whether it should be covered by integration tests

# Step 3 — Map Existing IntegratedTests Coverage

Inspect the `IntegratedTests` project.

For each existing test, identify:

- test class
- test method
- behavior covered
- endpoint/handler/consumer covered
- dependencies exercised
- assertions made
- whether the test is meaningful or superficial

Do not rely only on coverage percentage. Prefer behavior-based coverage.

# Step 4 — Build a Coverage Matrix

Create a Markdown table:

| Area | Production Entry Point | Expected Behavior | Existing Test(s) | Status | Missing Scenarios | Priority |
|---|---|---|---|---|---|---|

Status must be one of:

- Covered
- Partially covered
- Not covered
- Unclear

Priority must be one of:

- P0 — critical business flow or high-risk dependency behavior
- P1 — important flow
- P2 — useful but lower risk
- P3 — optional or low value

# Step 5 — Identify Gaps

Group missing coverage by type:

## API Contract Gaps

Examples:

- missing success scenario
- missing validation scenario
- missing not found scenario
- missing conflict scenario
- missing auth scenario

## SQL Server Gaps

Examples:

- missing persistence assertion
- missing transaction/consistency scenario
- missing duplicate handling
- missing query filtering/sorting/pagination scenario

## MongoDB Gaps

Examples:

- missing document persistence
- missing document update
- missing collection cleanup/isolation
- missing query behavior

## Kafka Gaps

Examples:

- missing producer test
- missing consumer test
- missing message payload contract
- missing idempotency
- missing retry/dead-letter/failure path
- missing eventual consistency assertion

## Fixture / Test Infrastructure Gaps

Examples:

- difficult setup
- shared state
- missing test data builders
- missing polling helper
- missing cleanup helper
- unclear fixture responsibility

# Step 6 — Call the Architecture Agent

Use the `integration-test-architect` subagent to review:

- fixture design
- docker-compose integration
- SQL Server setup/reset
- MongoDB setup/reset
- Kafka setup/reset
- test data setup
- async/eventual consistency support
- test isolation
- maintainability

Ask the subagent to connect fixture architecture problems to the missing coverage areas.

Incorporate the subagent's findings into the final plan.

# Step 7 — Produce the Final Report

Return a Markdown report with the following structure:

## Executive Summary

Summarize:

- current integration coverage quality
- main uncovered areas
- main fixture/architecture risks
- recommended attack strategy

## Repository / Test Structure Found

List the relevant projects and files.

## Current IntegratedTests Coverage

Summarize what is already covered.

## Coverage Matrix

Include the full coverage matrix.

## Missing Coverage by Category

Include:

- API
- SQL Server
- MongoDB
- Kafka
- error/edge cases
- auth/security, if applicable

## Fixture Architecture Review

Summarize the findings from the `integration-test-architect` subagent.

## Recommended Attack Plan

Break into phases:

### Phase 1 — Fixture and test infrastructure adjustments

Only include changes needed to safely add tests.

### Phase 2 — High-value API integration tests

Prioritize P0 and P1 endpoints/flows.

### Phase 3 — Persistence coverage

Cover SQL Server and MongoDB side effects.

### Phase 4 — Kafka/event-driven coverage

Cover producers, consumers, message contracts, idempotency, and eventual consistency.

### Phase 5 — Cleanup and maintainability

Improve builders, helpers, naming, and duplication.

## Suggested First Pull Request

Describe the smallest useful PR to start with.

Include:

- files to touch
- tests to add
- fixture changes, if any
- acceptance criteria

## Acceptance Criteria for the Whole Initiative

Define measurable completion criteria.

Examples:

- all P0 flows covered by integration tests
- all API endpoints have at least one happy path and one relevant failure path
- Kafka producers/consumers have integration coverage where business-critical
- SQL Server and MongoDB side effects are verified where relevant
- no test depends on execution order
- tests can run locally using existing docker-compose/fixtures
```
