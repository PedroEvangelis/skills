# Phase 8 — Behavioral Analysis

## Purpose

Discover and document the implicit behaviors of the system — those that go beyond
the explicit contract of requirements but are critical for correct implementation.

## What You Produce

`behavioral-analysis.md` — A document containing:
- Defaults for every configurable aspect
- Side effects for every operation
- Failure modes and degradation behavior
- Edge cases and their handling
- System quirks and non-obvious decisions
- Infrastructure dependencies and failure behavior

## Input

All previous phases, especially requirements (Phase 0), state machines (Phase 4), and flow diagrams (Phase 5).

## Workflow

### Step 1 — Defaults

For every aspect of the system that can be configured or has options:

- "If the user doesn't specify X, what should happen?"
- "What's the most common/safe value for this?"
- "Is there a sensible default that works for most cases?"

Examples:
- Pagination: default page size, default ordering
- Status: initial status when something is created
- Portals: which portals to publish to if not specified
- Timeouts: how long to wait before giving up
- Retry: how many times to retry on failure

**Validation checkpoint:** Every configurable aspect has a default. If there's no default, the system behavior is undefined — which is a bug waiting to happen.

### Step 2 — Side Effects

For every operation, identify what else happens beyond the obvious:

- "When the user creates X, what else happens?"
- "Does this trigger any background jobs?"
- "Does this send any notifications?"
- "Does this update any other data?"
- "Does this log anything?"

Document each operation as:
```
Operation: POST /vehicles
  Primary effect: Creates vehicle record
  Side effects:
    - Creates Listing records for each portal
    - Dispatches PublishVehicleJob for each listing
    - Logs audit entry (vehicle.created)
    - Fires VehicleCreated event
```

**Validation checkpoint:** Every operation has its side effects documented. If an operation has no side effects listed, ask: "Is that really true, or did we miss something?"

### Step 3 — Failure Modes

For every dependency and operation:

- "What happens if this external API is down?"
- "What happens if the database is slow?"
- "What happens if the queue is full?"
- "What happens if the user sends duplicate requests?"
- "What happens if the network times out mid-operation?"

For each failure mode, document:
- **Trigger**: What causes the failure
- **Detection**: How the system knows it failed
- **Response**: What the system does (retry, fail, degrade, alert)
- **User impact**: What the user sees
- **Recovery**: How the system returns to normal

**Validation checkpoint:** Every external dependency has at least one failure mode documented. If a dependency "never fails," that's an assumption — document it as such.

### Step 4 — Edge Cases

Think about unusual but possible scenarios:

- "What if there are zero items?" (empty collections)
- "What if there are 10,000 items?" (pagination, performance)
- "What if the same request is sent twice?" (idempotency)
- "What if the data is malformed?" (validation)
- "What if the user has no permissions?" (authorization)
- "What if the clock is wrong?" (time-based logic, token expiry)
- "What if two users edit the same thing simultaneously?" (concurrency)

### Step 5 — Quirks

Document behaviors that seem wrong but are intentional:

- "This looks like a bug but it's by design because..."
- "We return 402 instead of 400 here because..."
- "This field is called X in the API but Y in the database because..."

These are critical for new developers who might "fix" something that isn't broken.

### Step 6 — Infrastructure Dependencies

For each infrastructure component:

- **Required**: System cannot function without it
- **Degraded**: System works but with reduced functionality
- **Optional**: System works fully without it

```
Component: Redis
  Role: Queue + Cache
  If unavailable:
    - Queue: Jobs cannot be dispatched → 500 on publish
    - Cache: OAuth state validation fails → callback fails
  Mitigation: Use sync queue in development, Redis cluster in production
```

**Validation checkpoint:** Every infrastructure component has its failure behavior documented. If a component is "always available," that's an assumption — document it.

## Constraints

### MUST DO

- Document defaults for every configurable aspect
- Document side effects for every write operation
- Document failure modes for every external dependency
- Identify edge cases for boundary conditions (zero, one, many, max)
- Document quirks with rationale (why it's intentional)
- Map every infrastructure component with its failure behavior

### MUST NOT DO

- Assume defaults are "obvious" — if it's not documented, it will be debated
- Skip failure modes for "reliable" services — everything fails eventually
- Document quirks without rationale — without context, they look like bugs
- Assume idempotency without verifying — duplicate requests happen
- Ignore concurrency — simultaneous edits are common in real systems

## Good vs Bad Examples

**Bad default:**
> "The page size is whatever the user sends." — No limit, no default. A user can request 1 million records.

**Good default:**
> "Default page size is 20. Maximum is 100. If the user sends page_size > 100, clamp to 100. If page_size < 1, return error."

**Bad failure mode:**
> "If the ML API is down, the publish fails." — No retry, no fallback, no user communication.

**Good failure mode:**
> "If the ML API is down: retry 3 times with 60s backoff. If all retries fail, mark listing as 'failed' with error message. User can retry manually. System sends alert to admin."

**Bad edge case:**
> Not considering what happens when a user clicks "publish" twice rapidly.

**Good edge case:**
> "Publish endpoint is idempotent: if the same vehicle is published twice within 5 seconds, the second request returns the same listing IDs without creating duplicates."

## Completion Criteria

Before advancing to Phase 9, confirm:

- [ ] Every operation has documented defaults
- [ ] Every operation has documented side effects
- [ ] Every dependency has documented failure modes
- [ ] Edge cases are identified and handled
- [ ] Quirks are documented with rationale
- [ ] Infrastructure dependencies are mapped with degradation behavior

## Tips

- **Think in terms of 'what if'**: The best behavioral analysis comes from asking uncomfortable questions
- **Learn from production**: If there's an existing system, what has broken before? Why?
- **Document the 'why'**: A behavior without rationale looks like a bug to someone who wasn't there
- **Idempotency matters**: Any operation that can be retried should be idempotent
- **Graceful degradation**: Prefer partial functionality over total failure when possible
