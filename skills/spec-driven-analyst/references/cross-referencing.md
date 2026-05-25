# Cross-Referencing

## Purpose

Padronizar como os documentos se referenciam entre si. Uma rede de referências
navegável permite que o agente encontre contexto adicional sem carregar
documentos completos desnecessariamente.

## Formato Padrão

Use o formato `[arquivo#seção]` para referenciar:

```markdown
Ver [GLOSSARY.md#frete]
Ver [spec.md#rf-03]
Ver [behaviors.md#B-002]
Ver [CONVENTIONS.md#repository-pattern]
Ver [ARCHITECTURE.md#ADR-004]
```

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

### No behaviors.md

```markdown
## B-002: Cache de CEP
- [spec.md#rf-03] dizia "resposta rápida" sem especificar cache.
  Decidimos implementar cache Redis com TTL 5min.
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

1. **Sempre referencie** quando mencionar um termo, requisito, decisão ou
   comportamento definido em outro documento.

2. **Use o nome do arquivo, não o path completo.** Exceção: quando referenciar
   outra feature, use o path relativo (`features/<name>/spec.md`).

3. **Especifique a seção** (`#seção` ou `#ID`). Nunca referencie um arquivo
   sem dizer onde está a informação.

4. **Navegação pelo agente**: quando o agente encontrar uma referência, ele
   DEVE carregar APENAS a seção referenciada — nunca o documento inteiro.

5. **Atualização**: se um documento for movido ou uma seção renomeada, todas
   as referências para ele devem ser atualizadas.

## Good vs Bad Examples

**Bom:**
```markdown
- Implementar validação de CEP conforme [spec.md#rf-02]
- Comportamento de timeout documentado em [behaviors.md#B-003]
```

**Mau (muito vago):**
```markdown
- Conforme documentado na spec (qual spec? qual seção?)
- Ver o glossary (qual termo?)
```
