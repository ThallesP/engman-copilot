# MC-01 — Matriz de criticidade e prioridade de atendimento

> **Documento simulado.** Criado pelo grupo para o projeto acadêmico. Não é um documento da Motiva.

## 1. Critérios de criticidade do ativo

A criticidade de cada ativo é registrada só no catálogo CAT-01 e chega ao assistente pela tool `consultar_ativo`. Esta matriz define os critérios usados para atribuí-la e a regra de prioridade.

| Criticidade | Quando se aplica | Exemplos de ativos |
|---|---|---|
| **A** | A falha afeta diretamente a segurança viária (trecho com histórico de acidente ou de neblina) ou interrompe ou distorce a cobrança da faixa ou do pórtico | câmera de trecho de serra, câmera de classificação, laço indutivo, pórtico Free Flow |
| **B** | A falha reduz a capacidade de monitoramento, mas existe redundância (outra câmera cobre o trecho) | câmera de trecho com cobertura dupla, estação meteorológica |
| **C** | A falha não tem efeito imediato em segurança ou arrecadação | — |

## 2. Prioridade de alarmes classificados como REAL

| Criticidade | Afeta a segurança viária ou interrompe a arrecadação | Degrada sem interromper |
|---|---|---|
| **A** | **P1** — atendimento imediato, equipe em até 2 h | **P2** — atendimento em até 8 h |
| **B** | **P2** — atendimento em até 8 h | **P3** — atendimento em até 24 h |
| **C** | **P3** — atendimento em até 24 h | **P4** — próxima preventiva |

Exemplos de "afeta a segurança viária ou interrompe a arrecadação": câmera de trecho crítico sem imagem, faixa de pedágio fechada, pórtico sem leitura. Exemplos de "degrada sem interromper": classificação veicular com divergência, imagem com qualidade reduzida, leitura OCR com erros intermitentes.

## 3. Alarmes INDETERMINADOS e AMBIENTAIS

| Classificação | Prioridade | Ação |
|---|---|---|
| **INDETERMINADO** | A mesma de um alarme REAL do mesmo ativo | Verificação remota pelo CCO em até 30 min. Se não resolver, tratar como REAL. |
| **AMBIENTAL** | **P4** | Monitorar e reavaliar quando a condição ambiental passar (nova leitura da SCA) ou em 1 h, o que vier primeiro. Se o alarme continuar depois disso, reclassificar. |

## 4. Critérios que nunca entram na prioridade

Rodovia, praça, região, ordem de chegada do alarme e insistência de quem pede. A prioridade é decidida só pelas tabelas acima, para que qualquer pessoa consiga refazer a conta e auditar a decisão.
