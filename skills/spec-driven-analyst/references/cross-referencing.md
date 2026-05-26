# Cross-Referencing

## Purpose

Padronizar como os documentos se referenciam entre si. Uma rede de referências navegável (um grafo) permite que o agente encontre contexto adicional sem carregar documentos completos desnecessariamente.

Cada documento é um **nó**. Cada cross-reference é uma **aresta**. O agente navega carregando apenas seções — nunca documentos inteiros.

## Formato Padrão

Use o formato `[arquivo#seção]` para referenciar:

```markdown
Ver [GLOSSARY.md#frete]
Ver [spec.md#rf-03]
Ver [behaviors.md#B-002]
Ver [CONVENTIONS.md#repository-pattern]
Ver [ARCHITECTURE.md#ADR-004]
Ver [features/calculo-frete-cep/spec.md#rf-05]
```

## BIG_PICTURE.md como Índice de Navegação

BIG_PICTURE.md é o ponto de entrada do grafo. Ele consolida:

```markdown
# Big Picture

## Entities
- Vehicle → features/vehicle-registration/spec.md
           → features/publish-to-portal/spec.md
           → GLOSSARY.md#vehicle
- Listing → features/publish-to-portal/spec.md
           → GLOSSARY.md#listing

## Decision Log
- ADR-004: Redis como cache → ARCHITECTURE.md#adr-004
  Descoberto em: features/calculo-frete-cep/behaviors.md#B-002
```

O agente carrega BIG_PICTURE.md quando precisa de visão sistêmica.
A partir dele, navega para nós específicos via cross-references.

## Navegação por Seção (Nunca por Arquivo Inteiro)

Quando o agente encontra uma cross-reference, ele DEVE carregar APENAS a seção referenciada:

```
Referência encontrada: [GLOSSARY.md#frete]
→ Carrega APENAS a linha "## Frete" e seu conteúdo (até a próxima heading)
→ NUNCA carrega o GLOSSARY.md inteiro

Referência encontrada: [spec.md#rf-03]
→ Carrega APENAS "### RF-03" e seu conteúdo
→ NUNCA carrega spec.md inteiro
```

**Exceção:** BIG_PICTURE.md pode ser carregado por inteiro (é sempre <50 linhas).

## Onde Referenciar O Quê

### No spec.md

```markdown
## Functional Requirements

### RF-03: Calcular frete por CEP
- O sistema deve consultar frete em múltiplas transportadoras
  (ver [GLOSSARY.md#frete], [GLOSSARY.md#transportadora])
```

### No tasks.md

```markdown
- [ ] T005 Implementar fallback para Correios ([spec.md#rf-05])
```

### No behaviors.md (seção proativa)

```markdown
## Proactive Analysis

### B-002: Cache de CEP
- [spec.md#rf-03] dizia "resposta rápida" sem especificar cache.
  Decidimos implementar cache Redis com TTL 5min antecipadamente.
```

### No behaviors.md (seção reativa)

```markdown
## Discovered During Implementation

### B-004: Timeout de API externa
- [spec.md#rf-03] dizia "consultar transportadora".
  Durante implementação, descobrimos que a API dos Correios timeout em 30s.
  Implementamos timeout de 10s com retentativa 3x.
```

### No guide.md

```markdown
## Padrão usado
- Strategy Pattern para transportadoras ([CONVENTIONS.md#strategy-pattern])
```

### Na ARCHITECTURE.md

```markdown
## ADR-004: Redis como cache
- Decisão tomada durante implementação de
  [features/calculo-frete-cep/behaviors.md#B-002]
```

### No GLOSSARY.md

```markdown
## Frete
Taxa cobrada pelo transporte de mercadorias.

*Introduzido por:* [features/calculo-frete-cep/spec.md]
```

## Regras

1. **Sempre referencie** quando mencionar um termo, requisito, decisão ou comportamento definido em outro documento.

2. **Use o nome do arquivo, não o path completo.** Exceção: quando referenciar outra feature, use o path relativo (`features/<name>/spec.md`).

3. **Especifique a seção** (`#seção` ou `#ID`). Nunca referencie um arquivo sem dizer onde está a informação.

4. **Navegação pelo agente**: quando o agente encontrar uma referência, ele DEVE carregar APENAS a seção referenciada — nunca o documento inteiro.

5. **Atualização**: se um documento for movido ou uma seção renomeada, todas as referências para ele devem ser atualizadas.

6. **BIG_PICTURE.md como orquestrador**: o BIG_PICTURE.md mantém o índice do grafo. Documentos que não estão no BIG_PICTURE.md são "nós órfãos" — considere movê-los para archived/ ou integrá-los.

7. **Frontmatter para consulta cross-feature**: spec.md, behaviors.md e design.md têm frontmatter declarando concerns. Use-o para consultas que cruzam features sem carregar todos os behaviors.md (ex: "quais features tocam LGPD?" → scan `concerns` nos frontmatters).

## GLOBAL_BEHAVIORS.md — Comportamentos Cross-Cutting

Quando um comportamento se aplica a múltiplas features, registre em GLOBAL_BEHAVIORS.md e referencie de cada feature afetada.

### No GLOBAL_BEHAVIORS.md

```markdown
## Rate Limiting
- Default: 120 requisições/minuto por API key
- Atinge: todas as endpoints públicas
- Origem: [ARCHITECTURE.md#adr-005]
- Features afetadas: [features/oauth2-auth/spec.md], [features/calculo-frete-cep/spec.md]
```

### No behaviors.md da feature

```markdown
## Proactive Analysis

### B-003: Limitação de requisições
- O comportamento de rate limiting é global, ver [GLOBAL_BEHAVIORS.md#rate-limiting]
- Exceção desta feature: o endpoint de webhook não tem rate limiting
```

### Regra

- Comportamento global → registre em GLOBAL_BEHAVIORS.md
- Exceção por feature → registre no behaviors.md da feature com cross-ref para GLOBAL_BEHAVIORS.md
- GLOBAL_BEHAVIORS.md mantém a lista de features afetadas atualizada

## Referências entre Features

Quando uma feature referencia outra:

```markdown
# Em features/email-notification/spec.md
## RF-02: Notificar no pedido
- Quando um pedido é criado, disparar email de confirmação.
  (Ver [features/calculo-frete-cep/spec.md#rf-03] — o cálculo de frete
  deve estar completo antes da notificação)
```

Use o path completo `features/<feature-name>/arquivo.md#seção`.

## Quando Extrair para Arquivo Próprio

Ver `references/extraction-rules.md` para as regras completas.

Resumo: se uma seção é referenciada por 3+ nós diferentes, considere extraí-la para arquivo próprio e atualizar todas as referências.

## Good vs Bad Examples

**Bom:**
```markdown
- Implementar validação de CEP conforme [spec.md#rf-02]
- Comportamento de timeout documentado em [behaviors.md#B-003]
- Termo definido em [GLOSSARY.md#frete]
```

**Mau (muito vago):**
```markdown
- Conforme documentado na spec (qual spec? qual seção?)
- Ver o glossary (qual termo?)
- Veja o behaviors (qual behavior?)
```
