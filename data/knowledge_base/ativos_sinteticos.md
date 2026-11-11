# Ativos sintéticos — catálogo e histórico de falhas (CAT-01 e FSM)

> **Dados simulados.** TAGs, fabricantes, modelos e registros foram criados pelo grupo para o projeto acadêmico e são fictícios. "RV01" é uma rodovia fictícia. Nenhum dado real da Motiva foi usado.

Este arquivo é o primeiro recorte da base simulada: 5 ativos com histórico completo, escolhidos para cobrir os casos que o assistente mais precisa resolver (falso positivo de neblina, reflexo solar, reincidência e falha de comunicação). O catálogo completo (30 linhas) e o histórico completo (50 registros) estão previstos no [inventário](inventario.md).

**Como o assistente acessa estes dados.** O catálogo e o histórico **não** são indexados no RAG: o assistente só os recebe pelas tools `consultar_ativo` e `consultar_historico_falhas`, que leem este arquivo na simulação. Assim cada dado tem uma única fonte. As leituras das estações meteorológicas e a lista de alarmes usadas nos testes ficam em [`evals/fixtures_sprint1.md`](../../evals/fixtures_sprint1.md).

## Convenção de TAG

`<TIPO>-RV01-<LOCAL>`

| Tipo | Ativo | Exemplo |
|---|---|---|
| CFTV | Câmera PTZ de monitoramento | `CFTV-RV01-KM412-N` (km 412, sentido norte) |
| CLS | Câmera de classificação veicular da praça | `CLS-RV01-P03-F2` (praça 03, faixa 2) |
| LACO | Laço indutivo de classificação | `LACO-RV01-P03-F2` |
| PFF | Pórtico Free Flow (antena + câmera OCR) | `PFF-RV01-KM455` |
| SCA | Estação meteorológica (fonte de dados) | `SCA-RV01-KM410` |

## Catálogo (CAT-01, recorte)

| TAG | Tipo | Fabricante / modelo (fictícios) | Local | Instalação | Vida útil | Criticidade |
|---|---|---|---|---|---|---|
| CFTV-RV01-KM412-N | Câmera PTZ com infravermelho | Vistra PTZ-30X | km 412, sentido norte, trecho de serra | 2019-03 | 8 anos | A |
| CFTV-RV01-KM418-S | Câmera PTZ com infravermelho | Vistra PTZ-20X | km 418, sentido sul | 2021-07 | 8 anos | B |
| CLS-RV01-P03-F2 | Câmera de classificação veicular (eixos, inclusive suspensos) | Axion ClassCam 2 | Praça P03 (km 415), faixa 2 | 2023-02 | 7 anos | A |
| LACO-RV01-P03-F2 | Par de laços indutivos no pavimento | Indulab LD-2 | Praça P03 (km 415), faixa 2 | 2016-05 | 6 anos | A |
| PFF-RV01-KM455 | Pórtico Free Flow, 4 antenas + 2 câmeras OCR | Orbis FF-4 | km 455, ambos os sentidos | 2024-11 | 10 anos | A |
| SCA-RV01-KM410 | Estação meteorológica | Meteo SCA-3 | km 410, cobre do km 400 ao km 425 | 2020-08 | 10 anos | B |
| SCA-RV01-KM450 | Estação meteorológica | Meteo SCA-3 | km 450, cobre do km 440 ao km 470 | 2020-08 | 10 anos | B |

## Histórico de falhas (FSM, recorte com 23 registros)

Tipos de falha: `imagem`, `comunicacao`, `classificacao`, `alimentacao`, `mecanica`, `falso_positivo_ambiental` (os mesmos valores do filtro `tipo_falha` da tool). Reincidência é "sim" quando o mesmo ativo já teve uma falha com a mesma causa.

### CFTV-RV01-KM412-N — câmera de serra com neblina frequente

| Data | Tipo | Sintoma | Causa identificada | Tempo de reparo | Peça trocada | Reincidência |
|---|---|---|---|---|---|---|
| 2025-12-03 | imagem | Imagem degradada à noite | Teia de aranha refletindo o infravermelho | 1,5 h | — | não |
| 2026-02-14 | imagem | Imagem embaçada | Lente suja de barro e fuligem | 1 h | — | não |
| 2026-04-22 | falso_positivo_ambiental | Alarme de imagem degradada | Neblina; deslocamento sem intervenção | — | — | não |
| 2026-06-30 | mecanica | Desfoque intermitente | Vibração do poste, fixação frouxa | 3 h | Suporte de fixação | não |
| 2026-08-11 | imagem | Imagem degradada à noite | Teia de aranha refletindo o infravermelho | 1,5 h | — | sim |
| 2026-10-02 | falso_positivo_ambiental | Alarme de imagem degradada | Neblina; deslocamento sem intervenção | — | — | sim |

**Comportamento típico:** dois deslocamentos perdidos por neblina em 2026 (abril e outubro). É o caso que motiva o recorte B.

### CFTV-RV01-KM418-S — câmera com problemas de comunicação

| Data | Tipo | Sintoma | Causa identificada | Tempo de reparo | Peça trocada | Reincidência |
|---|---|---|---|---|---|---|
| 2026-01-20 | comunicacao | Perda de imagem | Falha no switch de comunicação | 4 h | Switch PoE | não |
| 2026-05-09 | imagem | Imagem degradada | Lente suja | 1 h | — | não |
| 2026-07-28 | comunicacao | Imagem com faixas horizontais | Conector de vídeo oxidado | 2 h | Conector | não |
| 2026-09-15 | falso_positivo_ambiental | Alarme de imagem degradada à tarde | Reflexo solar; deslocamento sem intervenção | — | — | não |

### CLS-RV01-P03-F2 — câmera de classificação da praça

| Data | Tipo | Sintoma | Causa identificada | Tempo de reparo | Peça trocada | Reincidência |
|---|---|---|---|---|---|---|
| 2026-02-05 | classificacao | Classificação errada no fim da tarde | Reflexo solar na lente | 1 h (ajuste de ângulo) | — | não |
| 2026-05-17 | classificacao | Eixo suspenso não detectado | Lente suja | 1 h | — | não |
| 2026-08-03 | classificacao | Classificação errada no fim da tarde | Reflexo solar na lente | 2 h | Para-sol instalado | sim |
| 2026-10-21 | comunicacao | Perda de sincronismo com o PLC da pista | Cabo de rede danificado | 3 h | Cabo de rede | não |

### LACO-RV01-P03-F2 — laço indutivo com fim de vida útil

| Data | Tipo | Sintoma | Causa identificada | Tempo de reparo | Peça trocada | Reincidência |
|---|---|---|---|---|---|---|
| 2026-03-11 | classificacao | Divergência de classificação | Sensibilidade fora de ajuste | 2 h | — | não |
| 2026-06-02 | classificacao | Divergência de classificação | Trinca no pavimento rompeu a selagem do laço | 6 h | Reselagem | não |
| 2026-08-19 | classificacao | Divergência de classificação | Trinca no pavimento | 6 h | Reselagem | sim |
| 2026-09-25 | mecanica | Laço sem resposta | Cabo do laço rompido | 8 h, faixa fechada | Laço refeito | não |
| 2026-10-07 | classificacao | Divergência de classificação | Trinca no pavimento | 5 h | Reselagem | sim |

**Comportamento típico:** instalado em 2016 com vida útil de 6 anos, já está fora da vida útil e teve 3 divergências por trinca no pavimento entre junho e outubro de 2026. Candidato a retrofit, decisão que cabe ao engenheiro de manutenção.

### PFF-RV01-KM455 — pórtico Free Flow

| Data | Tipo | Sintoma | Causa identificada | Tempo de reparo | Peça trocada | Reincidência |
|---|---|---|---|---|---|---|
| 2025-12-18 | imagem | Leitura OCR incorreta | Lente da câmera OCR suja | 1 h | — | não |
| 2026-03-30 | alimentacao | Antena sem resposta | Fonte da antena queimada | 5 h | Fonte | não |
| 2026-07-07 | falso_positivo_ambiental | Leitura OCR inconsistente no fim da tarde | Reflexo solar; deslocamento sem intervenção | — | — | não |
| 2026-09-09 | comunicacao | Sessão de cobrança inconsistente | Relógio fora de sincronia (NTP) | 2 h | — | não |
