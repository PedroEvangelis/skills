# Phase 9 — Testability Analysis

## Purpose

Evaluate whether the requirements are clear and specific enough to be tested.
Identify ambiguities, gaps, and missing acceptance criteria before implementation begins.

## What You Produce

`testability-report.md` — A document containing:
- Testability assessment for each requirement
- Identified ambiguities and suggested clarifications
- Test scenarios per use case
- Gaps in requirements (missing error cases, missing flows)
- Recommended test strategy (unit, integration, E2E)

## Input

All previous phases, especially requirements (Phase 0) and behavioral analysis (Phase 8).

## Workflow

### Step 1 — Assess Each Requirement for Testability

For each RF, RNF, and RN:

- "Can I write a test that proves this requirement is met?"
- "Is the expected outcome clear and measurable?"
- "Are the inputs well-defined?"
- "Are the boundary conditions specified?"

Mark each as:
- **Testable**: Clear inputs, outputs, and acceptance criteria
- **Ambiguous**: Vague terms that need clarification ("fast", "secure", "intuitive")
- **Incomplete**: Missing error cases, missing boundary conditions
- **Untestable**: Cannot be verified without additional specification

**Validation checkpoint:** Every requirement has a testability assessment. If any is marked "Untestable," it must be reformulated before implementation begins.

### Step 2 — Identify Ambiguities

Look for vague language in requirements:

| Vague Term | Problem | Suggested Clarification |
|---|---|---|
| "The system should be fast" | No metric | "Response time < 200ms for 95th percentile" |
| "Secure authentication" | No specifics | "OAuth 2.0 with PKCE, tokens encrypted at rest" |
| "Handle errors gracefully" | No definition | "Return structured error JSON with appropriate HTTP status" |
| "Support many users" | No number | "Support 100 concurrent users with < 5s response time" |

**Validation checkpoint:** Every vague term has been flagged with a suggested clarification. If the user can't provide a metric, document the ambiguity as a risk.

### Step 3 — Define Test Scenarios per Use Case

For each use case, define test scenarios:

```
## UC-01: Publish Vehicle

### Happy Path
- Given a valid vehicle with all required fields
- When published to ML and OLX
- Then listings are created with status "pending"
- And jobs are dispatched for each portal

### Alternative Paths
- Given a vehicle with no photos
- When published
- Then return 422 with INVALID_IMAGES error

- Given a vehicle with portal that has no OAuth token
- When published
- Then return 422 with OAUTH_REQUIRED error

### Error Cases
- Given the ML API is down
- When the publish job runs
- Then retry 3 times with 60s backoff
- Then mark listing as "failed"
```

**Validation checkpoint:** Every use case has at least one happy path, one alternative path, and one error case. If any is missing, the use case is underspecified.

### Step 4 — Identify Gaps

Look for missing requirements:

- "What happens if...?" scenarios not covered
- Error cases not specified
- Boundary conditions not defined
- Concurrency scenarios not considered
- Data migration scenarios not addressed

Common gaps:
- No error flow for external API failures
- No specification for concurrent edits
- No data validation rules for edge cases
- No specification for data retention/deletion
- No performance requirements for large datasets

### Step 5 — Recommend Test Strategy

Based on the system's architecture and complexity:

- **Unit tests**: What should be tested in isolation? (services, validators, entities)
- **Integration tests**: What needs real dependencies? (database, external APIs)
- **E2E tests**: What flows need full system testing? (critical user journeys)
- **Performance tests**: What needs load testing? (high-traffic endpoints)
- **Security tests**: What needs security testing? (auth, data exposure)

### Step 6 — Regression Test Candidates

From the behavioral analysis (Phase 8), identify behaviors that need regression tests:

- Defaults that must not change
- Side effects that must always happen
- Failure modes that must degrade gracefully
- Quirks that must not be "fixed"

**Validation checkpoint:** Every behavior from Phase 8 that could be accidentally changed has a regression test candidate.

## Constraints

### MUST DO

- Assess testability for every requirement (RF, RNF, RN)
- Flag every ambiguous term with suggested clarification
- Define test scenarios for every use case (happy + alternative + error)
- Identify gaps in requirements and communicate them
- Recommend a test strategy (unit, integration, E2E, performance, security)
- Identify regression test candidates from behavioral analysis

### MUST NOT DO

- Accept untestable requirements without reformulating them
- Skip error cases in test scenarios
- Assume edge cases are "obvious" — document them
- Recommend testing without considering the system's architecture
- Leave gaps uncommunicated — if something is missing, say so

## Good vs Bad Examples

**Bad testability:**
> RF-05: "The system must be user-friendly." — Not testable. What does "user-friendly" mean?

**Good testability:**
> RF-05: "A new user must be able to publish their first vehicle within 5 minutes, with no more than 3 clicks after login." — Testable with a usability test.

**Bad test scenario:**
> "Test that publishing works." — What does "works" mean?

**Good test scenario:**
> "Given a vehicle with valid data and active OAuth tokens for ML and OLX, when the user publishes, then two listings are created (one per portal), two jobs are dispatched, and the response contains the vehicle ID and both listing IDs."

**Bad gap:**
> No mention of what happens when two users edit the same vehicle simultaneously.

**Good gap identification:**
> "Gap: No specification for concurrent edits. Recommendation: Either implement optimistic locking (version field) or define last-write-wins behavior with audit trail."

## Completion Criteria

The analysis is complete when:

- [ ] Every requirement has a testability assessment
- [ ] All ambiguities are identified with suggested clarifications
- [ ] Test scenarios exist for all use cases
- [ ] Gaps are documented and communicated to the user
- [ ] Test strategy is recommended
- [ ] Regression test candidates are identified
- [ ] The user has reviewed and addressed the ambiguities

## Tips

- **If you can't test it, it's not a requirement**: Vague requirements lead to disputes about whether the system is correct
- **Test the negatives**: It's as important to test what the system should NOT do as what it should
- **Boundary values**: Test at the edges (0, 1, max, max+1) — that's where bugs live
- **State transitions**: Test every valid transition and every invalid one
- **Idempotency**: Test that repeating the same operation produces the same result
