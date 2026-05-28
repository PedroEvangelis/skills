# Design Feature

## Purpose

Criar o `design.md` de uma feature complexa — documento que registra as decisões
técnicas, diagramas, modelo de dados e contratos de API. É opcional: só existe
para features que exigem decisões arquiteturais ou têm múltiplos módulos.

## What You Produce

`.specs/features/<feature-name>/design.md`

## Input

- `spec.md` da feature
- Carregar `.specs/project/CONVENTIONS.md` (padrões existentes)
- Carregar `.specs/project/ARCHITECTURE.md` (decisões existentes)
- Carregar `.specs/project/BIG_PICTURE.md` (visão sistêmica)

## Workflow

### Step 1 — Decidir se design.md é Necessário

Use estes critérios:

| Precisa de design.md? | Condição |
|---|---|
| NÃO | Feature de 1-3 arquivos, sem novas dependências, sem decisão arquitetural |
| SIM | Feature com >3 arquivos, nova dependência externa, nova entidade, mudança de arquitetura |
| SIM | Feature que expõe API pública, integra com sistema externo, ou tem fluxo assíncrono |
| SIM | Decisão com trade-offs reais (ex: "usar Redis vs banco relacional para cache") |

Se estiver em dúvida, NÃO crie design.md. Comece com spec.md + implementação.
Se durante a implementação surgir uma decisão complexa, registre em behaviors.md.

**Validation checkpoint:** A feature realmente precisa de design.md? Se a resposta
não for "sim, claramente", então não precisa.

### Step 2 — Identificar Decisões Técnicas

Liste as decisões que precisam ser tomadas:

- "Qual a melhor abordagem técnica para [requisito]?"
- "Quais as opções disponíveis?"
- "Quais os trade-offs de cada opção?"

Para cada decisão, apresente 2-3 opções com prós e contras. Deixe o usuário
escolher ou tome a decisão com justificativa.

**Tecnologia mencionada pelo usuário:** Se durante a descoberta (CP0-CP2) o usuário
mencionou tecnologia específica, revise-a agora. Pergunte (máximo 2):

- "Você tem experiência com [tecnologia] ou está avaliando?" (familiaridade)
- "Que alternativas considerou?" (trade-offs)
- "Há restrições de ambiente? (memória, SO, linguagem)" (limitações)

**Como documentar:**

| Contexto | Exemplo de documentação |
|----------|------------------------|
| Escolha por familiaridade | "Redis para cache — escolha por familiaridade da equipe. Alternativas: Dragonfly, Memcached. Decisão: Redis. Risco: baixo — tecnologia madura e bem suportada." |
| Escolha com trade-off real | "Dragonfly vs Redis — Dragonfly tem 2x throughput em benchmarks. Decisão: Redis. Motivo: Dragonfly exige migração de infra e conhecimento especializado. Trade-off: throughput menor em troca de operação previsível." |
| Tecnologia indispensável | "Typst para renderização de PDF — única alternativa viável que atende aos requisitos de tagging semântico e layout avançado. Alternativas consideradas: Adobe (proprietário, caro), LaTeX (curva de aprendizado alta). Decisão: Typst — melhor custo-benefício." |

**Validation checkpoint:** Toda decisão tem pelo menos 2 opções consideradas.
Se só há uma opção viável, documente por que as outras foram descartadas.

### Step 3 — Desenhar Diagramas (Se Aplicável)

Use Mermaid para diagramas. Só crie se o diagrama adicionar clareza:

- **Diagrama de sequência**: fluxos complexos com múltiplos atores
- **Diagrama de componentes**: arquitetura com múltiplos módulos
- **Diagrama de entidades**: modelo de dados com relacionamentos

**Validation checkpoint:** O diagrama comunica algo que o texto não comunica.
Se o texto já é suficiente, o diagrama é opcional.

### Step 4 — Definir Contratos de API (Se Aplicável)

Se a feature expõe endpoints:

```markdown
## API Contracts

### POST /api/frete/calcular

**Request:**
```json
{
  "cep_origem": "01001-000",
  "cep_destino": "20000-000",
  "peso_kg": 1.5,
  "dimensoes_cm": { "altura": 10, "largura": 20, "profundidade": 30 }
}
```

**Response 200:**
```json
{
  "transportadoras": [
    { "nome": "Correios", "valor": 25.90, "prazo_dias": 5 },
    { "nome": "Transportadora X", "valor": 32.50, "prazo_dias": 3 }
  ]
}
```

**Response 422:**
```json
{ "erro": "CEP inválido", "codigo": "INVALID_CEP" }
```
```

### Step 5 — Escrever o design.md

Use esta estrutura:

```markdown
# Design — [Feature Name]

## Technical Decisions

### DEC-01: [Título]
- **Contexto:** [o que levou a essa decisão]
- **Opções consideradas:**
  - Opção A: [descrição] — [prós/contras]
  - Opção B: [descrição] — [prós/contras]
- **Decisão:** [o que foi escolhido e por quê]

## Data Model (se aplicável)

[Entidades, atributos, relacionamentos]

## API Contracts (se aplicável)

[Endpoints, request/response]

## Diagrams (se aplicável)

[Diagramas Mermaid]
```

## MUST DO

- Só criar design.md se a feature realmente precisar
- Para cada decisão, documentar opções consideradas e trade-offs
- Decisões de tecnologia com rationale: contexto, alternativas, trade-offs, decisão (ver tabela em Step 2)
- Diagramas só se adicionarem clareza além do texto
- Contratos de API com exemplos reais (request + response)
- Cross-referenciar CONVENTIONS.md e ARCHITECTURE.md

## MUST NOT DO

- Criar design.md para features simples (spec.md + implementação é suficiente)
- Incluir diagramas que ninguém vai consultar
- Deixar decisões sem justificativa — nem as "óbvias"
- Duplicar informação que já está em CONVENTIONS.md ou ARCHITECTURE.md
- Aceitar tecnologia como "a escolha óbvia" sem ao menos documentar por que é óbvia (ex: "Redis — padrão da indústria para cache, familiaridade da equipe")
- Incluir tecnologia na spec.md em vez do design.md

## Good vs Bad Examples

**Bom design.md:**

Feature: "Cálculo de frete com múltiplas transportadoras."

```markdown
## DEC-01: Estratégia de fallback entre transportadoras
- **Contexto:** Se a transportadora primária (Correios) falha, precisamos
  de uma alternativa.
- **Opções:**
  - A: Fallback síncrono — tenta Correios, se falha, tenta próxima na mesma request
  - B: Fallback assíncrono — retorna erro e agenda retentativa com outra transportadora
  - C: Todas em paralelo — consulta todas simultaneamente, retorna a mais rápida
- **Decisão:** Opção C (paralelo). Justificativa: usuário não precisa esperar
  fallback sequencial. Custo: N consultas simultâneas em vez de 1.
```

**Mau design.md:**

```markdown
## DEC-01: Transportadora
Vamos usar Correios.
```
(Sem contexto, sem alternativas, sem justificativa. Parece arbritário.)

## Completion Criteria

- [ ] design.md existe APENAS se a feature realmente precisar
- [ ] design.md tem frontmatter com concerns da feature
- [ ] Toda decisão tem opções consideradas e justificativa
- [ ] Diagramas usam Mermaid (formato default, ver [assets/uml-notation-guide.md])
- [ ] Contratos de API (se existirem) têm exemplos
- [ ] Cross-references para CONVENTIONS.md, ARCHITECTURE.md e BIG_PICTURE.md
- [ ] Seções referenciadas por 3+ arquivos — considerar extração (ver [extraction-rules.md])
- [ ] Usuário revisou e aprovou o design
