# Brownfield Feature

## Purpose

Adicionar uma nova funcionalidade em um projeto que já possui `.specs/`.
Carrega o contexto mínimo necessário, cria a estrutura da feature, e guia
a especificação, design, tasks e implementação com progressive disclosure.

## What You Produce

```
.specs/features/<feature-name>/
├── spec.md       ← OBRIGATÓRIO
├── design.md     ← Opcional (só se feature complexa)
├── tasks.md      ← Opcional (só se >3 tarefas)
├── behaviors.md  ← Gerado durante implementação
└── guide.md      ← Opcional (só se padrão novo)
```

## Input

- `.specs/` existente no projeto alvo
- Descrição da feature desejada (pode ser vaga)

## Workflow

### Step 1 — Carregar Contexto Mínimo

Carregue APENAS:

1. `.specs/project/STATE.md` — estado da sessão anterior
2. `.specs/project/GLOSSARY.md` — termos do domínio (referência)
3. `.specs/project/CONVENTIONS.md` — padrões existentes

NÃO carregue ainda:
- `ARCHITECTURE.md` (só se precisar)
- `VISION.md` (só se o usuário perguntar sobre propósito)
- Features anteriores (só se houver cross-reference)

**Validation checkpoint:** STATE.md foi carregado. Se não existe, o projeto pode
não ter sido inicializado — execute greenfield-init.md primeiro.

### Step 2 — Gerar Nome da Feature

Extraia 2-4 palavras da descrição do usuário:

| Descrição | Nome da Feature |
|---|---|
| "Quero autenticação com OAuth2" | `oauth2-auth` |
| "Precisamos de cálculo de frete por CEP" | `calculo-frete-cep` |
| "Adicionar notificação por email quando o pedido for criado" | `email-notification` |

Use kebab-case. Seja descritivo mas conciso.

**Validation checkpoint:** O nome reflete a essência da feature. O usuário
reconhece o nome como correto.

### Step 3 — Verificar Termos no GLOSSARY.md

Revise a descrição da feature e identifique termos que podem ser novos:

- Se um termo da feature NÃO está no GLOSSARY.md → adicione
- Se um termo está mas com definição diferente → atualize ou crie sub-termo

Exemplo:
> Feature: "Cálculo de frete por CEP com peso volumétrico"
> - "frete" já está no glossário? Se sim, ok.
> - "peso volumétrico" é novo? Adicione.

Não pergunte ao usuário para cada termo. Use seu conhecimento de domínio para
definir. Só pergunte se houver ambiguidade real.

### Step 4 — Criar Estrutura da Feature

```bash
mkdir -p .specs/features/<feature-name>
```

Pronto para especificar. Siga `references/specify-feature.md`.

### Step 5 — Determinar Auto-Sizing

Baseado na descrição e complexidade estimada:

| Se a feature... | Então gere também |
|---|---|
| Envolve 1-3 arquivos, 1 entidade | Só spec.md + behaviors.md (depois) |
| Envolve vários arquivos, 2-3 entidades | spec.md + tasks.md + behaviors.md |
| Envolve decisões arquiteturais ou múltiplos módulos | spec.md + design.md + tasks.md + behaviors.md + guide.md |

Se estiver em dúvida, comece com o mínimo (spec.md) e pergunte:
"Esta feature parece [simples/média/complexa]. Vou começar com spec.md e
podemos adicionar design.md/tasks.md se necessário."

## MUST DO

- Carregar contexto mínimo (STATE.md + GLOSSARY.md + CONVENTIONS.md)
- Verificar e atualizar GLOSSARY.md com termos novos
- Criar estrutura da feature antes de especificar
- Determinar auto-sizing antes de gerar artefatos

## MUST NOT DO

- Carregar documentos desnecessários (ARCHITECTURE.md, features anteriores)
- Criar spec sem verificar o glossário primeiro
- Assumir que termos do usuário são os mesmos do glossário existente
- Gerar design.md para features simples

## Good vs Bad Examples

**Bom:**
```
Usuário: "Quero adicionar autenticação OAuth2."
Agente carrega: STATE.md, GLOSSARY.md, CONVENTIONS.md.
Glossário já tem "usuário". OK.
Cria features/oauth2-auth/.
Auto-sizing: simples (1-2 arquivos de middleware).
Só spec.md + behaviors.md.
```

**Mau:**
```
Usuário: "Quero adicionar autenticação OAuth2."
Agente carrega tudo: VISION.md, ARCHITECTURE.md, todas as features passadas,
análise de impacto, diagramas...
Contexto estourado para uma feature de 2 arquivos.
```

## Completion Criteria

- [ ] STATE.md, GLOSSARY.md e CONVENTIONS.md carregados
- [ ] Nome da feature definido e estrutura criada
- [ ] GLOSSARY.md atualizado com termos novos (se houver)
- [ ] Auto-sizing determinado
- [ ] Pronto para especificar (specify-feature.md)
