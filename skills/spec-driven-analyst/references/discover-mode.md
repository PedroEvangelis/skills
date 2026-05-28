# Discover Mode

## Purpose

A jornada completa de design thinking. Guia o agente por uma conversa socrática de descoberta — uma pergunta por vez, construindo entendimento compartilhado com o usuário antes de qualquer código.

DISCOVER mode responde à pergunta: **"O que estamos construindo e por quê?"**

## When to Use

| Cenário | Ação |
|---------|------|
| Projeto novo (`.specs/` não existe) | DISCOVER (via greenfield-init.md) |
| Feature que introduz nova entidade core do domínio | DISCOVER |
| Feature com requisitos ambíguos ou descrição vaga | DISCOVER |
| Usuário diz "preciso analisar", "vamos planejar" | DISCOVER |
| Feature cruza múltiplos subsistemas | DISCOVER |

**Gatilho de ativação:** Se o agente está em dúvida entre DISCOVER e BUILD, pergunte ao usuário:

> "Esta feature parece tocar o domínio central do sistema ou introduz um conceito novo. Recomendo usarmos o modo DISCOVER para entendermos o problema primeiro. São ~3-5 perguntas rápidas antes de começarmos. Ok?"

Se o usuário recusar, registre a recusa em STATE.md e use BUILD mode com atenção redobrada à spec.md.

## What You Produce

```
.specs/
├── project/
│   ├── VISION.md
│   ├── GLOSSARY.md
│   ├── BIG_PICTURE.md
│   ├── ARCHITECTURE.md (esqueleto)
│   ├── CONVENTIONS.md (esqueleto)
│   └── STATE.md
│
└── features/<feature-name>/
    ├── spec.md
    ├── design.md (se necessário)
    ├── behaviors.md (seção proativa)
    ├── tasks.md (se necessário)
    └── guide.md (se necessário)
```

## Workflow — Os 6 Checkpoints

### CP0: Problem Discovery

Explore o problema antes de qualquer menção a solução.

**Perguntas socráticas (uma por vez):**

1. "O que acontece hoje sem esse software? Como o problema é resolvido atualmente?"
2. "Quem sente essa dor? Quem mais é afetado?"
3. "O que te motivou a buscar essa solução agora? Mudou algo?"
4. "Se tudo der certo, como você vai medir o sucesso? O que muda no dia a dia?"

**Regras de conversação:**
- Uma pergunta por vez — nunca despeje todas de uma vez
- Prefira múltipla escolha quando possível
- Escute workarounds e ineficiências — eles revelam requisitos não declarados
- Desafie suposições: "Você disse X — o que acontece se X não for verdade?"
- **Tecnologia na descoberta:** se o usuário mencionar tecnologia durante CP0 (ex: "quero usar Redis"), não pergunte sobre ela agora. Anote mentalmente e redirecione: "Entendi. Vamos primeiro entender bem o problema. A tecnologia a gente decide na fase de design, ok?"

**Exemplo de diálogo:**
```
Agente: "O que acontece hoje sem esse software?"
Usuário: "Usamos planilhas, mas é um caos. Perdemos prazos."
Agente: "Entendi. Quem mais na equipe sente esse problema?"
Usuário: "O pessoal do comercial. Eles não sabem se o veículo foi publicado."
Agente: "Se tudo der certo, como vocês vão medir o sucesso?"
Usuário: "Quero que o comercial veja o status em tempo real."
```

**Gate:**
- Você consegue resumir o problema em 1 frase que o usuário concorda?
- Atores estão identificados?
- Critérios de sucesso são mensuráveis?

Se o gate não for satisfeito, continue perguntando. Não avance.

**Produz:** `VISION.md` (problema, atores, métricas, fora de escopo).

Ver [completeness-gates.md#gate-cp0--problem-discovery-visionmd] para o checklist completo.

---

### CP1: Domain Discovery

Mapeie as entidades do domínio e estabeleça a linguagem ubígua.

**Perguntas socráticas (uma por vez):**

1. "Quais são as 'coisas' principais que esse sistema precisa gerenciar?"
2. "Como [Entidade A] se relaciona com [Entidade B]? Uma [A] pode ter várias [B]?"
3. "No seu dia a dia, como você chama [conceito]? É diferente de [outro conceito]?"
4. "O que você precisa saber sobre [Entidade]? Quais informações são essenciais?"

**Exemplo de diálogo:**
```
Agente: "Quais são as 'coisas' principais que esse sistema precisa gerenciar?"
Usuário: "Veículos, portais de venda e as listagens."
Agente: "Como um veículo se relaciona com uma listagem?"
Usuário: "Um veículo pode estar em vários portais ao mesmo tempo."
Agente: "E 'listagem' é o mesmo que 'anúncio'? Ou são coisas diferentes?"
Usuário: "São a mesma coisa. Vamos usar 'listagem'."
```

**Regras:**
- Use os termos do usuário — não introduza jargão técnico
- Se o usuário usar sinônimos, peça para escolher um termo único
- Documente a linguagem ubígua no GLOSSARY.md

**Gate:**
- Todo termo do VISION.md está definido no GLOSSARY.md?
- Entidades core estão identificadas?
- Relacionamentos principais estão descritos?

**Produz:** `GLOSSARY.md` (termos, relacionamentos, linguagem ubígua), `BIG_PICTURE.md` (entidades + feature map inicial).

Ver [completeness-gates.md#gate-cp1--domain-discovery-glossarymd--big-picturemd] para o checklist completo.

---

### CP2: Requirements Elicitation

Derive os requisitos funcionais, não-funcionais e regras de negócio.

**Perguntas socráticas (uma por vez):**

1. "Quando [ator] faz [ação], o que o sistema deve fazer passo a passo?"
2. "Qual o tempo de resposta aceitável? O que é 'rápido' para vocês?"
3. "Se 50 pessoas usarem ao mesmo tempo, ainda precisa ser rápido?"
4. "Existem regras de negócio? Limites, aprovações, cálculos específicos?"
5. "E se algo der errado durante [operação]? O que deve acontecer?"

**Classificação dos requisitos:**

| Tipo | Código | Exemplo |
|------|--------|---------|
| Functional | RF-01 | "O sistema deve calcular frete com base no CEP" |
| Non-Functional | RNF-01 | "Resposta em <500ms para P95" |
| Business Rule | RN-01 | "Frete grátis para compras > R$ 200" |

**Regras:**
- Toda RF tem input + output + prioridade (Must/Should/Could)
- Toda RNF tem métrica mensurável. Se não tem métrica, documente por quê
- Toda RN está vinculada a pelo menos um RF
- Use BDD/Gherkin para acceptance criteria: Given/When/Then
- **Re-enquadre tecnologia:** se o usuário responder com tecnologia ("usar Redis para cache"), re-enquadre para o problema real: "Então você precisa de cache. Por quanto tempo os dados devem ficar armazenados?" A tecnologia vai para design.md.

**Gate:**
- Toda RF tem input, output e prioridade?
- Toda RNF tem métrica (ou justificativa)?
- Toda RN está vinculada a um RF?
- User stories cobrem happy path + pelo menos 1 erro?

**Produz:** `spec.md` (RFs, RNFs, RNs, user stories, critérios de sucesso, fora de escopo).

Ver [completeness-gates.md#gate-cp2--requirements-elicitation-specmd] para o checklist completo.
Ver [references/specify-feature.md] para o workflow detalhado de escrita da spec.

---

### CP3: Conceptual Design (Opcional)

Só execute se a feature tiver complexidade que justifique.

**Quando executar CP3:**
- Feature com entidades que têm ciclo de vida (rascunho → publicado → arquivado)
- Feature com fluxos complexos (múltiplos atores, decisões condicionais)
- Feature que requer decisões arquiteturais (banco, cache, fila, API externa)
- Feature que expõe API pública

**Perguntas socráticas (uma por vez):**

1. "[Entidade] passa por diferentes estados? Quais?"
2. "Quais são os passos do fluxo principal? (3-5 passos)"
3. "Que decisões arquiteturais precisamos tomar agora? Ou podemos adiar?"

**Gate:**
- Entidades com ciclo de vida têm estados mapeados?
- Decisões documentadas com alternativas consideradas?
- Contratos de API têm exemplos de request/response?

**Produz:** `design.md` (diagramas Mermaid, state machines, fluxos, ADRs, contratos de API).

Ver [references/design-feature.md] para o workflow detalhado.
Ver [completeness-gates.md#gate-cp3--conceptual-design-designmd--opcional] para o checklist.

---

### CP4: Proactive Behavioral Analysis

Antes de escrever código, antecipe comportamentos.

**Perguntas socráticas (uma por vez):**

1. "Qual o valor padrão se o usuário não especificar [campo]?"
2. "Quando [operação] é executada, que efeitos colaterais ela causa no sistema?"
3. "Se [API externa/banco/fila] estiver fora do ar, o que acontece?"
4. "E se o usuário enviar a mesma requisição duas vezes?"
5. "E se não houver dados? E se houver 10.000 registros?"
6. "Existe algum comportamento que parece errado mas é intencional?"

**Exemplo de diálogo:**
```
Agente: "Quando o veículo é publicado, que efeitos colaterais acontecem?"
Usuário: "Ele aparece nos portais. E o preço é calculado automaticamente."
Agente: "Se a API do portal estiver fora do ar, o que deve acontecer?"
Usuário: "A publicação falha para aquele portal, mas continua para os outros."
Agente: "E se o usuário clicar em 'publicar' duas vezes rápido?"
Usuário: "Boa pergunta... não tinha pensado nisso. Não pode criar duplicatas."
```

**Gate:**
- Defaults documentados para cada aspecto configurável?
- Side effects previstos para cada operação de escrita?
- Failure modes para cada dependência externa?
- Edge cases identificados (zero, um, muitos, duplicatas, concorrência, simultaneidade)?

**Produz:** `behaviors.md` (seção `## Proactive Analysis`).

Ver [completeness-gates.md#gate-cp4--proactive-behavioral-analysis-behaviorsmd---seção-proativa] para o checklist completo.

---

### ═══ LINHA DE CORTE ═══

A análise está completa. O agente pode implementar.

### CP5: Implementation & Reactive Behaviors

Implemente e capture o que descobrir durante o caminho.

**Workflow:**
1. Carregue spec.md + design.md (se existir) + behaviors.md (seção proativa)
2. Implemente seguindo as tasks ou diretamente
3. A cada tarefa concluída, verifique se descobriu comportamentos não previstos
4. Registre na seção `## Discovered During Implementation` do behaviors.md
5. Ao final, atualize STATE.md, CONVENTIONS.md, ARCHITECTURE.md, GLOSSARY.md, BIG_PICTURE.md

**Gate:**
- Código implementado conforme a spec?
- behaviors.md atualizado (proativo + reativo)?
- Documentos globais atualizados?

Ver [references/implement-track.md] para o workflow detalhado.
Ver [completeness-gates.md#gate-cp5--pós-implementação] para o checklist completo.

---

## DISCOVER para Features vs Projetos Completos

DISCOVER mode se adapta ao escopo:

| Escopo | CP0 | CP1 | CP2 | CP3 | CP4 | CP5 |
|--------|-----|-----|-----|-----|-----|-----|
| Projeto novo (via greenfield) | Visão completa do produto | Domínio completo do produto | Requisitos da 1ª feature | Design da 1ª feature | Comportamentos da 1ª feature | Implementação |
| Feature complexa em projeto existente | Contexto do problema da feature | Entidades novas da feature | Requisitos da feature | Design da feature | Comportamentos da feature | Implementação |

A diferença é o escopo das perguntas: um projeto novo merece "qual o propósito do sistema?", enquanto uma feature nova merece "qual o propósito desta feature no contexto do sistema existente?"

## MUST DO

- Perguntar uma coisa por vez — nunca despejar perguntas
- Oferecer múltipla escolha sempre que possível
- Só avançar quando o gate do checkpoint atual está satisfeito
- Se o usuário tentar pular para implementação, recuse educadamente
- Usar o vocabulário do usuário, não jargão técnico
- Documentar decisões com data e rationale

## MUST NOT DO

- Escrever código de implementação durante CP0-CP4
- Aceitar termos vagos ("fácil", "rápido", "intuitivo") sem métrica
- Pular gates
- Ignorar comportamentos proativos — "descobrimos durante a implementação" não é desculpa para não ter pensado antes
- Fazer múltiplas perguntas na mesma mensagem
- Usar jargão técnico quando o domínio tem um termo mais simples

## Good vs Bad Examples

**Bom DISCOVER:**

```
Usuário: "Quero um sistema para gerenciar veículos e publicar em portais."

Agente (CP0): "O que acontece hoje sem esse sistema?"
Usuário: "Usamos planilhas e publicamos manualmente em cada portal."
Agente: "Quem mais na equipe sente esse problema?"
...

(4 interações depois, gate satisfeito)

Agente (CP1): "Quais são as 'coisas' principais que o sistema gerencia?"
Usuário: "Veículos, portais, listagens."
Agente: "Um veículo pode estar em vários portais?"
...

(3 interações depois, gate satisfeito)

Agente: "Pronto. Temos o domínio mapeado. Vamos para os requisitos?"
```

**Mau DISCOVER:**

```
Usuário: "Quero um sistema para gerenciar veículos."

Agente: "Vamos criar a estrutura. Qual o nome do projeto?"
  (Pulou CP0 e CP1 — não entendeu o problema nem o domínio)
```

**Mau DISCOVER (perguntas em lote):**

```
Agente: "Vou fazer algumas perguntas: qual o problema? quem usa? qual a métrica?
  quais as entidades? como elas se relacionam? qual o fluxo principal?"
  (Nunca despeje perguntas — uma por vez)
```
