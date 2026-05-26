# Greenfield Init

## Purpose

Inicializar a documentação de um projeto novo. Diferente da abordagem anterior (que criava 5 documentos rasos), o greenfield agora ativa o **DISCOVER mode** para uma jornada de descoberta do domínio antes de criar a primeira feature.

## What You Produce

```
.specs/project/
├── VISION.md                    ← Problema, atores, métricas (via CP0)
├── GLOSSARY.md                  ← Termos + relacionamentos (via CP1)
├── BIG_PICTURE.md               ← Entidades, feature map inicial (via CP1)
├── ARCHITECTURE.md              ← Esqueleto, pronto para ADRs
├── CONVENTIONS.md               ← Vazio, preenchido durante implementação
└── STATE.md                     ← Estado inicial da sessão
```

## Input

O usuário quer criar um projeto novo. Pode ou não ter uma descrição do propósito.

## Workflow

### Step 1 — Verificar se já existe `.specs/`

Se `.specs/` já existe no projeto alvo, mude para o modo Brownfield (ver `references/brownfield-feature.md`).

**Validation checkpoint:** `.specs/` não existe.

### Step 2 — Ativar DISCOVER Mode

O greenfield agora delega para o **DISCOVER mode** (ver `references/discover-mode.md`), que guia uma conversa socrática de descoberta.

O agente inicia com CP0:

> "Vamos entender o problema primeiro. O que acontece hoje sem esse software?"

Siga o workflow completo de DISCOVER mode para CP0 (Problem Discovery) e CP1 (Domain Discovery).

**Validation checkpoint:** CP0 e CP1 concluídos com gates satisfeitos.

### Step 3 — Criar VISION.md

Baseado na conversa de CP0, produza:

```markdown
# Product Vision — [Nome do Projeto]

## Problem Statement
[O que estamos resolvendo? 1-2 frases]

## Atores
- [Ator 1]: [descrição — quem é, o que precisa]
- [Ator 2]: [descrição]

## Success Metrics
- [Métrica 1]: [alvo]
- [Métrica 2]: [alvo]

## Out of Scope (Initial)
- [O que NÃO será construído agora]
```

O VISION.md deve ter o tamanho que precisar para cobrir o gate de completude. Não há limite de linhas. O gate verifica: problema declarado, atores identificados, métricas definidas, fora de escopo definido.

### Step 4 — Criar GLOSSARY.md

Baseado na conversa de CP1, produza:

```markdown
# Glossary

## [Termo 1]
[Definição concisa]

## [Termo 2]
[Definição concisa]
```

O glossário começa com os termos discutidos em CP1. Novos termos serão adicionados conforme features forem criadas.

### Step 5 — Criar BIG_PICTURE.md

```markdown
# Big Picture — [Nome do Projeto]

## Entities
- [Entidade 1]: descrição
- [Entidade 2]: descrição

## Feature Map
Ainda sem features. A primeira feature será mapeada aqui.

## Open Questions
- [Perguntas que ficaram em aberto durante a conversa]
```

### Step 6 — Criar Esqueletos

```markdown
# Architecture

## ADRs
_Nenhuma decisão arquitetural ainda. Será preenchido durante implementação._
```

```markdown
# Conventions

_Nenhum padrão definido ainda. Será preenchido durante implementação._

**Default:**
- Diagramas: Mermaid (compatível com GitHub, GitLab, Obsidian, VS Code)

```

```markdown
# Session State — [Data]

## Decisões Pendentes
- [nenhuma]

## Blockers
- [nenhum]

## Lições Aprendidas
- [nenhuma]

## Deferred Ideas
- [nenhuma]
```

### Step 7 — Reportar Conclusão e Planejar Primeira Feature

Informe ao usuário:

- Estrutura `.specs/project/` criada
- Quantos termos no glossário
- BIG_PICTURE.md disponível

Pergunte:

> "A estrutura inicial está pronta. Qual feature vamos construir primeiro?"

A primeira feature pode usar DISCOVER mode (recomendado) ou BUILD mode, dependendo da clareza do pedido.

## MUST DO

- Conduzir CP0 e CP1 como conversa socrática — uma pergunta por vez
- Verificar gates de completude antes de criar os documentos
- Criar BIG_PICTURE.md como índice navegável
- Manter ARCHITECTURE.md e CONVENTIONS.md como esqueletos — serão preenchidos organicamente

## MUST NOT DO

- Pular CP0 e CP1 — "já sabemos o que fazer" é o maior risco de greenfield
- Criar documentos de análise completos antes da primeira feature
- Documentar suposições de arquitetura como se fossem decisões
- Limitar VISION.md por número de linhas — o gate de completude é o que importa

## Good vs Bad Examples

**Bom Greenfield:**

```
Usuário: "Quero um sistema para gerenciar veículos e publicar em portais."

Agente (CP0): "O que acontece hoje sem esse sistema?"
Usuário: "Usamos planilhas. É um caos. Perdemos prazos."
Agente: "Quem sente essa dor?"
Usuário: "O comercial e o administrativo."

(CP0 gate satisfeito — problema claro)

Agente (CP1): "Quais são as 'coisas' principais que o sistema gerencia?"
Usuário: "Veículos, portais, listagens."
Agente: "Como um veículo se relaciona com uma listagem?"
Usuário: "Um veículo pode estar em vários portais."

(CP1 gate satisfeito — entidades mapeadas)

→ VISION.md, GLOSSARY.md, BIG_PICTURE.md criados
→ Pronto para primeira feature em DISCOVER ou BUILD
```

**Mau Greenfield:**

```
Usuário: "Quero um sistema para gerenciar veículos."

Agente: "Vou criar a estrutura." (pula CP0 e CP1)
Agente: "VISION.md pronto (5 linhas). GLOSSARY.md com 3 termos. Qual a primeira feature?"
Usuário: "Cadastro de veículos."
Agente: "Vamos implementar." (nunca entendeu o domínio — vai gerar retrabalho)
```

## Completion Criteria

- [ ] CP0 concluído: problema claro, atores identificados, métricas definidas
- [ ] CP1 concluído: entidades core mapeadas, termos definidos no glossário
- [ ] `.specs/project/VISION.md` existe (gate de completude satisfeito)
- [ ] `.specs/project/GLOSSARY.md` existe com termos da conversa
- [ ] `.specs/project/BIG_PICTURE.md` existe com entidades e feature map inicial
- [ ] `.specs/project/ARCHITECTURE.md` existe (esqueleto)
- [ ] `.specs/project/CONVENTIONS.md` existe (esqueleto)
- [ ] `.specs/project/STATE.md` existe
- [ ] Usuário confirmou que pode prosseguir para primeira feature
