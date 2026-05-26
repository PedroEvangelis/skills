# FIX Mode

## Purpose

Lidar com tarefas rápidas (bug fix, ajuste ≤3 arquivos) sem gerar documentação
formal. O FIX mode é o "express lane" — pula especificação, design e tasks.
Apenas implementa e atualiza o mínimo necessário.

## What You Produce

Cenário padrão (com `.specs/` existente):
- Correção no código
- `.specs/project/STATE.md` atualizado
- `behaviors.md` da feature afetada atualizado (se aplicável)

Cenário sem `.specs/` (projeto sem documentação):
- `.specs/quick/NNN-slug/TASK.md`
- `.specs/quick/NNN-slug/SUMMARY.md`

## Input

- Descrição do problema ou ajuste
- Se `.specs/` existe: STATE.md + BIG_PICTURE.md + spec.md + behaviors.md da feature afetada

## Workflow

### Step 1 — Classificar a Tarefa

| É quick mode? | Condição |
|---|---|
| SIM | Bug fix em ≤3 arquivos |
| SIM | Ajuste de configuração |
| SIM | Refatoração sem mudança de comportamento |
| NÃO | Feature nova → BUILD (ou DISCOVER) |
| NÃO | Mudança que toca >3 arquivos → BUILD |
| NÃO | Decisão arquitetural → BUILD + Design |

**Validation checkpoint:** Realmente são ≤3 arquivos? Se a resposta for "sim,
mas..." então não é quick mode.

### Step 2 — Se `.specs/` Existe

1. Carregue APENAS `STATE.md` e `BIG_PICTURE.md`
2. Identifique a feature afetada (use BIG_PICTURE.md para ver impacto em outras features)
3. Carregue `behaviors.md` da feature (para não quebrar comportamentos existentes)
4. Implemente a correção
5. Atualize `behaviors.md` com o que foi descoberto/corrigido
6. Atualize `STATE.md` e `BIG_PICTURE.md`

### Step 3 — Se `.specs/` Não Existe

Crie `.specs/quick/` com:

```markdown
# TASK — [slug]

## O que precisa ser feito
[1-3 linhas]

## Arquivos afetados
- [arquivo 1]
- [arquivo 2]
```

Após implementar:

```markdown
# SUMMARY — [slug]

## O que foi feito
[1-3 linhas]

## Decisões tomadas
- [decisão 1]
- [decisão 2]
```

### Step 4 — Atualizar STATE.md

Registre no STATE.md:

- O que foi feito (1 linha)
- Se aprendeu algo, registre em "Lições Aprendidas"
- Se criou débito técnico, registre em "Deferred Ideas"

## MUST DO

- Confirmar que a tarefa realmente se qualifica como FIX mode (≤3 arquivos)
- Carregar BIG_PICTURE.md para verificar impacto em outras features
- Carregar behaviors.md da feature afetada para não quebrar comportamentos
- Atualizar behaviors.md se descobrir algo novo
- Atualizar STATE.md e BIG_PICTURE.md

## MUST NOT DO

- Usar FIX mode para features que exigem spec.md
- Ignorar behaviors.md existente ("só um bug fix, não preciso verificar")
- Deixar STATE.md ou BIG_PICTURE.md desatualizados
- Criar spec.md, design.md ou tasks.md em FIX mode

## Good vs Bad Examples

**Bom quick mode:**

```
Problema: "O cálculo de frete está retornando prazo negativo para CEPs do norte."

1. Carrega STATE.md
2. Carrega behaviors.md de calculo-frete-cep
3. Descobre que não há validação de CEPs remotos
4. Corrige: adiciona prazo mínimo de 1 dia útil
5. Atualiza behaviors.md: "B-004: CEPs remotos retornavam prazo negativo.
   Validação adicionada: prazo mínimo = 1 dia útil. Risco: Baixo."
6. Atualiza STATE.md
```

**Mau FIX mode:**

```
Problema: "O cálculo de frete está retornando prazo negativo para CEPs do norte."

1. Pula STATE.md (não vê o estado atual)
2. Pula BIG_PICTURE.md (não vê se outras features dependem desse cálculo)
3. Pula behaviors.md (não vê comportamentos existentes)
4. Corrige "na sorte" — pode quebrar algo
5. Não documenta a correção
6. STATE.md e BIG_PICTURE.md desatualizados
```

## Completion Criteria

- [ ] Correção implementada (≤3 arquivos)
- [ ] behaviors.md atualizado (se feature afetada existe)
- [ ] STATE.md atualizado
- [ ] BIG_PICTURE.md atualizado
- [ ] Nenhum artifact formal criado (spec.md, design.md, tasks.md)
