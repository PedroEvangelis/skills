# Phase 0 — Requirements Engineering

## Purpose

Extract the real need from the business problem and translate it into structured,
testable requirements. This is the foundation — everything downstream depends on it.

## What You Produce

| Document | Content |
|----------|---------|
| `01-vision.md` | Product vision, stakeholders, personas, success metrics |
| `02-srs.md` | Functional requirements (RF), non-functional (RNF), business rules (RN) |
| `03-user-stories.md` | User stories with acceptance criteria (BDD/Gherkin format) |
| `04-use-cases.md` | Detailed use cases with flows, pre/post-conditions, alternative paths |

## Input

The user's raw problem description — an idea, a pain point, a business need.

## Workflow

### Step 1 — Understand the Problem

Start by understanding the business context. Ask questions **one at a time** that reveal the real problem:

- "What problem are you trying to solve? What happens today without this software?"
- "Who experiences this problem? How do they cope right now?"
- "What would success look like? How would you measure it?"
- "What triggered this project now? What happens if you don't do it?"

Listen for pain points, workarounds, and inefficiencies. These often reveal requirements
that the user hasn't explicitly stated.

**Validation checkpoint:** You can articulate the problem in one sentence that the user agrees with. If you can't, keep asking.

### Step 2 — Identify Actors and Goals

Map out who interacts with the system and what they want:

- "Who will use this system? What role does each person play?"
- "What does each actor need to accomplish?"
- "Are there external systems that interact with this? (APIs, webhooks, data feeds)"
- "Who benefits from this? Who might be negatively affected?"

**Validation checkpoint:** Every actor has at least one clear goal. No actor exists without a purpose.

### Step 3 — Elicit Functional Requirements

For each actor-goal pair, derive what the system must do:

- "When [actor] needs to [goal], what should the system do?"
- "What information does the system need to receive? What should it produce?"
- "Are there triggers that start this process? (time-based, event-based, manual)"
- "What happens after? What's the next step?"

Classify each as a **Functional Requirement (RF)**:
- Number them sequentially: RF-01, RF-02, ...
- Include: description, priority (Must/Should/Could), input, output, notes

**Validation checkpoint:** Every RF has a clear actor, trigger, input, and output. If any is missing, the requirement is incomplete.

### Step 4 — Elicit Non-Functional Requirements

These define how the system behaves, not what it does:

- "How fast should this be? What's acceptable?"
- "How many users/transactions simultaneously?"
- "What about security? Encryption? Access control?"
- "What happens when something fails? Retry? Alert?"
- "Are there compliance requirements? (LGPD, HIPAA, PCI, etc.)"
- "What's the expected availability? (99%, 99.9%?)"

Classify each as a **Non-Functional Requirement (RNF)**:
- Number them: RNF-01, RNF-02, ...
- Include: description, metric (if measurable), notes

**Validation checkpoint:** Every RNF has a measurable metric. "Fast" is not acceptable — "<200ms for P95" is. If a metric truly can't be defined, document why.

### Step 5 — Elicit Business Rules

These are the company's premises that the software must respect:

- "Are there rules about who can do what? (permissions, approvals)"
- "Are there calculations or formulas the system must follow?"
- "Are there conditions that must be met before something happens?"
- "Are there regulatory or policy constraints?"

Classify each as a **Business Rule (RN)**:
- Number them: RN-01, RN-02, ...
- Include: description, which RF it relates to

### Step 6 — Write User Stories

For each significant requirement, write a user story with acceptance criteria:

```
As a [role], I want to [action] so that [benefit].

Acceptance Criteria:
- Given [context], when [action], then [expected result]
- Given [context], when [action], then [expected result]
```

Use BDD/Gherkin format for clarity. Each criterion should be testable.

### Step 7 — Write Use Cases

For complex flows, write detailed use cases:

```
## UC-XX: [Name]
**Actor:** [Primary actor]
**Preconditions:** [What must be true before]
**Main Flow:**
1. [Step]
2. [Step]
3. ...
**Alternative Flows:**
- At step X: [alternative]
**Postconditions:** [What is true after]
**Exceptions:**
- [What can go wrong and how it's handled]
```

## Constraints

### MUST DO

- Ask one question at a time — never overwhelm the user
- Force measurable metrics for every non-functional requirement
- Classify every requirement with MoSCoW priority (Must/Should/Could/Won't)
- Link every business rule (RN) to at least one functional requirement (RF)
- Include error flows and alternative paths in every use case
- Document every decision made during elicitation with date and rationale

### MUST NOT DO

- Accept vague terms like "fast", "secure", "intuitive" without metrics
- Write requirements that can't be tested
- Skip error flows — every use case must have exception handling
- Assume requirements are obvious — if it's not written, it doesn't exist
- Mix functional and non-functional requirements in the same section
- Use technical jargon when the domain has a simpler term

## Output Templates

### 01-vision.md
```markdown
# Product Vision — [Product Name]

## Problem Statement
[What problem are we solving?]

## Business Value
[Quantified value: time saved, cost reduced, revenue generated]

## Project Scope
### In Scope
- [...]
### Out of Scope
- [...]

## Stakeholders
| Role | Interest | Influence |
|------|----------|-----------|

## User Personas
| Persona | Role | Pain Points | Needs |
|---------|------|-------------|-------|

## Success Metrics
| Metric | Target | How Measured |
|--------|--------|--------------|
```

### 02-srs.md
```markdown
# Software Requirements Specification — [Product Name]

## Functional Requirements
### RF-01: [Title]
- **Description:** [...]
- **Priority:** Must/Should/Could
- **Input:** [...]
- **Output:** [...]
- **Notes:** [...]

## Non-Functional Requirements
### RNF-01: [Title]
- **Description:** [...]
- **Metric:** [...]
- **Priority:** Must/Should/Could

## Business Rules
### RN-01: [Title]
- **Description:** [...]
- **Related RF:** RF-XX
```

### 03-user-stories.md
```markdown
# User Stories — [Product Name]

### US-01: [Title]
As a [role], I want to [action] so that [benefit].

**Priority:** Must/Should/Could

**Acceptance Criteria:**
- Given [context], when [action], then [expected result]
```

### 04-use-cases.md
```markdown
# Use Cases — [Product Name]

## UC-01: [Name]
**Actor:** [Primary actor]
**Preconditions:** [...]
**Main Flow:**
1. [...]
**Alternative Flows:**
- At step X: [...]
**Postconditions:** [...]
**Exceptions:**
- [...]
```

## Good vs Bad Examples

**Bad requirement:**
> "The system must be fast."

**Good requirement:**
> "The system must respond to search queries in <200ms for the 95th percentile, measured under load of 100 concurrent users."

**Bad user story:**
> "As a user, I want to manage vehicles."

**Good user story:**
> "As a salesperson, I want to register a new vehicle with photos and pricing so that it can be published to ML and OLX within 5 minutes."

**Bad use case flow:**
> "The system publishes the vehicle."

**Good use case flow:**
> "1. System validates vehicle data (photos ≥ 1, price > 0, plate format).
> 2. System creates Listing records for each selected portal.
> 3. System dispatches PublishVehicleJob for each listing.
> 4. System returns 202 Accepted with vehicle ID and listing IDs."

## Completion Criteria

Before advancing to Phase 1, confirm:

- [ ] Every RF has a clear input, output, and priority
- [ ] Every RNF has a measurable metric (or explicit reason why it can't)
- [ ] Every RN is tied to at least one RF
- [ ] User stories cover all Must-have requirements
- [ ] Use cases exist for all complex multi-step flows
- [ ] No requirement is ambiguous or untestable
- [ ] Error flows are documented for every use case
- [ ] The user has reviewed and confirmed all documents

## Tips

- **Challenge assumptions**: "You said the system must X — what happens if it doesn't?"
- **Look for contradictions**: "RF-03 says X, but RN-02 says Y. How do they coexist?"
- **Find the edges**: "What's the maximum/minimum? What if there are none? What if there are thousands?"
- **External systems**: "Does this depend on another system? What if that system is down?"
- **Data ownership**: "Who creates this data? Who can modify it? Who can delete it?"
