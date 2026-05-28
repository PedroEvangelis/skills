# Build Mode

## Purpose

Entrega eficiente de features com domínio já estabelecido. BUILD mode pula a descoberta profunda (CP0-CP1) e vai direto para especificação e implementação.

BUILD mode responde à pergunta: **"Sabemos o que fazer. Vamos fazer."**

## When to Use

| Cenário | Ação |
|---------|------|
| `.specs/` existe com domínio mapeado | BUILD |
| Feature não introduz nova entidade core | BUILD |
| Descrição do usuário é clara e específica | BUILD |
| Usuário rejeitou DISCOVER mode | BUILD (com ressalva em STATE.md) |
| Feature simples (1-3 arquivos, 1 entidade) | BUILD |

**Gatilho de ativação:** O agente detecta automaticamente pelo contexto. Se o usuário descreve uma feature específica em um projeto com `.specs/` existente, assume BUILD a menos que haja indicadores de DISCOVER.

## What You Produce

```
.specs/features/<feature-name>/
├── spec.md
├── tasks.md (se necessário)
├── behaviors.md (seção reativa)
└── code (implementação)
```

## Workflow

### Step 1 — Carregar Contexto

Carregue APENAS:

1. `.specs/project/STATE.md` — estado da sessão anterior
2. `.specs/project/GLOSSARY.md` — termos do domínio (referência)
3. `.specs/project/CONVENTIONS.md` — padrões existentes
4. `.specs/project/BIG_PICTURE.md` — visão sistêmica (features existentes, entidades)

NÃO carregue ainda:
- ARCHITECTURE.md (só se precisar)
- VISION.md (só se o propósito for questionado)
- Features anteriores (só se houver cross-reference)

**Validation checkpoint:** STATE.md e GLOSSARY.md carregados.

### Step 2 — Verificar Impacto

Antes de criar a feature, consulte BIG_PICTURE.md:

- "Esta feature afeta features existentes?"
- "Ela introduz um termo que já existe no glossário com outro significado?"
- "Ela modifica uma entidade que outras features usam?"

Se o impacto for significativo:
- Carregue behaviors.md das features afetadas
- Considere mudar para DISCOVER mode se houver ambiguidade

**Validation checkpoint:** Impacto avaliado. Se for ambíguo ou alto, considere DISCOVER.

### Step 3 — Verificar Termos no GLOSSARY.md

Revise a descrição da feature e identifique termos novos ou conflitantes:

- Se um termo NÃO está no GLOSSARY.md → adicione
- Se um termo está mas com definição diferente → atualize ou questione o usuário
- Se não houver ambiguidade, use seu conhecimento de domínio para definir

**Validation checkpoint:** GLOSSARY.md atualizado com termos novos.

### Step 4 — Criar Estrutura da Feature

```bash
mkdir -p .specs/features/<feature-name>
```

### Step 5 — Especificar (CP2)

Siga `references/specify-feature.md` para criar `spec.md`.

A spec em BUILD mode é mais enxuta que em DISCOVER mode:
- Foco no que a feature faz, não no contexto do problema (já está mapeado)
- User stories diretas, sem exploração do domínio
- RFs com input/output diretos

**Validation checkpoint:** spec.md criado com gates de CP2 satisfeitos.

### Step 6 — Design (CP3, Opcional)

Só se a feature for média ou complexa. Features simples (1-3 arquivos) não precisam de design.md.

**Validation checkpoint:** design.md só existe se necessário.

### Step 7 — Behavioral Proativo (CP4, Opcional)

Só se a feature for média ou complexa. Features simples pulam CP4 e capturam comportamentos apenas na seção reativa.

**Validation checkpoint:** behaviors.md seção proativa só existe se necessário.

### Step 8 — Implementar (CP5)

Siga `references/implement-track.md` para implementar e gerar behaviors.md reativo.

**Validation checkpoint:** Código implementado. behaviors.md atualizado. Documentos globais atualizados.

## Auto-Sizing em BUILD Mode

| Complexidade | Artefatos | Checkpoints |
|-------------|-----------|-------------|
| 🟢 Simples (1-3 arquivos) | spec.md + behaviors.md (reativo) | CP2 + CP5 |
| 🟡 Média (4-8 arquivos) | spec.md + tasks.md + behaviors.md (dual) | CP2 + CP4 + CP5 |
| 🔴 Complexa | spec.md + design.md + tasks.md + behaviors.md (dual) + guide.md | CP2 + CP3 + CP4 + CP5 |

Se a feature começar como simples e crescer durante a implementação, adicione artefatos conforme necessário — não force toda a estrutura no início.

## MUST DO

- Carregar contexto mínimo (STATE.md + GLOSSARY.md + CONVENTIONS.md + BIG_PICTURE.md)
- Verificar BIG_PICTURE.md antes de começar
- Verificar GLOSSARY.md para termos novos ou conflitantes
- Criar spec.md — mesmo que mínima
- Atualizar behaviors.md após implementação

## MUST NOT DO

- Implementar sem spec.md
- Pular verificação de BIG_PICTURE.md
- Assumir que "já sabemos o que fazer" sem verificar o glossário
- Criar design.md para features simples
- Ignorar comportamentos descobertos durante implementação
- Assumir que BUILD mode é "spec rápida" — tecnologia ainda pertence ao design.md, não à spec.md

## Good vs Bad Examples

**Bom BUILD:**

```
Usuário: "Quero adicionar campo 'placa' no formulário de cadastro de veículo."

Agente carrega: STATE.md, GLOSSARY.md, CONVENTIONS.md, BIG_PICTURE.md.
GLOSSARY.md já tem "veículo" e "placa". OK.
BIG_PICTURE.md mostra que features/vehicle-registration/ existe.
Cria spec.md com 1 RF: "Adicionar campo placa ao formulário de veículo."
Implementa em 1 arquivo.
Atualiza behaviors.md: "B-004: Placa adicionada. Validação: formato ABC-1234."
```

**Mau BUILD:**

```
Usuário: "Quero adicionar campo 'placa' no formulário."

Agente: "Claro!" (não carrega STATE.md — perde estado da sessão anterior)
Agente: "Vou implementar." (não verifica BIG_PICTURE.md — pode conflitar com outra feature)
Agente: (implementa sem spec.md — ninguém sabe o que foi feito depois)
```
