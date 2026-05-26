# Big Picture — [Nome do Projeto]

Índice navegável do sistema. Mantido automaticamente pelo agente.

## Entities

Entidades core do domínio e as features que as definem:

- **[Entidade 1]**: [descrição] → [features/feature-a/spec.md], [features/feature-b/spec.md]
- **[Entidade 2]**: [descrição] → [features/feature-c/spec.md]
- **[Entidade 3]**: [descrição] → [features/feature-d/spec.md]

## Feature Map

| Feature | Status | Modo | Entidades |
|---------|--------|------|-----------|
| [feature-a] | ✅ Done | DISCOVER | Entidade 1 |
| [feature-b] | 🔧 WIP | BUILD | Entidade 1, Entidade 3 |
| [feature-c] | 📋 Spec | BUILD | Entidade 2 |
| [feature-d] | 💡 Deferred | — | Entidade 3 |

## Decision Log

| ADR | Decisão | Feature de Origem |
|-----|---------|-------------------|
| [ADR-001] | [decisão resumida] | [features/feature-a/behaviors.md#B-002] |
| [ADR-002] | [decisão resumida] | [features/feature-b/design.md#DEC-01] |

## Global Behaviors

| Comportamento | Aplica-se a | Origem |
|---------------|-------------|--------|
| [Rate limiting: 120 req/min] | Todas as endpoints | [GLOBAL_BEHAVIORS.md#rate-limiting] |
| [Paginação: default 20, max 100] | Todas as listagens | [GLOBAL_BEHAVIORS.md#paginacao] |

## Open Questions

- [ ] [Pergunta pendente] — Contexto: [arquivo#seção] — Desde: YYYY-MM-DD
- [ ] [Pergunta pendente] — Contexto: [arquivo#seção] — Desde: YYYY-MM-DD
