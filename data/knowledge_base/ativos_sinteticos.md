# Ativos sintéticos — catálogo e histórico de falhas (CAT-01 e FSM)

> **Dados simulados.** TAGs, fabricantes, modelos e registros foram criados pelo grupo para o projeto acadêmico e são fictícios. "RV01" é uma rodovia fictícia. Nenhum dado real da Motiva foi usado.

Este arquivo é o primeiro recorte da base simulada: 5 ativos com histórico completo, escolhidos para cobrir os casos que o assistente mais precisa resolver (falso positivo de neblina, reflexo solar, reincidência e falha de comunicação). Ele já serve de dataset para os casos do [eval set](../../evals/sprint1_eval_set.json). O catálogo completo (30 linhas) e o histórico completo (50 registros) estão previstos no [inventário](inventario.md).

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
| CFTV-RV01-KM412-N | Câmera PTZ com infravermelho | Vistra PTZ-30X | km 412, sentido norte, trecho de serra | 03/2019 | 8 anos | A |
| CFTV-RV01-KM418-S | Câmera PTZ com infravermelho | Vistra PTZ-20X | km 418, sentido sul | 07/2021 | 8 anos | B |
| CLS-RV01-P03-F2 | Câmera de classificação veicular (eixos, inclusive suspensos) | Axion ClassCam 2 | Praça P03 (km 415), faixa 2 | 02/2023 | 7 anos | A |
| LACO-RV01-P03-F2 | Par de laços indutivos no pavimento | Indulab LD-2 | Praça P03 (km 415), faixa 2 | 05/2016 | 6 anos | A |
| PFF-RV01-KM455 | Pórtico Free Flow, 4 antenas + 2 câmeras OCR | Orbis FF-4 | km 455, ambos os sentidos | 11/2024 | 10 anos | A |
| SCA-RV01-KM410 | Estação meteorológica | Meteo SCA-3 | km 410, cobre do km 400 ao km 425 | 08/2020 | 10 anos | B |
| SCA-RV01-KM450 | Estação meteorológica | Meteo SCA-3 | km 450, cobre do km 440 ao km 470 | 08/2020 | 10 anos | B |

## Histórico de falhas (FSM, recorte com 23 registros)

### CFTV-RV01-KM412-N — câmera de serra com neblina frequente

| Data | Sintoma | Causa identificada | Tempo de reparo | Peça trocada | Reincidência |
|---|---|---|---|---|---|
| 2025-12-03 | Imagem degradada à noite | Teia de aranha refletindo o infravermelho | 1,5 h | — | não |
| 2026-02-14 | Imagem embaçada | Lente suja de barro e fuligem | 1 h | — | não |
| 2026-04-22 | Alarme de imagem degradada | Neblina. Falso positivo, sem intervenção | deslocamento perdido | — | — |
| 2026-06-30 | Desfoque intermitente | Vibração do poste, fixação frouxa | 3 h | Suporte de fixação | não |
| 2026-08-11 | Imagem degradada à noite | Teia de aranha refletindo o infravermelho | 1,5 h | — | sim (2ª em 9 meses) |
| 2026-10-02 | Alarme de imagem degradada | Neblina. Falso positivo, sem intervenção | deslocamento perdido | — | sim |

**Comportamento típico:** dois deslocamentos perdidos por neblina em 6 meses. É o caso que motiva o recorte B.

### CFTV-RV01-KM418-S — câmera com problemas de comunicação

| Data | Sintoma | Causa identificada | Tempo de reparo | Peça trocada | Reincidência |
|---|---|---|---|---|---|
| 2026-01-20 | Perda de imagem | Falha no switch de comunicação | 4 h | Switch PoE | não |
| 2026-05-09 | Imagem degradada | Lente suja | 1 h | — | não |
| 2026-07-28 | Imagem com faixas horizontais | Conector de vídeo oxidado | 2 h | Conector | não |
| 2026-09-15 | Alarme de imagem degradada à tarde | Reflexo solar. Falso positivo, sem intervenção | deslocamento perdido | — | não |

### CLS-RV01-P03-F2 — câmera de classificação da praça

| Data | Sintoma | Causa identificada | Tempo de reparo | Peça trocada | Reincidência |
|---|---|---|---|---|---|
| 2026-02-05 | Classificação errada no fim da tarde | Reflexo solar na lente | 1 h (ajuste de ângulo) | — | não |
| 2026-05-17 | Eixo suspenso não detectado | Lente suja | 1 h | — | não |
| 2026-08-03 | Classificação errada no fim da tarde | Reflexo solar na lente | 2 h | Para-sol instalado | sim |
| 2026-10-21 | Perda de sincronismo com o PLC da pista | Cabo de rede danificado | 3 h | Cabo de rede | não |

### LACO-RV01-P03-F2 — laço indutivo com fim de vida útil

| Data | Sintoma | Causa identificada | Tempo de reparo | Peça trocada | Reincidência |
|---|---|---|---|---|---|
| 2026-03-11 | Divergência de classificação | Sensibilidade fora de ajuste | 2 h | — | não |
| 2026-06-02 | Divergência de classificação | Trinca no pavimento rompeu a selagem do laço | 6 h | Reselagem | não |
| 2026-08-19 | Divergência de classificação | Trinca no pavimento | 6 h | Reselagem | sim |
| 2026-09-25 | Laço sem resposta | Cabo do laço rompido | 8 h, faixa fechada | Laço refeito | sim |
| 2026-10-07 | Divergência de classificação | Trinca no pavimento | 5 h | Reselagem | sim |

**Comportamento típico:** instalado em 2016 com vida útil de 6 anos, já está fora da vida útil e tem reincidência de trinca. Candidato a retrofit, decisão que cabe ao engenheiro de manutenção.

### PFF-RV01-KM455 — pórtico Free Flow

| Data | Sintoma | Causa identificada | Tempo de reparo | Peça trocada | Reincidência |
|---|---|---|---|---|---|
| 2025-12-18 | Leitura OCR incorreta | Lente da câmera OCR suja | 1 h | — | não |
| 2026-03-30 | Antena sem resposta | Fonte da antena queimada | 5 h | Fonte | não |
| 2026-07-07 | Leitura OCR inconsistente no fim da tarde | Reflexo solar. Falso positivo, sem intervenção | deslocamento perdido | — | não |
| 2026-09-09 | Sessão de cobrança inconsistente | Relógio fora de sincronia (NTP) | 2 h | — | não |

## Leituras ambientais usadas no eval set

Retornos simulados da tool `consultar_condicao_ambiental` para o dia 2026-11-18:

| Estação | Horário | Condição |
|---|---|---|
| SCA-RV01-KM410 | 05:10 a 06:30 | Neblina, visibilidade entre 60 m e 120 m, sem chuva |
| SCA-RV01-KM410 | 14:00 a 16:00 | Tempo seco, visibilidade acima de 10 km, sol forte |
| SCA-RV01-KM450 | o dia todo | Sem dados: estação em manutenção |
