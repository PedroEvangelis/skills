# Phase 7 — Impact Analysis

## Purpose

Understand how the software will affect existing processes, people, and priorities.
This ensures the solution delivers real value and is adopted successfully.

## What You Produce

`impact-analysis.md` — A document containing:
- Organizational impact assessment
- Process changes (what's replaced, what's new)
- MoSCoW prioritization matrix
- Phased delivery roadmap (MVP → subsequent phases)

## Input

All previous phases, especially requirements (Phase 0) and architecture (Phase 6).

## Workflow

### Step 1 — Map Process Changes

For each existing process that the software touches:

- "How is this done today?" (As-Is)
- "How will it be done with the software?" (To-Be)
- "What steps are eliminated?"
- "What new steps are introduced?"
- "Who does less work? Who does more?"

Document the delta:
- **Eliminated**: Steps that no longer exist
- **Automated**: Steps done by the system instead of humans
- **New**: Steps that didn't exist before
- **Modified**: Steps that change but still exist

**Validation checkpoint:** Every process change has a clear owner (who is affected) and a clear benefit (why it's better). If a change has no benefit, question whether it's needed.

### Step 2 — Assess Organizational Impact

For each role affected:

- "How does this change their daily work?"
- "What skills do they need that they don't have today?"
- "What resistance might they have? Why?"
- "What training is needed?"

Impact categories:
- **High impact**: Role changes significantly, needs training
- **Medium impact**: Role changes somewhat, needs guidance
- **Low impact**: Role barely changes, minimal adjustment

### Step 3 — MoSCoW Prioritization

For each requirement, assign a priority:

- **Must have**: Non-negotiable. The system fails without it.
- **Should have**: Important but not critical. Workaround exists.
- **Could have**: Nice to have. Adds value but not essential.
- **Won't have (this phase)**: Explicitly out of scope for now.

Rules:
- Must-haves should be ~60% of the total (if everything is Must, nothing is)
- Every Must-have must have a clear reason why it's Must
- Won't-haves must be explicitly acknowledged, not silently ignored

Ask:
- "If we delivered everything except this, would the system be usable?"
- "Is there a workaround if this isn't ready on day one?"
- "What's the cost of delaying this to a later phase?"

**Validation checkpoint:** Must-haves are ≤60% of total requirements. If more, re-evaluate: which ones truly block the system from functioning?

### Step 4 — Define MVP

The Minimum Viable Product is the smallest set of Must-haves that delivers value:

- "What's the absolute minimum that solves the core problem?"
- "Can we deliver value with just these features?"
- "Who would use the MVP? Is it a real user or a test scenario?"

The MVP should:
- Solve the core problem (not all problems)
- Be usable by real users
- Be deliverable in a short timeframe (weeks, not months)
- Provide a foundation to build on

### Step 5 — Plan Phases

Organize delivery into phases:

```
Phase 1 (MVP): [Core features]
  - Deliverables: [...]
  - Users: [...]
  - Success criteria: [...]

Phase 2: [Next set of features]
  - Depends on: Phase 1
  - Deliverables: [...]

Phase 3: [Remaining features]
  - Depends on: Phase 2
  - Deliverables: [...]
```

Ask:
- "What must exist before we can build X?"
- "Can Phase 2 start before Phase 1 is complete?"
- "What's the risk of each phase?"

**Validation checkpoint:** Each phase produces working, usable software on its own. If a phase delivers nothing usable, it's too granular.

## Constraints

### MUST DO

- Document As-Is and To-Be for every affected process
- Assign MoSCoW priority to every requirement
- Define a clear MVP with success criteria
- Plan delivery phases with explicit dependencies
- Identify training needs for each affected role
- Explicitly document Won't-haves with rationale

### MUST NOT DO

- Make everything a Must-have (if 80% is Must, the prioritization is meaningless)
- Treat Won't-have as never (it means "not this phase")
- Define an MVP that doesn't solve the core problem
- Create phases that deliver nothing usable on their own
- Ignore resistance to change — surface it and plan for it
- Focus on cost-benefit analysis (out of scope for this phase)

## Good vs Bad Examples

**Bad prioritization:**
> 18 out of 22 requirements are Must-have. Nothing is prioritized.

**Good prioritization:**
> 12 Must-have (core publishing flow), 6 Should-have (webhooks, bulk import), 4 Could-have (analytics dashboard). Won't-have this phase: lead management, financing integration.

**Bad MVP:**
> "The MVP is just the login screen." — Doesn't solve the core problem (publishing vehicles).

**Good MVP:**
> "The MVP allows a user to register a vehicle and publish it to ML. No OLX, no webhooks, no bulk import — but the core value (publishing) works end to end."

## Completion Criteria

Before advancing to Phase 8, confirm:

- [ ] All process changes are documented (As-Is → To-Be)
- [ ] Impact on each role is assessed
- [ ] Every requirement has a MoSCoW priority
- [ ] MVP is defined and agreed upon
- [ ] Delivery phases are planned with dependencies
- [ ] The user agrees with the prioritization

## Tips

- **Must is not everything**: If 80% of requirements are Must, the prioritization is meaningless
- **Won't is not never**: It means "not this phase" — document why and when it might come
- **MVP is not a prototype**: It's a real, usable product, just with fewer features
- **Dependencies matter**: Some features must exist before others make sense
- **Stakeholder alignment**: Different stakeholders will prioritize differently — surface this
