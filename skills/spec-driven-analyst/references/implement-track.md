# Implement Track

## Purpose

Guiar a implementação de uma feature e, **automaticamente**, gerar e manter
o `behaviors.md` com os comportamentos descobertos durante o desenvolvimento.
Este é o coração da documentação viva.

## What You Produce

- Código da feature implementado
- `.specs/features/<feature-name>/behaviors.md` atualizado (auto-gerado)
- `.specs/project/CONVENTIONS.md` atualizado (se padrão novo descoberto)
- `.specs/project/GLOSSARY.md` atualizado (se termo novo)
- `.specs/project/STATE.md` atualizado

## Input

- `spec.md` da feature (obrigatório)
- `tasks.md` da feature (se existir)
- `design.md` da feature (se existir)

## Workflow

### Step 1 — Carregar Contexto de Implementação

Carregue APENAS:

1. `spec.md` da feature — o que precisa ser construído
2. `tasks.md` da feature — plano de execução (se existir)
3. `design.md` da feature — decisões técnicas (se existir)
4. `.specs/project/CONVENTIONS.md` — padrões a seguir

**Validation checkpoint:** spec.md está carregado. Sem spec, não implemente —
volte para specify-feature.md.

### Step 2 — Executar Tarefas (ou Implementar Direto)

Se `tasks.md` existe, siga as tarefas em ordem.

Se `tasks.md` não existe (feature simples), implemente diretamente seguindo
o fluxo: 1 arquivo por vez, validando após cada um.

### Step 3 — Detectar Comportamentos Não-Previstos

**Durante a implementação, esteja atento a:**

- Decisões de edge case que você precisou tomar
- Comportamentos implícitos que descobriu
- Side effects não documentados na spec
- Fallbacks e degradação que implementou
- Quirks intencionais
- Diferenças entre a spec e a implementação real

**Regra de ouro:** Se você teve que tomar uma decisão que não estava na spec,
isso é um comportamento a ser registrado.

### Step 4 — Atualizar behaviors.md (AUTO-GERADO)

Ao final de cada tarefa (ou ao final da implementação, se não houver tasks.md),
atualize `behaviors.md` da feature.

Use o template em `assets/behaviors-template.md`.

Para cada comportamento descoberto, registre:

```markdown
## B-001: [Título]
- **Descoberto em**: Tarefa [ID] — [nome da tarefa]
- **Comportamento**: [o que o sistema realmente faz]
- **Previsto na spec?** Sim | Não | Parcialmente
- **Por que não previsto**: [edge case, decisão, ambiguidade]
- **Teste relacionado**: [arquivo::função] (se aplicável)
- **Risco de regressão**: Alto | Médio | Baixo
```

**Validation checkpoint:** Toda decisão de implementação que afeta o comportamento
externo do sistema está registrada. Se você não tem nada a registrar, pense de novo.

### Step 5 — Verificar se Precisa Atualizar Docs Globais

Após implementar e registrar behaviors, verifique:

1. **CONVENTIONS.md**: A implementação usou um padrão novo não documentado?
   - Ex: "usamos Repository pattern para acesso a dados"
   - Se sim, adicione em CONVENTIONS.md

2. **GLOSSARY.md**: Surgiu um termo novo não documentado?
   - Ex: "peso volumétrico", "frete gratis"
   - Se sim, adicione em GLOSSARY.md

3. **ARCHITECTURE.md**: A implementação exigiu uma decisão arquitetural?
   - Ex: "escolhemos Redis em vez de cache em memória"
   - Se sim, crie um ADR em ARCHITECTURE.md

4. **STATE.md**: Atualize blockers resolvidos, lições aprendidas.

### Step 6 — Atualizar tasks.md (se existir)

Marque as tarefas concluídas como `[X]`.

## Auto-Generation — Gatilhos

O behaviors.md DEVE ser atualizado automaticamente, SEMPRE que:

| Gatilho | Ação |
|---|---|
| Tarefa de implementação concluída | Verificar e registrar comportamentos descobertos |
| Edge case resolvido durante codificação | Registrar imediatamente |
| Decisão de design tomada durante implementação | Registrar (mesmo que design.md exista) |
| Diferença entre spec e implementação | Registrar e considerar atualizar spec.md |
| Teste que revela comportamento não documentado | Registrar |

NUNCA pergunte "devo registrar isso no behaviors.md?". A resposta é sempre sim.

## MUST DO

- Atualizar behaviors.md ao final de CADA tarefa (automático, sem perguntar)
- Registrar comportamentos descobertos, não triviais ("implementei o que a spec mandou" não conta)
- Vincular cada comportamento ao teste que o valida (se existir)
- Verificar docs globais após implementar
- Atualizar STATE.md ao final

## MUST NOT DO

- Implementar sem ter a spec.md carregada
- Ignorar comportamentos descobertos ("é óbvio, não preciso documentar")
- Relaxar a atualização de behaviors.md porque a tarefa é "simples"
- Deixar STATE.md desatualizado

## Good vs Bad Examples

**Bom — comportamento registrado:**

Feature: "O usuário pode se cadastrar com email e senha."

Durante implementação, você descobre que:
- Se o email já existe, retorna 409
- A senha deve ter no mínimo 8 caracteres
- Após cadastro, dispara email de confirmação

```markdown
## B-001: Email duplicado retorna 409
- **Descoberto em**: Tarefa T003 — UserService.create
- **Comportamento**: Se email já existe no banco, retorna HTTP 409 CONFLICT
  com mensagem "Email já cadastrado". Não loga o evento (apenas 409 seco).
- **Previsto na spec?** Não — spec não mencionava tratamento de duplicidade
- **Teste relacionado**: tests/test_users.py::test_create_duplicate_email

## B-002: Senha com tamanho mínimo
- **Descoberto em**: Tarefa T003 — UserService.create
- **Comportamento**: Senha deve ter >= 8 caracteres. Se menor, retorna 422.
- **Previsto na spec?** Parcialmente — spec dizia "senha segura"
- **Teste relacionado**: tests/test_users.py::test_password_min_length

## B-003: Email de confirmação no cadastro
- **Descoberto em**: Tarefa T003 — UserService.create
- **Comportamento**: Após criar usuário, dispara email de confirmação
  via fila assíncrona (RabbitMQ). Se a fila está offline, o cadastro
  ainda é criado mas o email é perdido (logado para retentativa manual).
- **Previsto na spec?** Não — spec não mencionava notificação
- **Teste relacionado**: tests/test_users.py::test_welcome_email_dispatched
```

**Mau — comportamento perdido:**

Implementou as 3 descobertas acima mas não registrou nada no behaviors.md.
Três meses depois, um novo desenvolvedor refatora o UserService, remove a
validação de senha "sem querer", e ninguém sabe que era um comportamento
intencional.

## Completion Criteria

- [ ] Código implementado (ou tarefas marcadas como [X] em tasks.md)
- [ ] behaviors.md atualizado com todos os comportamentos descobertos
- [ ] CONVENTIONS.md atualizado se padrão novo
- [ ] GLOSSARY.md atualizado se termo novo
- [ ] ARCHITECTURE.md atualizado se decisão arquitetural
- [ ] STATE.md atualizado
