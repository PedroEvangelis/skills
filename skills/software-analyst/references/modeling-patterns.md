# Modeling Patterns and Anti-Patterns

Common patterns and pitfalls encountered during software analysis.

## Patterns

### 1. Canonical Model

**When**: Multiple external systems use different formats for the same concept.

**Solution**: Define an internal canonical model and translate at the boundaries.

```
External A → Adapter → Canonical Model ← Adapter ← External B
```

**Example**: Vehicle model that abstracts ML and OLX differences.

### 2. Status with History

**When**: You need to track not just the current status but how it changed.

**Solution**: Separate status table with timestamp and actor.

```
Entity (current_status) → StatusHistory (entity_id, from, to, at, by)
```

### 3. Soft Delete

**When**: Data must be "deleted" but recoverable or auditable.

**Solution**: `deleted_at` timestamp instead of actual deletion.

**Rule**: All queries must filter `WHERE deleted_at IS NULL`.

### 4. Polymorphic Association

**When**: One entity can belong to different types of parent entities.

**Solution**: `(parent_type, parent_id)` instead of a single foreign key.

**Caution**: Harder to enforce referential integrity at the database level.

### 5. Event Sourcing (Lightweight)

**When**: You need full history of changes, not just current state.

**Solution**: Store events (things that happened), derive state from events.

**Lightweight version**: Audit log + current state table.

### 6. Circuit Breaker

**When**: External API is unreliable and failures cascade.

**Solution**: Track failure count, stop calling after threshold, retry after cooldown.

```
Closed (normal) → Open (failing, reject calls) → Half-Open (test) → Closed
```

### 7. Idempotency Key

**When**: Operations can be retried and must not duplicate effects.

**Solution**: Client sends unique key, server checks if already processed.

### 8. Saga Pattern

**When**: A business transaction spans multiple services/systems.

**Solution**: Sequence of local transactions, each with a compensating action.

```
Step1 → Step2 → Step3
If Step3 fails: Compensate2 → Compensate1
```

## Anti-Patterns

### 1. God Class / God Entity

**Problem**: One entity/class has too many responsibilities (>15 attributes, >7 methods).

**Symptom**: "The Vehicle also stores the owner info, the listing config, the pricing rules..."

**Fix**: Split into focused entities with clear boundaries.

### 2. Anemic Domain Model

**Problem**: Entities are just data containers with no behavior.

**Symptom**: All logic is in services, entities have only getters and setters.

**Fix**: Move behavior to the entity that owns the data (Tell, Don't Ask).

### 3. Primitive Obsession

**Problem**: Using primitive types for domain concepts.

**Symptom**: `String status` instead of `ListingStatus` enum. `Decimal price` without currency.

**Fix**: Create value objects or enums for domain concepts.

### 4. Feature Envy

**Problem**: A method uses more data from another class than its own.

**Symptom**: `vehicleService.calculatePrice(vehicle)` uses only vehicle data.

**Fix**: Move the method to the class whose data it uses: `vehicle.calculatePrice()`.

### 5. Circular Dependency

**Problem**: A depends on B, B depends on A.

**Symptom**: "Vehicle needs Listing to know its status, Listing needs Vehicle to know its data."

**Fix**: Extract shared concept, use events, or introduce a mediator.

### 6. Big Ball of Mud

**Problem**: No clear layering or boundaries, everything depends on everything.

**Symptom**: "Where should I put this logic?" — "Just put it where it works."

**Fix**: Define layers and enforce dependency direction.

### 7. Speculative Generality

**Problem**: Building abstractions for requirements that don't exist yet.

**Symptom**: "We might need to support iCarros someday, so let's build the adapter pattern now."

**Fix**: YAGNI — build for today's requirements, refactor when the need arises.

### 8. Hidden Assumption

**Problem**: Requirements that are assumed but not stated.

**Symptom**: "Obviously the system would..." — "Wait, that's not in the requirements."

**Fix**: Surface every assumption during analysis. If it matters, document it.

## Decision Heuristics

| Question | Guideline |
|---|---|
| Should this be a separate entity? | If it has its own lifecycle, identity, and behavior — yes |
| Should this be embedded (JSON)? | If it's always accessed with the parent and has no independent queries — yes |
| Should this be an enum or a table? | If the values are fixed and known — enum. If they change and are managed — table |
| Should this be synchronous or async? | If the caller needs the result now — sync. If it can wait — async |
| Should this be a hard constraint or soft validation? | If violating it corrupts data — hard. If it's a business preference — soft |
| Should this be in the domain or application layer? | If it's a business rule — domain. If it's orchestration — application |
