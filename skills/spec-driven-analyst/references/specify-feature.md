# Specify Feature

## Purpose

Escrever o `spec.md` de uma feature — o documento que define O QUE a feature faz,
sem entrar em COMO será implementado. A spec é o contrato entre o analista e o
implementador.

## What You Produce

`.specs/features/<feature-name>/spec.md`

## Input

- Estrutura da feature já criada (`features/<feature-name>/`)
- Descrição da feature (do usuário + contexto carregado)
- Template: `assets/spec-template.md`

## Workflow

### Step 1 — Carregar o Template

Leia `assets/spec-template.md` como base.

### Step 2 — Extrair a Essência da Feature

Com base no que o usuário pediu, responda internamente:

1. "Qual o objetivo desta feature em 1 frase?"
2. "Quem usa? (atores)"
3. "Qual o fluxo principal? (3-5 passos)"
4. "O que pode dar errado? (pelo menos 2 cenários)"

**Validation checkpoint:** Você consegue explicar a feature em 30 segundos para
alguém que não participou da conversa. Se não consegue, refine.

### Step 3 — Identificar [NEEDS CLARIFICATION]

Máximo de **3 markers** por spec. Use apenas para questões que:

- Impactam significativamente o escopo ou experiência do usuário
- Têm múltiplas interpretações razoáveis com implicações diferentes
- Não têm um default razoável obvious

Exemplo de algo que NÃO precisa de clarification:
> "Qual banco de dados usar?" — Se o projeto já usa PostgreSQL, não pergunte.

Exemplo de algo que PRECISA:
> "O cálculo de frete deve incluir taxa de risco?" — Impacta preço final.
> Não tem default obvious sem conhecer o negócio.

**Validation checkpoint:** Máximo 3 NEEDS CLARIFICATION. Se você marcou mais,
priorize os 3 mais críticos e tome decisões para o resto.

### Step 4 — Escrever User Stories

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

**Validation checkpoint:** Toda user story tem pelo menos 2 acceptance criteria
(1 happy + 1 erro/alternativo).

### Step 5 — Escrever Functional Requirements

Formato:

```markdown
## Functional Requirements

### RF-01: [Título]
- **Descrição:** [o que o sistema deve fazer]
- **Input:** [o que entra]
- **Output:** [o que sai]
- **Prioridade:** Must | Should | Could
```

Mantenha cada RF atômica e testável.

**Validation checkpoint:** Cada RF-* tem input e output definidos. Se um RF não
tem output observável, não é um requisito funcional.

### Step 6 — Definir Critérios de Sucesso

```markdown
## Success Criteria

- [Critério mensurável 1]
- [Critério mensurável 2]
```

Critérios devem ser:
- **Mensuráveis**: "resposta em <500ms", não "rápido"
- **Tecnologia-agnósticos**: "usuário completa ação em <2min", não "API responde em <200ms"
- **Verificáveis**: pode ser testado por alguém sem acesso ao código

### Step 7 — Escrever a Spec

Preencha o template com o conteúdo dos passos 2-6.
Se houver [NEEDS CLARIFICATION], pergunte ao usuário **antes** de finalizar.

**Regra de perguntas:** Faça 1 pergunta por vez. Se houver 3 markers, são
no máximo 3 interações.

### Step 8 — Atualizar Glossário

Se a feature introduziu termos novos que não estão em `GLOSSARY.md`,
adicione-os agora.

## MUST DO

- Manter spec.md enxuto — foco no O QUE, não no COMO
- Cada RF deve ter input e output definidos
- Cada US deve ter happy path + pelo menos 1 alternativa/erro
- Máximo 3 NEEDS CLARIFICATION por spec
- Atualizar GLOSSARY.md com termos novos

## MUST NOT DO

- Incluir detalhes de implementação (frameworks, bibliotecas, APIs específicas)
- Escrever acceptance criteria vagos ("funciona corretamente", "é rápido")
- Deixar NEEDS CLARIFICATION sem resolver (pergunte ao usuário)
- Criar RFs que não podem ser testados
- Documentar comportamentos que não estão no escopo da feature

## Good vs Bad Examples

**Bom RF:**
```
RF-03: Calcular frete
- Input: CEP origem, CEP destino, peso (kg), dimensões (cm)
- Output: valor (R$), prazo (dias úteis), transportadora
- Prioridade: Must
```

**Mau RF:**
```
RF-03: Calcular frete
- O sistema calcula o frete. (Não diz: input? output? o que é "frete"?)
```

**Bom acceptance criterion:**
```
Given um CEP válido de origem e destino, when a loja consulta frete,
then o sistema retorna valor e prazo em <2 segundos.
```

**Mau acceptance criterion:**
```
Given um CEP, when consulta, then funciona. (O que é "funciona"?)
```

## Completion Criteria

- [ ] spec.md escrito seguindo o template
- [ ] No máximo 3 NEEDS CLARIFICATION (resolvidos ou perguntados)
- [ ] Toda RF tem input e output
- [ ] Toda US tem happy path + pelo menos 1 alternativa
- [ ] Critérios de sucesso são mensuráveis
- [ ] GLOSSARY.md atualizado com termos novos
- [ ] Usuário revisou e aprovou a spec
