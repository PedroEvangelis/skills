# Greenfield Init

## Purpose

Inicializar a documentação de um projeto novo. Cria a estrutura `.specs/project/`
com os documentos globais mínimos e prepara o terreno para a primeira feature.

## What You Produce

| Documento | Conteúdo |
|---|---|
| `.specs/project/VISION.md` | Propósito, stakeholders, métricas de sucesso (5-15 linhas) |
| `.specs/project/GLOSSARY.md` | Termos iniciais do domínio |
| `.specs/project/ARCHITECTURE.md` | Esqueleto vazio, pronto para ADRs |
| `.specs/project/CONVENTIONS.md` | Vazio, será preenchido com padrões descobertos |
| `.specs/project/STATE.md` | Estado inicial da sessão |

## Input

O usuário quer criar um projeto novo. Pode ou não ter uma descrição do propósito.

## Workflow

### Step 1 — Verificar se já existe `.specs/`

Se `.specs/` já existe no projeto alvo, mude para o modo Brownfield.
O usuário pode querer adicionar uma feature, não iniciar do zero.

**Validation checkpoint:** `.specs/` não existe. Se existir, aborta greenfield.

### Step 2 — Entender o Propósito

Faça **1 pergunta por vez**, no máximo 3 perguntas no total:

1. "Qual o propósito principal deste projeto? Em uma frase."
2. "Quem são os usuários/atores principais?"
3. "Qual a métrica de sucesso? (opcional — pode ser 'não definida ainda')"

Se o usuário já forneceu essas informações na mensagem inicial, não pergunte de novo.

**Validation checkpoint:** Você consegue resumir o projeto em 1 frase que o usuário
concorda. Se não consegue, continue perguntando.

### Step 3 — Criar `.specs/project/VISION.md`

Use este template:

```markdown
# Product Vision — [Nome do Projeto]

## Propósito
[1-2 frases]

## Atores
- [Ator 1]: [descrição]
- [Ator 2]: [descrição]

## Métricas de Sucesso
- [Métrica 1]: [alvo]
- [Métrica 2]: [alvo]

## Fora de Escopo (Inicial)
- [O que NÃO será construído agora]
```

Mínimo viável: 5 linhas. Máximo: 15 linhas. Não escreva um documento de visão de
páginas — isso será descoberto organicamente conforme as features forem construídas.

### Step 4 — Criar `.specs/project/GLOSSARY.md`

Extraia do propósito e atores os termos-chave do domínio:

```markdown
# Glossary

## [Termo]
[Definição concisa em 1-2 frases]
```

Comece com 3-5 termos. Novos termos serão adicionados conforme features forem
criadas.

**Validation checkpoint:** Todo termo usado no VISION.md está definido no GLOSSARY.md.
Se um termo do VISION.md não está no GLOSSARY.md, a definição está faltando.

### Step 5 — Criar `.specs/project/ARCHITECTURE.md`

```markdown
# Architecture

## ADRs
_Nenhuma decisão arquitetural ainda. Será preenchido conforme decisões forem tomadas._
```

Apenas o esqueleto. ADRs são adicionados quando decisões reais são tomadas durante
a implementação de features.

### Step 6 — Criar `.specs/project/CONVENTIONS.md`

```markdown
# Conventions

_Nenhum padrão definido ainda. Será preenchido conforme padrões forem descobertos._
```

### Step 7 — Criar `.specs/project/STATE.md`

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

## Preferências
- Modelo rápido preferido para: [a definir]
```

### Step 8 — Reportar Conclusão

Informe ao usuário:

- Estrutura `.specs/project/` criada
- Quantos termos no glossário
- Pronto para criar a primeira feature (guia: `references/brownfield-feature.md`)

## MUST DO

- Manter VISION.md enxuto (5-15 linhas)
- GLOSSARY.md começa pequeno e cresce organicamente
- ARCHITECTURE.md só recebe ADRs de decisões reais
- STATE.md é atualizado ao final de cada sessão

## MUST NOT DO

- Criar documentos de análise completos antes da primeira feature
- Documentar suposições de arquitetura como se fossem decisões
- Escrever VISION.md com mais de 15 linhas (se precisar, algo está errado)
- Adicionar termos ao GLOSSARY.md que não são usados em nenhuma feature

## Good vs Bad Examples

**Bom VISION.md (10 linhas):**
```markdown
# Product Vision — Integrador de Fretes

## Propósito
Integrar múltiplas transportadoras em uma API unificada para cálculo de frete.

## Atores
- Loja: consulta frete para seus clientes
- Admin: gerencia transportadoras e regras de negócio

## Métricas de Sucesso
- 99% de disponibilidade da API
- Resposta em <500ms para 95% das consultas
```

**Mau VISION.md (páginas):**
Documento de 3 páginas com análise de mercado, SWOT, personas detalhadas,
roadmap de 12 meses. Isso muda antes de ser usado.

## Completion Criteria

- [ ] `.specs/project/VISION.md` existe com propósito, atores e métricas
- [ ] `.specs/project/GLOSSARY.md` existe com 3-5 termos iniciais
- [ ] `.specs/project/ARCHITECTURE.md` existe (esqueleto)
- [ ] `.specs/project/CONVENTIONS.md` existe (esqueleto)
- [ ] `.specs/project/STATE.md` existe
- [ ] Estado atualizado em STATE.md
- [ ] Usuário confirmou que pode prosseguir para primeira feature
