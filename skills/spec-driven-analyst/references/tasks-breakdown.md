# Tasks Breakdown

## Purpose

Quebrar uma feature em tarefas atômicas e ordenadas por dependência.
Só deve ser executado quando a feature tem complexidade suficiente para
justificar um plano formal.

## What You Produce

`.specs/features/<feature-name>/tasks.md`

## Input

- `spec.md` da feature
- `design.md` da feature (se existir)

## Workflow

### Step 1 — Decidir se tasks.md é Necessário

| Cenário | Precisa de tasks.md? |
|---|---|
| Feature simples (1-3 arquivos, 1 entidade) | NÃO — implemente direto |
| Feature média (vários arquivos, 2-3 entidades) | SIM |
| Feature complexa (módulos, decisões arquiteturais) | SIM |
| Feature com dependências entre componentes | SIM |

Se estiver em dúvida, NÃO crie. Comece a implementar. Se perceber que precisa
de organização, crie tasks.md durante a implementação.

### Step 2 — Extrair User Stories e Requisitos

Leia `spec.md` e liste:

- User stories com suas prioridades (US-01, US-02...)
- Requisitos funcionais (RF-01, RF-02...)
- Critérios de sucesso

Cada user story pode se tornar uma fase.

### Step 3 — Criar Tarefas

Cada tarefa segue este formato:

```markdown
- [ ] T001 [P] Criar modelo Frete em src/models/frete.py
- [ ] T002 [P] [US1] Implementar FreteService.calcular em src/services/frete.py
```

| Componente | Regra |
|---|---|
| `- [ ]` | Checkbox — marcar `[X]` quando concluída |
| `T001` | ID sequencial |
| `[P]` | Opcional. Incluir APENAS se a tarefa pode rodar em paralelo com outras |
| `[US1]` | Opcional. Vincular à user story (features simples podem pular) |
| Descrição | Ação + arquivo exato |

### Step 4 — Organizar por Fases

```
## Phase 1: Setup
- T001: [setup]
- T002: [setup]

## Phase 2: [US-01 Nome]
- T003: [tarefa]
- T004: [P] [tarefa paralelizável]

## Phase 3: [US-02 Nome]
- T005: [tarefa]
```

Fases são independentes e devem poder ser validadas isoladamente.

### Step 5 — Validar Dependências

Verifique:

- Tarefas com `[P]` realmente não dependem umas das outras?
- Tarefas sem `[P]` realmente precisam ser sequenciais?
- A ordem está correta? (setup → foundation → feature)

### Step 6 — Atualização Durante Implementação

O `tasks.md` é vivo:

- Tarefas concluídas: `[X]`
- Tarefas que se revelaram desnecessárias: remova ou marque como `[~]`
- Novas tarefas descobertas: adicione com ID sequencial

## MUST DO

- IDs sequenciais (T001, T002...)
- Descrição com ação + arquivo exato
- `[P]` só para tarefas realmente paralelizáveis
- `[X]` marca tarefas concluídas
- Organizar por fases com validação independente

## MUST NOT DO

- Criar tasks.md para features simples (1-3 arquivos)
- Incluir tarefas sem arquivo alvo
- Marcar `[P]` em tarefas que modificam os mesmos arquivos
- Deixar tarefas órfãs sem cross-reference para spec.md

## Good vs Bad Examples

**Bom tasks.md (feature média):**

```markdown
## Phase 1: Setup
- [ ] T001 Adicionar gem 'correios' ao Gemfile
- [ ] T002 [P] Criar configuração de transportadoras em config/transportadoras.yml

## Phase 2: US-01 — Consultar frete por CEP
- [ ] T003 [US1] Criar endpoint POST /api/frete/calcular em app/controllers/frete_controller.rb
- [ ] T004 [P] [US1] Implementar FreteService.calcular em app/services/frete_service.rb
- [ ] T005 [P] [US1] Criar validação de CEP em app/validators/cep_validator.rb

## Phase 3: US-02 — Fallback entre transportadoras
- [ ] T006 [US2] Implementar fallback para Correios em app/services/frete_service.rb
- [ ] T007 [US2] Adicionar teste de integração em spec/integration/frete_spec.rb
```

**Mau tasks.md:**

```markdown
- [ ] T001 Configurar projeto (vago — qual projeto? o que configurar?)
- [ ] T002 Fazer feature (vago — qual feature? quantas tarefas?)
- [ ] T003 Testar (vago — o que testar? como?)
```

## Completion Criteria

- [ ] tasks.md existe APENAS se necessário (feature média ou complexa)
- [ ] Toda tarefa tem ação + arquivo exato
- [ ] Tarefas `[P]` são realmente paralelizáveis
- [ ] Fases são validações independentes
- [ ] Cross-references para spec.md (US-*, RF-*)
