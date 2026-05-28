# Specify Feature

## Purpose

Escrever o `spec.md` de uma feature — o documento que define **O QUE** a feature faz, com requisitos funcionais, não-funcionais, regras de negócio e critérios de aceitação.

A spec é o contrato entre análise e implementação. Em DISCOVER mode, ela emerge da conversa socrática (CP2). Em BUILD mode, ela é escrita diretamente a partir da descrição do usuário.

## What You Produce

`.specs/features/<feature-name>/spec.md`

## Input

- Estrutura da feature já criada (passo anterior do brownfield ou discover-mode)
- Descrição da feature (do usuário + contexto carregado)
- Template: `assets/spec-template.md`

## Workflow

### Step 1 — Carregar o Template

Leia `assets/spec-template.md` como base.

### Step 2 — Identificar Concerns

Com base na descrição, identifique os concerns que esta feature toca. Concerns são dimensões transversais do sistema, por exemplo:

- **auth**: autenticação, autorização, RBAC
- **performance**: rate limiting, cache, otimizações
- **lgpd**: dados pessoais, criptografia, retention
- **cache**: caching layer
- **compliance**: regulatório, auditoria, logs
- **observabilidade**: métricas, tracing, alertas
- **filas**: processamento assíncrono, job queues
- **notificações**: email, push, webhook
- **upload**: processamento de arquivos, storage

Nem toda feature tem concerns — features puramente técnicas (migração, refatoração) podem ter lista vazia.

Se houver dúvida, pergunte: "Essa feature toca [concern]?" Uma pergunta por vez.

**Roteamento de tecnologia:** se o usuário mencionar tecnologia específica durante a descrição (ex: "usar Redis para cache"), registre como decisão de design pendente — não inclua na spec. A tecnologia concreta vai para design.md.

**Validation checkpoint:** concerns identificados antes de escrever a spec. O frontmatter será preenchido com esta lista.

### Step 3 — Extrair a Essência da Feature

Com base no que o usuário pediu, responda internamente:

1. "Qual o objetivo desta feature em 1 frase?"
2. "Quem usa? (atores)"
3. "Qual o fluxo principal? (3-5 passos)"
4. "O que pode dar errado? (pelo menos 2 cenários)"

**Validation checkpoint:** Você consegue explicar a feature em 30 segundos para alguém que não participou da conversa.

### Step 4 — Identificar Pontos Ambíguos

Identifique aspectos que precisam de decisão do usuário. Limite a **3 pontos no máximo** por spec. Use apenas para questões que:

- Impactam significativamente a experiência do usuário
- Têm múltiplas interpretações razoáveis
- Não têm um default óbvio

**Validation checkpoint:** Máximo 3 pontos ambíguos. Se houver mais, priorize os 3 mais críticos.

### Step 5 — Escrever User Stories

Formato:

```markdown
## User Stories

### US-01: [Título]
As a [ator], I want to [ação] so that [benefício].

**Acceptance Criteria:**
- Given [contexto], when [ação], then [resultado esperado]
- Given [contexto alternativo], when [ação], then [resultado alternativo]
```

Cada user story deve ter:
- 1 fluxo principal (happy path)
- Pelo menos 1 fluxo alternativo ou de erro

**Validation checkpoint:** Toda user story tem pelo menos 2 acceptance criteria (1 happy + 1 erro/alternativo).

### Step 6 — Escrever Functional Requirements

```markdown
## Functional Requirements

### RF-01: [Título]
- **Description:** [o que o sistema deve fazer]
- **Input:** [o que entra]
- **Output:** [o que sai]
- **Priority:** Must | Should | Could
- **Cross-refs:** [GLOSSARY.md#termo], [US-01]
```

Mantenha cada RF atômica e testável.

**Validation checkpoint:** Cada RF tem input e output definidos. Se um RF não tem output observável, não é um requisito funcional.

### Step 7 — Escrever Non-Functional Requirements

```markdown
## Non-Functional Requirements

### RNF-01: [Título]
- **Description:** [o que o sistema deve garantir]
- **Metric:** [valor mensurável]
- **Priority:** Must | Should | Could
```

Se um RNF não tem métrica mensurável, documente a justificativa:

> **RNF-02: Segurança** — Metric: (não definida) — Justificativa: sistema single-user em rede local, sem dados sensíveis. Será revisado se houver mudança de contexto.

**Validation checkpoint:** Toda RNF tem métrica ou justificativa documentada.

### Step 8 — Escrever Business Rules

```markdown
## Business Rules

### RN-01: [Título]
- **Description:** [regra de negócio]
- **Related RF:** RF-XX
- **Rationale:** [por que essa regra existe]
```

Toda RN deve estar vinculada a pelo menos um RF.

**Validation checkpoint:** Toda RN tem RF vinculado.

### Step 9 — Definir Critérios de Sucesso

```markdown
## Success Criteria

- [Critério mensurável 1]
- [Critério mensurável 2]
```

Critérios devem ser:
- **Mensuráveis**: "resposta em <500ms", não "rápido"
- **Tecnologia-agnósticos**: "usuário completa ação em <2min", não "API responde em <200ms"
- **Verificáveis**: pode ser testado por alguém sem acesso ao código

### Step 10 — Escrever Fora de Escopo

```markdown
## Out of Scope

- [O que explicitamente NÃO faz parte desta feature]
- [O que será feito em outra feature]
```

### Step 11 — Resolver Pontos Ambíguos

Se houver pontos ambíguos identificados no Step 4, pergunte ao usuário **antes** de finalizar. Uma pergunta por vez.

### Step 12 — Atualizar Glossário

Se a feature introduziu termos novos que não estão em `GLOSSARY.md`, adicione-os agora.

## Template Completo da Spec

```markdown
---
type: spec
feature: <feature-name>
concerns: [auth, performance, lgpd, cache]
---

# Spec — [Feature Name]

## Description
[1-3 parágrafos descrevendo o objetivo da feature]

## User Stories
...

## Functional Requirements
...

## Non-Functional Requirements
...

## Business Rules
...

## Success Criteria
...

## Out of Scope
...

## Glossary Terms
- [Termo 1]: ver [GLOSSARY.md#termo-1]
```

## MUST DO

- Manter spec.md focado no O QUE, não no COMO
- Cada RF deve ter input e output definidos
- Cada US deve ter happy path + pelo menos 1 alternativa/erro
- Toda RNF tem métrica ou justificativa
- Toda RN vinculada a um RF
- Máximo 3 pontos ambíguos para perguntar ao usuário
- Atualizar GLOSSARY.md com termos novos
- Adicionar frontmatter com concerns identificados em Step 2

## MUST NOT DO

- Incluir tecnologia específica (Redis, Nginx, Laravel, bcrypt) na spec — use termos abstratos (cache, proxy, hash). A tecnologia concreta pertence ao design.md.
- Escrever acceptance criteria vagos ("funciona corretamente", "é rápido")
- Deixar pontos ambíguos sem resolver — pergunte ao usuário
- Criar RFs que não podem ser testados
- Pular RNFs — "não temos requisitos não-funcionais" é um requisito não-funcional em si

## Good vs Bad Examples

**Bom RF:**
```
RF-03: Calcular frete
- Input: CEP origem, CEP destino, peso (kg), dimensões (cm)
- Output: valor (R$), prazo (dias úteis), transportadora
- Priority: Must
```

**Mau RF (contaminado):**
```
RF-03: Calcular frete
- Input: CEP origem, CEP destino, peso (kg)
- Output: valor calculado via API dos Correios
  (Tecnologia específica (Correios) na spec. Se trocar de transportadora,
  a spec precisa ser alterada. O correto é "valor (R$), prazo (dias)")
```

**Bom RNF:**
```
RNF-01: Performance
- Metric: Resposta em <500ms para P95, medido sob carga de 50 requisições simultâneas.
- Priority: Must
```

**Mau RNF:**
```
RNF-01: Performance
- O sistema deve ser rápido.
  (O que é "rápido"?)
```

**Bom acceptance criterion:**
```
Given um CEP válido de origem e destino,
when a loja consulta frete,
then o sistema retorna valor e prazo em <2 segundos.
```

**Mau acceptance criterion:**
```
Given um CEP, when consulta, then funciona.
  (O que é "funciona"?)
```

## Completion Criteria

- [ ] spec.md escrito seguindo o template
- [ ] Máximo 3 pontos ambíguos (resolvidos ou perguntados)
- [ ] Toda RF tem input e output
- [ ] Toda RNF tem métrica ou justificativa
- [ ] Toda RN está vinculada a um RF
- [ ] Toda US tem happy path + pelo menos 1 alternativa
- [ ] Critérios de sucesso são mensuráveis
- [ ] GLOSSARY.md atualizado com termos novos
- [ ] Frontmatter preenchido com concerns identificados
- [ ] Usuário revisou e aprovou a spec
