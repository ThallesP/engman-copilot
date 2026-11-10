# MC-01 — Matriz de criticidade e prioridade de atendimento

> **Documento simulado.** Criado pelo grupo para o projeto acadêmico. Não é um documento da Motiva.

## 1. Criticidade do ativo

| Criticidade | Quando se aplica | Ativos do recorte |
|---|---|---|
| **A** | Falha afeta diretamente a segurança viária (trecho com histórico de acidente ou neblina) ou a cobrança da faixa ou do pórtico | CFTV-RV01-KM412-N, CLS-RV01-P03-F2, LACO-RV01-P03-F2, PFF-RV01-KM455 |
| **B** | Falha reduz a capacidade de monitoramento, mas existe redundância (outra câmera cobre o trecho) | CFTV-RV01-KM418-S, SCA-RV01-KM410, SCA-RV01-KM450 |
| **C** | Falha sem efeito imediato em segurança ou arrecadação | nenhum ativo do recorte atual |

## 2. Prioridade de alarmes classificados como REAL

| Criticidade | Afeta a segurança viária ou para a arrecadação | Degrada sem parar |
|---|---|---|
| **A** | **P1** — atendimento imediato, equipe em até 2 h | **P2** — atendimento em até 8 h |
| **B** | **P2** — atendimento em até 8 h | **P3** — atendimento em até 24 h |
| **C** | **P3** — atendimento em até 24 h | **P4** — próxima preventiva |

Exemplos de "afeta a segurança viária ou para a arrecadação": câmera de trecho crítico sem imagem, faixa de pedágio fechada, pórtico sem leitura. Exemplos de "degrada sem parar": classificação veicular com divergência, imagem com qualidade reduzida, leitura OCR com erros intermitentes.

## 3. Alarmes INDETERMINADOS e AMBIENTAIS

| Classificação | Prioridade | Ação |
|---|---|---|
| **INDETERMINADO** | A mesma de um alarme REAL do mesmo ativo | Verificação remota pelo CCO em até 30 min. Se não resolver, tratar como REAL. |
| **AMBIENTAL** | **P4** | Monitorar e reavaliar quando a condição ambiental passar, ou em 1 h. Se o alarme continuar depois disso, reclassificar. |

## 4. Critérios que nunca entram na prioridade

Rodovia, praça, região, ordem de chegada do alarme e insistência de quem pede. A prioridade é decidida só pelas tabelas acima, para que qualquer pessoa consiga refazer a conta e auditar a decisão.
