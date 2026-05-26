# Disclosure Algorithm

## Purpose

Referência detalhada do algoritmo de progressive disclosure — como o agente decide o modo, o que carregar, quando carregar, e o que NÃO carregar.

O algoritmo é uma árvore de decisão que considera: estado do projeto, intenção do usuário, e complexidade da tarefa.

## Decision Tree

```
INÍCIO DA SESSÃO
│
├── .specs/ existe?
│   ├── NÃO → GREENFIELD
│   │   Modo: DISCOVER (via greenfield-init.md)
│   │   Carrega: NADA (vai criar)
│   │   Cria: project/{VISION,GLOSSARY,BIG_PICTURE,ARCHITECTURE,CONVENTIONS,STATE}.md
│   │
│   └── SIM → Qual a intenção?
│       │
│       ├── "Feature nova" → BROWNFIELD
│       │   ├── Carrega: STATE.md, GLOSSARY.md, CONVENTIONS.md, BIG_PICTURE.md
│       │   ├── Auto-sensing: DISCOVER vs BUILD?
│       │   │   ├── Entidade core nova? → DISCOVER (discover-mode.md)
│       │   │   ├── Descrição ambígua? → DISCOVER (discover-mode.md)
│       │   │   ├── Descrição clara, domínio mapeado? → BUILD (build-mode.md)
│       │   │   └── Em dúvida? → Pergunte ao usuário
│       │   └── SOB DEMANDA:
│       │       ├── ARCHITECTURE.md (só se precisar decidir arquitetura)
│       │       ├── VISION.md (só se precisar alinhar propósito)
│       │       └── features/*/behaviors.md (só se cross-referenciar)
│       │
│       ├── "Bug fix / Ajuste" → FIX
│       │   Carrega:
│       │   ├── OBRIGATÓRIO: STATE.md
│       │   ├── OBRIGATÓRIO: BIG_PICTURE.md
│       │   ├── OBRIGATÓRIO: behaviors.md da feature afetada
│       │   └── SOB DEMANDA:
│       │       └── CONVENTIONS.md (se precisar entender padrão)
│       │
│       ├── "Refatorar" → MAINTENANCE
│       │   Carrega:
│       │   ├── OBRIGATÓRIO: STATE.md
│       │   ├── OBRIGATÓRIO: BIG_PICTURE.md
│       │   ├── OBRIGATÓRIO: behaviors.md da(s) feature(s) afetada(s)
│       │   └── SOB DEMANDA:
│       │       ├── CONVENTIONS.md (se precisar entender padrões)
│       │       └── ARCHITECTURE.md (se precisar entender estrutura)
│       │
│       └── "Retomar sessão anterior" → CONTINUE
│           Carrega:
│           ├── OBRIGATÓRIO: STATE.md
│           ├── OBRIGATÓRIO: BIG_PICTURE.md
│           └── SOB DEMANDA:
│               └── Última feature trabalhada (spec.md + behaviors.md)
│
DURANTE A EXECUÇÃO (lazy load):
│
├── Preciso de um termo → busca específica em GLOSSARY.md (seção, não arquivo)
├── Preciso de um padrão → busca em CONVENTIONS.md (seção)
├── Preciso de arquitetura → busca seção relevante em ARCHITECTURE.md
├── Preciso de comportamentos → busca em behaviors.md da feature (seção)
├── Preciso de spec → busca em spec.md da feature (seção)
├── Preciso de design → busca em design.md da feature (seção)
├── Preciso de visão sistêmica → carrega BIG_PICTURE.md
└── Preciso de propósito → carrega VISION.md
```

## Regras de Carregamento

### OBRIGATÓRIO sempre

- `STATE.md` — estado da sessão anterior

### OBRIGATÓRIO por modo

| Modo | Obrigatório |
|------|-------------|
| GREENFIELD (DISCOVER) | Nada (vai criar) |
| BROWNFIELD (DISCOVER) | STATE.md, GLOSSARY.md, CONVENTIONS.md, BIG_PICTURE.md, GLOBAL_BEHAVIORS.md |
| BROWNFIELD (BUILD) | STATE.md, GLOSSARY.md, CONVENTIONS.md, BIG_PICTURE.md, GLOBAL_BEHAVIORS.md |
| FIX | STATE.md, BIG_PICTURE.md, behaviors.md da feature afetada |
| MAINTENANCE | STATE.md, BIG_PICTURE.md, behaviors.md da(s) feature(s) afetada(s) |
| CONTINUE | STATE.md, BIG_PICTURE.md |

### SOB DEMANDA (nunca carregar sem motivo)

| Documento | Quando carregar |
|-----------|----------------|
| `ARCHITECTURE.md` | Só se for tomar decisão arquitetural |
| `VISION.md` | Só se o propósito do projeto for questionado |
| `GLOBAL_BEHAVIORS.md` | OBRIGATÓRIO em DISCOVER e BUILD (se existir). FIX: sob demanda |
| `features/*/behaviors.md` | Só se houver cross-reference explícito |
| `features/*/design.md` | Só se for implementar aquela feature |
| `features/*/guide.md` | Só se for dar manutenção naquela feature |

### NUNCA carregar

- Documentos de features arquivadas (a menos que explicitamente referenciados)
- Múltiplas features simultaneamente (a menos que cross-referenciadas)
- Documentos que não existem (verificar antes)

## Navegação no Grafo

Quando uma cross-reference é encontrada:

```
spec.md diz: "Ver [GLOSSARY.md#frete]"
→ O agente carrega APENAS a heading "## Frete" do GLOSSARY.md
→ NUNCA carrega o GLOSSARY.md inteiro
```

Isso vale para todos os documentos: carregue apenas a seção referenciada.

Exceção: BIG_PICTURE.md é pequeno o suficiente (<50 linhas) para ser carregado
por inteiro sempre que necessário.

## Pós-Implementação — Atualizações Obrigatórias

APÓS CADA IMPLEMENTAÇÃO (automático, sem perguntar):

```
1. behaviors.md da feature ← SEMPRE atualizar (seção reativa)
2. STATE.md ← SEMPRE atualizar
3. BIG_PICTURE.md ← SEMPRE atualizar (feature status, entidades, decisões)
4. GLOBAL_BEHAVIORS.md ← SE comportamento cross-cutting descoberto
5. CONVENTIONS.md ← SE padrão novo descoberto
6. ARCHITECTURE.md ← SE decisão arquitetural
7. GLOSSARY.md ← SE termo novo
```

## Tamanho vs Completude

Diferente da versão anterior, não há "tamanhos esperados" ou limites de linhas.
O que determina a saúde de um documento é a completude do gate, não seu tamanho.

Se um documento parece "grande demais", verifique extraction-rules.md:
- Uma seção é referenciada por 3+ arquivos? → Extraia.
- Uma seção tem ciclo de vida independente? → Extraia.
- Nenhum dos acima? → O tamanho é apropriado para a complexidade do projeto.
