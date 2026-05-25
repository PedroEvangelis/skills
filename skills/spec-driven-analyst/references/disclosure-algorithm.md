# Disclosure Algorithm

## Purpose

Referência detalhada do algoritmo de progressive disclosure — como o agente
decide o que carregar, quando carregar, e o que NÃO carregar.

## Decison Tree

```
INÍCIO DA Sessão
│
├── .specs/ existe?
│   ├── NÃO → GREENFIELD
│   │   Carrega: NADA (vai criar)
│   │   Cria: .specs/project/{VISION,GLOSSARY,ARCHITECTURE,CONVENTIONS,STATE}.md
│   │
│   └── SIM → Qual a intenção?
│       │
│       ├── "Feature nova" → BROWNFIELD
│       │   Carrega:
│       │   ├── OBRIGATÓRIO: STATE.md
│       │   ├── OBRIGATÓRIO: GLOSSARY.md (referência de termos)
│       │   ├── OBRIGATÓRIO: CONVENTIONS.md (padrões existentes)
│       │   └── SOB DEMANDA:
│       │       ├── ARCHITECTURE.md (só se precisar decidir arquitetura)
│       │       ├── VISION.md (só se precisar alinhar propósito)
│       │       └── features/*/behaviors.md (só se cross-referenciar)
│       │
│       ├── "Refatorar" → MAINTENANCE
│       │   Carrega:
│       │   ├── OBRIGATÓRIO: STATE.md
│       │   ├── OBRIGATÓRIO: behaviors.md da(s) feature(s) afetada(s)
│       │   └── SOB DEMANDA:
│       │       ├── CONVENTIONS.md (se precisar entender padrões)
│       │       └── ARCHITECTURE.md (se precisar entender estrutura)
│       │
│       ├── "Bug fix" → QUICK
│       │   Carrega:
│       │   ├── OBRIGATÓRIO: STATE.md
│       │   ├── OBRIGATÓRIO: spec.md da feature afetada
│       │   ├── OBRIGATÓRIO: behaviors.md da feature afetada
│       │   └── SOB DEMANDA:
│       │       └── CONVENTIONS.md (se precisar entender padrão)
│       │
│       └── "Ajuste rápido" → QUICK MODE
│           Carrega:
│           └── OBRIGATÓRIO: STATE.md
│
DURANTE A EXECUÇÃO (lazy load):
│
├── Preciso de um termo → busca específica em GLOSSARY.md
├── Preciso de um padrão → busca em CONVENTIONS.md
├── Preciso de arquitetura → busca seção relevante em ARCHITECTURE.md
├── Preciso de comportamentos → busca em behaviors.md da feature
├── Preciso de spec → busca em spec.md da feature
└── Preciso de design → busca em design.md da feature
```

## Regras de Carregamento

### OBRIGATÓRIO sempre

- `STATE.md` — estado da sessão anterior

### OBRIGATÓRIO por modo

| Modo | Obrigatório |
|---|---|
| Greenfield | Nada (vai criar) |
| Brownfield | STATE.md, GLOSSARY.md, CONVENTIONS.md |
| Maintenance | STATE.md, behaviors.md da feature |
| Quick (bug fix) | STATE.md, spec.md + behaviors.md da feature |
| Quick mode | STATE.md |

### SOB DEMANDA (nunca carregar sem motivo)

| Documento | Quando carregar |
|---|---|
| `ARCHITECTURE.md` | Só se for tomar decisão arquitetural |
| `VISION.md` | Só se o propósito do projeto for questionado |
| `features/*/behaviors.md` | Só se houver cross-reference explícito |
| `features/*/design.md` | Só se for implementar aquela feature |
| `features/*/guide.md` | Só se for dar manutenção naquela feature |

### NUNCA carregar

- Documentos de features arquivadas
- Múltiplas features simultaneamente (a menos que cross-referenciadas)
- Documentos que não existem (verificar antes)

## Pós-Implementação — Atualizações Obrigatórias

APÓS CADA IMPLEMENTAÇÃO (automático, sem perguntar):

```
1. behaviors.md da feature ← SEMPRE atualizar
2. STATE.md ← SEMPRE atualizar
3. CONVENTIONS.md ← SE padrão novo descoberto
4. ARCHITECTURE.md ← SE decisão arquitetural
5. GLOSSARY.md ← SE termo novo
```

## Limites de Contexto

| Documento | Tamanho esperado | Quando preocupa |
|---|---|---|
| STATE.md | <1K | >5K indica acúmulo — arquive decisões antigas |
| VISION.md | 5-15 linhas | >30 linhas está detalhando demais |
| GLOSSARY.md | 3-50 termos | >100 termos sem poda — arquive termos de features arquivadas |
| ARCHITECTURE.md | 1-20 ADRs | >20 ADRs sem arquivar — mova ADRs antigos para ARCHIVED.md |
| CONVENTIONS.md | 3-20 padrões | >20 padrões sem revisão — alguns podem estar obsoletos |
| spec.md | 20-100 linhas | >200 linhas sem design.md separado — mova detalhes para design.md |
| behaviors.md | 5-30 comportamentos | >30 sem revisão — arquive comportamentos de features estáveis |
