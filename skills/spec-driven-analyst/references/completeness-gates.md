# Completeness Gates

## Purpose

Checklists de completude para cada checkpoint. O agente verifica o gate ANTES de avançar ao próximo checkpoint.

Gates verificam **conteúdo**, não tamanho. Um VISION.md de 200 linhas para um ERP complexo é válido se cobre todos os pontos do gate. Um VISION.md de 5 linhas para um projeto simples também é válido — desde que cubra problema, atores e métricas.

## Gate CP0 — Problem Discovery (VISION.md)

- [ ] Declaração do problema: "Estamos resolvendo [problema] para [atores]"
- [ ] Atores/stakeholders primários identificados
- [ ] Critérios de sucesso mensuráveis (tempo, dinheiro, satisfação, adoção)
- [ ] Fora de escopo definido (o que NÃO estamos fazendo agora)
- [ ] O agente consegue resumir o projeto em 1 frase que o usuário concorda

## Gate CP1 — Domain Discovery (GLOSSARY.md + BIG_PICTURE.md)

- [ ] Todo termo usado no VISION.md está definido no GLOSSARY.md
- [ ] Entidades core identificadas com atributos principais
- [ ] Relacionamentos entre entidades descritos (cardinalidade quando relevante)
- [ ] Linguagem ubígua acordada — termos do domínio, não termos técnicos
- [ ] BIG_PICTURE.md criado com entidades e feature map inicial
- [ ] Nenhum termo do glossário está órfão (todo termo é usado em pelo menos um documento)

## Gate CP2 — Requirements Elicitation (spec.md)

- [ ] User stories cobrem os atores principais
- [ ] Toda user story tem pelo menos 2 acceptance criteria (1 happy path + 1 erro/alternativo)
- [ ] RFs numerados (RF-01, RF-02...) com input, output e prioridade (Must/Should/Could)
- [ ] RNFs numerados (RNF-01, RNF-02...) com métrica mensurável
- [ ] RNF sem métrica tem justificativa documentada (ex: "não aplicável — sistema single-user")
- [ ] RNs numerados (RN-01, RN-02...) vinculados a pelo menos um RF
- [ ] Critérios de sucesso da feature são mensuráveis e verificáveis
- [ ] Fora de escopo da feature definido
- [ ] Cross-references para GLOSSARY.md quando usa termos do domínio
- [ ] Frontmatter preenchido com concerns da feature

## Gate CP3 — Conceptual Design (design.md) — Opcional

Só aplicável se design.md foi criado:

- [ ] Decisões documentadas com alternativas consideradas (mínimo 2 opções por decisão)
- [ ] Alternativas descartadas têm justificativa
- [ ] Diagramas legíveis e relevantes (só existem se adicionam clareza além do texto)
- [ ] Contratos de API com exemplos de request/response (se aplicável)
- [ ] State machines para entidades com ciclo de vida (se aplicável)
- [ ] Cross-references para CONVENTIONS.md e ARCHITECTURE.md

## Gate CP4 — Proactive Behavioral Analysis (behaviors.md - seção proativa)

- [ ] Defaults documentados para cada aspecto configurável da feature
- [ ] Side effects previstos para cada operação de escrita
- [ ] Failure modes mapeados para cada dependência externa
- [ ] Edge cases identificados (zero, um, muitos, duplicatas, concorrência)
- [ ] Quirks documentados com rationale (se houver)
- [ ] Infraestrutura: cada componente mapeado como Required/Degraded/Optional
- [ ] Comportamentos cross-cutting identificados? Se sim, registrados em GLOBAL_BEHAVIORS.md (não duplicados em behaviors.md da feature)

## Gate CP5 — Pós-Implementação (comportamentos reativos + atualizações globais)

- [ ] Código implementado conforme spec.md
- [ ] behaviors.md atualizado com seção `## Discovered During Implementation`
- [ ] Toda decisão de implementação que afeta comportamento externo está registrada
- [ ] STATE.md atualizado (blockers resolvidos, lições aprendidas, decisões pendentes)
- [ ] BIG_PICTURE.md atualizado (feature status, entidades, ADRs, open questions)
- [ ] CONVENTIONS.md atualizado se padrão novo foi descoberto
- [ ] ARCHITECTURE.md atualizado se decisão arquitetural foi tomada
- [ ] GLOSSARY.md atualizado se termo novo foi introduzido
- [ ] GLOBAL_BEHAVIORS.md atualizado se comportamento cross-cutting foi descoberto
- [ ] Frontmatter do behaviors.md atualizado com concerns descobertos durante implementação
- [ ] Extração necessária? Alguma seção é referenciada por 3+ arquivos?
