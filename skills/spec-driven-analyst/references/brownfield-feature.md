# Brownfield Feature

## Purpose

Adicionar uma nova funcionalidade em um projeto que já possui `.specs/`. Diferente da abordagem anterior (que sempre usava o mesmo fluxo), o brownfield agora faz **auto-sensing** para determinar se a feature merece **DISCOVER mode** (deep dive no domínio) ou **BUILD mode** (entrega eficiente).

## What You Produce

Depende do modo selecionado:

| Modo | Artefatos |
|------|-----------|
| DISCOVER | spec.md + design.md* + behaviors.md (dual) + tasks.md* + guide.md* |
| BUILD | spec.md + behaviors.md (reativo) + code |

* = opcional, depende de auto-sizing

## Input

- `.specs/` existente no projeto alvo
- Descrição da feature desejada (pode ser vaga)

## Workflow

### Step 1 — Carregar Contexto Mínimo

Carregue APENAS:

1. `.specs/project/STATE.md` — estado da sessão anterior
2. `.specs/project/GLOSSARY.md` — termos do domínio (referência)
3. `.specs/project/CONVENTIONS.md` — padrões existentes
4. `.specs/project/BIG_PICTURE.md` — visão sistêmica

NÃO carregue ainda:
- ARCHITECTURE.md (só se precisar)
- VISION.md (só se o propósito for questionado)
- Features anteriores (só se houver cross-reference)

**Validation checkpoint:** Estado da sessão carregado. GLOSSARY.md disponível para consulta.

### Step 2 — Auto-Sensing: DISCOVER ou BUILD?

Analise a descrição da feature contra estes critérios:

| Indicador | Modo sugerido |
|-----------|---------------|
| Descrição vaga ou ambígua | DISCOVER |
| Feature introduz nova entidade core do domínio | DISCOVER |
| Feature cruza múltiplos subsistemas | DISCOVER |
| Feature modifica entidade que outras features usam | DISCOVER |
| Descrição clara e específica | BUILD |
| Feature não toca entidades existentes | BUILD |
| Feature é puramente técnica (migração, refatoração) | BUILD |
| Usuário diz explicitamente "é simples" | BUILD (mas verifique se realmente é) |

**Regra de ouro:** Se você está em dúvida, pergunte ao usuário:

> "Esta feature parece [simples/média/complexa]. Recomendo usarmos o modo [DISCOVER/BUILD]. DISCOVER faz uma análise mais profunda do domínio antes de implementar — são algumas perguntas rápidas. BUILD vai direto para a especificação. Qual prefere?"

Se o usuário recusar DISCOVER, registre em STATE.md e prossiga com BUILD.

**Validation checkpoint:** Modo determinado. Se DISCOVER, registre a decisão.

### Step 3 — Gerar Nome da Feature

Extraia 2-4 palavras da descrição do usuário:

| Descrição | Nome da Feature |
|---|---|
| "Quero autenticação com OAuth2" | `oauth2-auth` |
| "Precisamos de cálculo de frete por CEP" | `calculo-frete-cep` |
| "Adicionar notificação por email quando o pedido for criado" | `email-notification` |

Use kebab-case. Seja descritivo mas conciso.

**Validation checkpoint:** O nome reflete a essência da feature. O usuário reconhece o nome como correto.

### Step 4 — Verificar Termos no GLOSSARY.md

Revise a descrição da feature e identifique termos que podem ser novos ou conflitantes:

- Se um termo NÃO está no GLOSSARY.md → adicione
- Se um termo está mas com definição diferente → atualize ou crie sub-termo
- Se um termo tem o mesmo nome mas significado diferente em outra feature → desambigue

Não pergunte ao usuário para cada termo. Use seu conhecimento de domínio para definir. Só pergunte se houver ambiguidade real.

### Step 5 — Criar Estrutura da Feature

```bash
mkdir -p .specs/features/<feature-name>
```

### Step 6 — Executar o Modo Selecionado

#### Se DISCOVER:

Siga `references/discover-mode.md`:

- CP2: Requirements Elicitation (spec.md)
- CP3: Conceptual Design (se necessário)
- CP4: Proactive Behavioral Analysis (behaviors.md proativo)
- CP5: Implementation & Reactive Behaviors

#### Se BUILD:

Siga `references/build-mode.md`:

- CP2: Spec (spec.md enxuto)
- CP4: Behavioral Proativo (só se complexo)
- CP5: Implementation & Reactive Behaviors

## Auto-Sizing

Independente do modo, o auto-sizing adapta a profundidade:

| Complexidade | DISCOVER | BUILD |
|-------------|----------|-------|
| 🟢 Simples (1-3 arquivos) | CP2 + CP4 + CP5 | CP2 + CP5 |
| 🟡 Média (4-8 arquivos) | CP2 + CP3 + CP4 + CP5 | CP2 + CP4 + CP5 |
| 🔴 Complexa (módulos) | CP2 + CP3 + CP4 + CP5 + guide.md | CP2 + CP3 + CP4 + CP5 + guide.md |

## MUST DO

- Carregar BIG_PICTURE.md — essencial para auto-sensing
- Fazer auto-sensing (DISCOVER vs BUILD) antes de criar artefatos
- Verificar e atualizar GLOSSARY.md com termos novos
- Criar estrutura da feature antes de especificar
- Registrar a escolha do modo em STATE.md

## MUST NOT DO

- Assumir que toda brownfield feature é BUILD — algumas merecem DISCOVER
- Carregar documentos desnecessários (ARCHITECTURE.md, features anteriores sem cross-reference)
- Criar spec sem verificar o glossário primeiro
- Ignorar BIG_PICTURE.md — sem ele o auto-sensing é cego

## Good vs Bad Examples

**Bom Brownfield — auto-sensing correto:**

```
Usuário: "Quero adicionar autenticação OAuth2."

Agente carrega: STATE.md, GLOSSARY.md, CONVENTIONS.md, BIG_PICTURE.md.
GLOSSARY.md já tem "usuário". OK.
BIG_PICTURE.md mostra que nenhuma feature existente usa autenticação.
Feature não introduz entidade core (usuário já existe no glossário).
Descrição é clara e específica.

→ BUILD mode. Cria spec.md enxuto. Implementa. behaviors.md atualizado.
```

**Bom Brownfield — auto-sensing ativando DISCOVER:**

```
Usuário: "Quero adicionar cálculo de frete por CEP."

Agente carrega: STATE.md, GLOSSARY.md, CONVENTIONS.md, BIG_PICTURE.md.
GLOSSARY.md não tem "frete", "transportadora", "CEP".
BIG_PICTURE.md mostra que a feature "calculo-frete-cep" toca domínio não mapeado.

→ DISCOVER mode. CP2 descobre que frete tem regras complexas (frete grátis, peso
  volumétrico, múltiplas transportadoras). CP4 descobre failure modes (API dos
  Correios pode ficar offline). CP5 implementa com behaviors completo.
```

**Mau Brownfield:**

```
Usuário: "Quero adicionar cálculo de frete por CEP."

Agente: "É uma feature simples. Vou criar spec.md e implementar."
  (Pulou BIG_PICTURE.md — não viu que "frete" não estava no glossário)
  (Pulou auto-sensing — não percebeu que o domínio não estava mapeado)
  (Resultado: implementou sem entender regras de negócio de frete)
```

## Completion Criteria

- [ ] Contexto carregado (STATE.md, GLOSSARY.md, CONVENTIONS.md, BIG_PICTURE.md)
- [ ] Auto-sensing concluído — modo DISCOVER ou BUILD determinado
- [ ] Nome da feature definido e estrutura criada
- [ ] GLOSSARY.md atualizado com termos novos (se houver)
- [ ] Modo executado com checkpoints apropriados
- [ ] behaviors.md atualizado
- [ ] Documentos globais atualizados
