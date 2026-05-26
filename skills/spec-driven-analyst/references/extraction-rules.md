# Extraction Rules

## Purpose

Regras para particionar documentos em nós menores. Extração preserva a navegabilidade do grafo — cross-references são atualizadas para manter a rede íntegra.

**Princípio fundamental:** Extração é sobre **padrão de referência e coesão**, não sobre tamanho do arquivo. Um arquivo de 300 linhas coeso e referenciado como um todo não deve ser particionado. Um arquivo de 50 linhas onde cada seção é referenciada por fontes diferentes pode merecer extração.

## Gatilhos para Extração

### Gatilho 1 — Seção referenciada por 3+ arquivos diferentes

Quando uma seção específica é referenciada por múltiplos arquivos, ela se torna um ponto de acoplamento. Extrair para arquivo próprio reduz a carga de navegação.

**Exemplo:**
```
ARCHITECTURE.md#ADR-004 é referenciado por:
- features/calculo-frete-cep/behaviors.md#B-002
- features/oauth2-auth/design.md#DEC-01
- features/email-notification/spec.md

→ ADR-004 merece ser extraído para ARCHITECTURE.md#adr-004 (continua no mesmo arquivo)
  Ou, se ADRs passam de 15, criar project/adrs/004-redis-cache.md
```

### Gatilho 2 — Seção mantida em ciclo de vida independente

Quando uma seção muda em ritmo diferente do resto do arquivo.

**Exemplo:**
```
ARCHITECTURE.md:
- ADR-001 a ADR-003: estáveis, não mudam há meses
- ADR-004 a ADR-008: mudam a cada sprint

→ ADRs recentes podem ficar. Se o arquivo passar de 20 ADRs,
  mova ADRs antigos para ARCHIVED.md.
```

### Gatilho 3 — Sub-estrutura rica que justifica diretório próprio

Quando uma seção tem sub-seções suficientes para merecer um diretório.

**Exemplo:**
```
project/VISION.md:
- ## Stakeholders (5 subseções)
- ## Personas (4 subseções)
- ## Market Context (3 subseções)
- ## Success Metrics (2 subseções)

→ Se stakeholders.md é referenciado por 3+ features, extraia.
  Se não, mantenha em VISION.md — a coesão do documento de visão
  é mais importante que o tamanho.
```

### Gatilho 4 — Contexto de manutenção separado

Quando duas seções do mesmo arquivo são mantidas por contextos diferentes.

**Exemplo:**
```
CONVENTIONS.md:
- ## Repository Pattern (padrão de código)
- ## Docker Config (padrão de infra)

→ Infra e código têm contextos de manutenção diferentes.
  Extraia Docker Config para project/infrastructure/docker.md
```

## O Que NÃO É Gatilho

| Falso gatilho | Por que não |
|---------------|-------------|
| "Esse arquivo está grande" | Tamanho não mede coesão. Um spec.md de 300 linhas para uma feature complexa é mais navegável que 5 arquivos de 60 linhas sem contexto. |
| "Prefiro arquivos pequenos" | Preferência pessoal não é critério arquitetural. |
| "Vamos extrair tudo para ser modular" | Modularidade prematura adiciona complexidade sem benefício. |
| "O template dizia X linhas" | Templates são pontos de partida, não limites. |

## Mecanismo de Extração

Ao extrair uma seção para arquivo próprio:

1. **Criar** o novo arquivo com o conteúdo da seção
2. **Manter referência** no arquivo original: `Ver [novo-arquivo.md#secao]`
3. **Atualizar** todas as referências existentes para apontar para o novo arquivo
4. **Verificar** se o BIG_PICTURE.md precisa ser atualizado

```
ANTES:
VISION.md#stakeholders referenciado por 3 features
  → cada feature carrega VISION.md inteiro

DEPOIS:
project/stakeholders.md criado
VISION.md: "Ver [stakeholders.md]"
3 features: atualizam referências para [stakeholders.md]
```

## Anti-Padrões

### Extração Prematura

```
"Sintoma: O VISION.md tem 15 seções, vou extrair todas para arquivos separados."
"Resultado: 15 arquivos de 10 linhas cada. Navegação piorou."
"Diagnóstico: Cada seção era referenciada por 1-2 arquivos.
 Extração não reduziu carga — só adicionou complexidade."
```

### Arquivo Ônibus

```
"Sintoma: Um arquivo tem 500 linhas e 0 referências externas."
"Resultado: Ninguém consulta. O conteúdo morreu."
"Diagnóstico: O problema não é o tamanho — é que o conteúdo não é referenciado.
 Extrair não vai ajudar. Revisar o conteúdo para torná-lo relevante, sim."
```

### Extração sem Atualização de Referências

```
"Sintoma: Extraiu seção para novo arquivo mas não atualizou as referências."
"Resultado: Referências quebradas. O grafo tem arestas para nós que não existem."
"Regra: Toda extração é seguida de atualização de cross-references."
```

## Regra de Ouro

**Extraia quando a navegação melhora, não quando o arquivo cresce.**

Pergunte:
- "Essa seção é consultada independentemente do resto do arquivo?"
- "Essa seção é referenciada por múltiplas fontes?"
- "Essa seção muda em ritmo diferente?"

Se a resposta for "sim" para qualquer uma, extraia.
Se for "não" para todas, mantenha onde está — independente do tamanho.
