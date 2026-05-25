# Evolve Architecture

## Purpose

Atualizar os documentos globais do projeto (ARCHITECTURE.md, CONVENTIONS.md,
GLOSSARY.md) quando novas decisões arquiteturais, padrões ou termos são
descobertos durante a implementação de features.

Este é o documento que garante que a arquitetura do projeto evolui organicamente,
sem exigir uma "fase de arquitetura" separada.

## What You Produce

- `.specs/project/ARCHITECTURE.md` atualizado (novo ADR)
- `.specs/project/CONVENTIONS.md` atualizado (novo padrão)
- `.specs/project/GLOSSARY.md` atualizado (novo termo)

## Input

- Comportamento descoberto durante implementação que indica decisão arquitetural
- Padrão de código identificado como recorrente
- Termo de domínio que apareceu pela primeira vez

## Workflow

Este reference é invocado por `implement-track.md` quando o agente detecta
que algo mudou no nível global. Não é um passo isolado — é parte do ciclo
de implementação.

### Gatilhos para ARCHITECTURE.md

Crie um ADR quando:

- Uma decisão técnica afeta múltiplas features
- Uma dependência externa é introduzida (banco, cache, fila, API)
- Um padrão arquitetural é escolhido (monolito vs módulos, sync vs async)
- Uma tecnologia é adicionada ao stack

Formato do ADR:

```markdown
## ADR-XXX: [Título]

**Data:** YYYY-MM-DD
**Contexto:** [O que levou a essa decisão]
**Decisão:** [O que foi decidido]
**Consequências:** [Impactos positivos e negativos]
**Feature relacionada:** [link para features/<feature>/design.md ou behaviors.md]
```

### Gatilhos para CONVENTIONS.md

Atualize quando:

- Um padrão de código é usado em 2+ features (ex: "sempre usar Repository pattern")
- Uma convenção de nomenclatura é estabelecida
- Um padrão de tratamento de erro é definido
- Uma estrutura de diretórios é padronizada

Formato:

```markdown
## [Padrão]

**Onde usar:** [contexto]
**Regra:** [descrição da convenção]
**Exemplo:**
```
[código exemplo]
```
**Origem:** Feature [link]
```

### Gatilhos para GLOSSARY.md

Atualize quando:

- Um termo de domínio aparece pela primeira vez
- Um termo existente ganha novo significado
- Uma abreviação ou sigla é introduzida

Formato:

```markdown
## [Termo]

[Definição concisa em 1-2 frases]

*Introduzido por:* [link para features/<feature>/spec.md]
```

## MUST DO

- Criar ADR apenas para decisões que afetam múltiplas features
- Registrar o link para a feature de origem em toda atualização
- Manter CONVENTIONS.md como registro de padrões REAIS (não desejados)
- Atualizar GLOSSARY.md sempre que um termo novo aparecer

## MUST NOT DO

- Criar ADR para decisões triviais ("usamos Express como framework web")
- Documentar padrões que não foram implementados em nenhuma feature
- Deixar termos órfãos no GLOSSARY.md sem referência para a feature de origem
- Acumular ADRs não relacionados — um ADR por decisão

## Good vs Bad Examples

**Bom ADR:**

```markdown
## ADR-004: Redis como cache de consultas de frete

**Data:** 2024-03-15
**Contexto:** Durante implementação da feature "calculo-frete-cep",
  percebemos que consultas repetidas ao mesmo CEP eram lentas (800ms-1.2s).
  A spec dizia "resposta em <500ms" sem especular sobre cache.
**Decisão:** Implementar cache Redis com TTL de 5 minutos para consultas de
  frete. Se Redis está indisponível, fallback para consulta direta sem cache.
**Consequências:** Positivo: respostas em cache em <50ms. Negativo: dados
  podem ficar desatualizados por até 5min. Dados frescos sempre consultam
  fontes externas.
**Feature relacionada:** [features/calculo-frete-cep/behaviors.md#B-002]
```

**Mau ADR:**

```markdown
## ADR-004: Cache

Vamos usar Redis porque é popular.
```
(Sem contexto, sem alternativa considerada, sem consequências.)

## Completion Criteria

- [ ] ADR criado APENAS para decisões que afetam múltiplas features
- [ ] CONVENTIONS.md atualizado APENAS com padrões implementados em 2+ features
- [ ] GLOSSARY.md atualizado com novos termos
- [ ] Toda atualização tem link para a feature de origem
