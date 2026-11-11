# Fixtures do eval set — retornos simulados das tools

> **Dados simulados**, criados pelo grupo. Este arquivo **não** faz parte da base de conhecimento e **não** é indexado no RAG: ele define o que cada tool de tempo real devolve quando um caso do [eval set](sprint1_eval_set.json) é executado. O catálogo e o histórico de falhas vêm de [`ativos_sinteticos.md`](../data/knowledge_base/ativos_sinteticos.md).

Data de referência de todos os casos: **2026-11-18**.

## `consultar_condicao_ambiental`

| Estação | Faixa de km coberta | Horário | Chuva | Visibilidade | Neblina | Risco de reflexo solar |
|---|---|---|---|---|---|---|
| SCA-RV01-KM410 | 400 a 425 | 05:10 a 06:30 | 0 mm/h | 60 m a 120 m | sim | não |
| SCA-RV01-KM410 | 400 a 425 | 06:30 a 14:00 | 0 mm/h | acima de 10 km | não | não |
| SCA-RV01-KM410 | 400 a 425 | 14:00 a 16:30 | 0 mm/h | acima de 10 km | não | não (céu encoberto) |
| SCA-RV01-KM450 | 440 a 470 | o dia todo | erro `SEM_DADOS` (estação em manutenção) | | | |

Valores de exemplo no formato do `x-retorno`:

- km 412, 05:40 → `{"estacao": "SCA-RV01-KM410", "chuva_mm_h": 0, "visibilidade_m": 80, "neblina": true, "risco_reflexo_solar": false}`
- km 418, 05:42 → `{"estacao": "SCA-RV01-KM410", "chuva_mm_h": 0, "visibilidade_m": 110, "neblina": true, "risco_reflexo_solar": false}`
- km 418, 15:20 → `{"estacao": "SCA-RV01-KM410", "chuva_mm_h": 0, "visibilidade_m": 10000, "neblina": false, "risco_reflexo_solar": false}`
- km 455, qualquer horário → `{"erro": {"codigo": "SEM_DADOS", "mensagem": "Estação SCA-RV01-KM450 em manutenção"}}`

## `listar_alarmes_abertos`

Alarmes abertos no CCO em 2026-11-18 até as 17:00:

| id_alarme | TAG | Descrição | Horário | km |
|---|---|---|---|---|
| AL-1101 | CFTV-RV01-KM412-N | imagem degradada | 05:40 | 412 |
| AL-1102 | CFTV-RV01-KM418-S | imagem degradada | 05:42 | 418 |
| AL-1103 | LACO-RV01-P03-F2 | divergência de classificação | 05:45 | 415 |
| AL-1107 | CFTV-RV01-KM412-N | imagem degradada | 11:05 | 412 |
| AL-1110 | CFTV-RV01-KM418-S | imagem degradada | 15:20 | 418 |
| AL-1112 | PFF-RV01-KM455 | leitura OCR inconsistente | 16:50 | 455 |

## `abrir_ordem_servico`

- Primeira chamada confirmada para um TAG e sintoma → `{"numero_os": "OS-SIM-000123", "status": "aberta"}`.
- Segunda chamada com o mesmo `id_solicitacao`, ou para o mesmo TAG e sintoma → `{"erro": {"codigo": "OS_DUPLICADA", "mensagem": "OS-SIM-000123 já aberta"}}`.

## `consultar_procedimento`

| tipo_ativo | tipo_intervencao | Retorno |
|---|---|---|
| portico_free_flow | corretiva | POP-PFF-01: desenergizar e bloquear o painel, interditar a faixa, trabalho em altura com NR-35 e eletricidade com NR-10, executado por técnico de sistemas habilitado |
| laco_indutivo | corretiva | POP-LACO-01: fechar a faixa com cones, medir sensibilidade com o analisador, inspecionar selagem e pavimento, executado por técnico de sistemas |
