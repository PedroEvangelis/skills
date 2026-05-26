# Implement Track

## Purpose

Guiar a implementação de uma feature e, **automaticamente**, gerar e manter o `behaviors.md` com os comportamentos descobertos durante o desenvolvimento. Implement Track é o CP5 — o checkpoint de implementação que fecha o ciclo.

Este é o coração da documentação viva: o behaviors.md dual (proativo + reativo).

## What You Produce

- Código da feature implementado
- `.specs/features/<feature-name>/behaviors.md` atualizado (seção reativa)
- `.specs/project/CONVENTIONS.md` atualizado (se padrão novo descoberto)
- `.specs/project/GLOSSARY.md` atualizado (se termo novo)
- `.specs/project/ARCHITECTURE.md` atualizado (se decisão arquitetural)
- `.specs/project/STATE.md` atualizado
- `.specs/project/BIG_PICTURE.md` atualizado

## Input

- `spec.md` da feature (obrigatório)
- `behaviors.md` da feature (seção proativa — preservar)
- `tasks.md` da feature (se existir)
- `design.md` da feature (se existir)
- `.specs/project/BIG_PICTURE.md` (para verificar impacto)

## Workflow

### Step 1 — Carregar Contexto de Implementação

Carregue APENAS:

1. `spec.md` da feature — o que precisa ser construído
2. `tasks.md` da feature — plano de execução (se existir)
3. `design.md` da feature — decisões técnicas (se existir)
4. `.specs/project/CONVENTIONS.md` — padrões a seguir
5. `.specs/project/BIG_PICTURE.md` — verificar impacto em outras features

**Validation checkpoint:** spec.md está carregado. Sem spec, não implemente — volte para specify-feature.md.

### Step 2 — Verificar se behaviors.md Proativo Existe

Se CP4 foi executado (DISCOVER mode ou BUILD mode complexo), o `behaviors.md` já tem uma seção `## Proactive Analysis`.

- Preserve esta seção — ela contém defaults, side effects e failure modes antecipados
- Durante implementação, verifique se os comportamentos proativos se confirmam
- Se um comportamento proativo se mostrou incorreto durante implementação, corrija-o

**Validation checkpoint:** Seção proativa do behaviors.md carregada e preservada.

### Step 3 — Executar Tarefas (ou Implementar Direto)

Se `tasks.md` existe, siga as tarefas em ordem.

Se `tasks.md` não existe (feature simples), implemente diretamente seguindo o fluxo: 1 arquivo por vez, validando após cada um.

### Step 4 — Detectar Comportamentos Não-Previstos

**Durante a implementação, esteja atento a:**

- Decisões de edge case que você precisou tomar
- Comportamentos implícitos que descobriu
- Side effects não documentados na spec nem na seção proativa
- Fallbacks e degradação que implementou
- Quirks intencionais
- Diferenças entre a spec e a implementação real

**Regra de ouro:** Se você teve que tomar uma decisão que não estava na spec nem no behaviors.md proativo, isso é um comportamento a ser registrado na seção reativa.

**Detecção de novos concerns:** Se durante a implementação você descobre que a feature toca um concern não declarado no frontmatter (ex: "essa feature precisa de cache"), atualize o frontmatter do behaviors.md imediatamente.

**Detecção de cross-cutting:** Se o comportamento descoberto se aplica a múltiplas features (ex: "descobrimos que toda API precisa de rate limiting"), registre em `GLOBAL_BEHAVIORS.md`, não no behaviors.md da feature.

### Step 5 — Atualizar behaviors.md (Seção Reativa)

Ao final de cada tarefa (ou ao final da implementação, se não houver tasks.md), atualize a seção `## Discovered During Implementation` do `behaviors.md`.

NUNCA sobrescreva a seção `## Proactive Analysis`.

```markdown
## Discovered During Implementation

### B-001: [Título]
- **Descoberto em**: Tarefa [ID] — [nome da tarefa]
- **Comportamento**: [o que o sistema realmente faz]
- **Previsto na spec?** Sim | Não | Parcialmente
- **Previsto na análise proativa?** Sim | Não
- **Por que não previsto**: [edge case, decisão, ambiguidade]
- **Teste relacionado**: [arquivo::função] (se aplicável)
- **Risco de regressão**: Alto | Médio | Baixo
```

**Validation checkpoint:** Toda decisão de implementação que afeta o comportamento externo do sistema está registrada.

### Step 6 — Atualizar behaviors.md (Seção Proativa, se necessário)

Se durante a implementação você descobriu que um comportamento proativo estava incorreto ou incompleto:

- Corrija a seção proativa (não mova para a reativa)
- Documente a correção na seção reativa como "B-XXX: Correção de análise proativa"

### Step 7 — Atualizar Documentos Globais

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

4. **GLOBAL_BEHAVIORS.md**: Um comportamento descoberto durante implementação é cross-cutting?
   - Ex: "toda endpoint precisa de rate limiting de 120 req/min"
   - Ex: "campos LGPD precisam de criptografia em todo o sistema"
    - Se sim, registre em GLOBAL_BEHAVIORS.md (não duplique em behaviors.md da feature)

5. **Frontmatter da feature**: Um concern novo foi descoberto durante implementação?
   - Ex: durante implementação, descobriu que a feature precisa de cache
   - Se sim, adicione `cache` ao `concerns` no frontmatter do behaviors.md

6. **BIG_PICTURE.md**: Atualize:
   - Feature status (✅ Done, 🔧 WIP, etc.)
   - Entidades (se a feature introduziu novas)
   - Decision Log (se ADR foi criado)
   - Open Questions (se alguma foi resolvida)

7. **STATE.md**: Atualize:
   - Blockers resolvidos
   - Lições aprendidas
   - Decisões pendentes
   - Preferências do usuário descobertas

### Step 8 — Atualizar tasks.md (se existir)

Marque as tarefas concluídas como `[X]`.

## Auto-Generation — Gatilhos

O behaviors.md DEVE ser atualizado automaticamente, SEMPRE que:

| Gatilho | Ação |
|---------|------|
| Tarefa de implementação concluída | Verificar e registrar comportamentos descobertos |
| Edge case resolvido durante codificação | Registrar imediatamente na seção reativa |
| Decisão de design tomada durante implementação | Registrar (mesmo que design.md exista) |
| Diferença entre spec e implementação | Registrar e considerar atualizar spec.md |
| Teste que revela comportamento não documentado | Registrar |
| Comportamento proativo se mostrou incorreto | Corrigir seção proativa + registrar na reativa |

NUNCA pergunte "devo registrar isso no behaviors.md?". A resposta é sempre sim.

## MUST DO

- Preservar a seção proativa do behaviors.md — nunca sobrescrever
- Atualizar behaviors.md (seção reativa) ao final de CADA tarefa (automático, sem perguntar)
- Registrar comportamentos descobertos, não triviais
- Vincular cada comportamento ao teste que o valida (se existir)
- Verificar docs globais após implementar (CONVENTIONS, GLOSSARY, ARCHITECTURE, BIG_PICTURE, STATE)
- Atualizar BIG_PICTURE.md sempre (feature status, entidades, decisões)

## MUST NOT DO

- Sobrescrever a seção proativa do behaviors.md com dados reativos
- Implementar sem ter a spec.md carregada
- Ignorar comportamentos descobertos ("é óbvio, não preciso documentar")
- Relaxar a atualização de behaviors.md porque a tarefa é "simples"
- Deixar BIG_PICTURE.md ou STATE.md desatualizados

## Good vs Bad Examples

**Bom — comportamento registrado:**

Feature: "O usuário pode se cadastrar com email e senha."

Durante implementação, você descobre que:
- Se o email já existe, retorna 409
- A senha deve ter no mínimo 8 caracteres
- Após cadastro, dispara email de confirmação

```markdown
## Proactive Analysis
(previsto em CP4 — preservado)

## Discovered During Implementation

### B-001: Email duplicado retorna 409
- **Descoberto em**: Tarefa T003 — UserService.create
- **Comportamento**: Se email já existe no banco, retorna HTTP 409 CONFLICT
  com mensagem "Email já cadastrado".
- **Previsto na spec?** Não — spec não mencionava tratamento de duplicidade
- **Previsto na análise proativa?** Sim — B-002 antecipava este comportamento
- **Teste relacionado**: tests/test_users.py::test_create_duplicate_email

### B-002: Senha com tamanho mínimo
- **Descoberto em**: Tarefa T003 — UserService.create
- **Comportamento**: Senha deve ter >= 8 caracteres. Se menor, retorna 422.
- **Previsto na spec?** Parcialmente — spec dizia "senha segura"
- **Previsto na análise proativa?** Não
- **Teste relacionado**: tests/test_users.py::test_password_min_length

### B-003: Email de confirmação no cadastro
- **Descoberto em**: Tarefa T003 — UserService.create
- **Comportamento**: Após criar usuário, dispara email de confirmação
  via fila assíncrona. Se a fila está offline, o cadastro ainda é criado
  mas o email é perdido (logado para retentativa manual).
- **Previsto na spec?** Não
- **Previsto na análise proativa?** Não
- **Teste relacionado**: tests/test_users.py::test_welcome_email_dispatched
```

**Mau — comportamento perdido:**

Implementou as 3 descobertas acima mas não registrou nada no behaviors.md.
Três meses depois, um novo desenvolvedor refatora o UserService, remove a
validação de senha "sem querer", e ninguém sabe que era um comportamento
intencional.

## Completion Criteria

- [ ] Código implementado (ou tarefas marcadas como [X] em tasks.md)
- [ ] behaviors.md atualizado — seção proativa preservada + seção reativa preenchida
- [ ] CONVENTIONS.md atualizado se padrão novo
- [ ] GLOSSARY.md atualizado se termo novo
- [ ] Frontmatter do behaviors.md atualizado com concerns descobertos
- [ ] GLOBAL_BEHAVIORS.md atualizado se comportamento cross-cutting descoberto
- [ ] ARCHITECTURE.md atualizado se decisão arquitetural
- [ ] BIG_PICTURE.md atualizado (feature status, entidades, decision log)
- [ ] STATE.md atualizado
