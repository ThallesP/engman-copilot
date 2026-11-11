# Inventário da base de conhecimento

Lista dos documentos que o assistente vai consultar por RAG. Documentos **reais** vêm do briefing e de fontes públicas. Documentos **simulados** são criados pelo grupo com dados fictícios e plausíveis, e são sempre identificados como fictícios no próprio arquivo.

## Documentos

| ID | Documento | Conteúdo e campos | Origem | Volume | Status na Sprint 01 |
|---|---|---|---|---|---|
| CAT-01 | Catálogo de ativos | TAG, tipo, fabricante, modelo, km, praça ou pórtico, instalação, vida útil, criticidade | simulado | 30+ linhas | 7 linhas prontas em [`ativos_sinteticos.md`](ativos_sinteticos.md) |
| PMP-01 | Plano de manutenção preventiva | Ativo, tarefa, periodicidade, responsável, tempo estimado, recursos, condição de execução, POP | simulado | 1 documento, 8 tarefas | pronto em [`plano_manutencao_preventiva.md`](plano_manutencao_preventiva.md) |
| FSM | Histórico de falhas do FSM | TAG, data, sintoma, causa identificada, tempo de reparo, peça trocada, reincidência | simulado | 50+ registros | 23 registros prontos em [`ativos_sinteticos.md`](ativos_sinteticos.md) |
| POP | Procedimentos operacionais | POP-CFTV-01 (limpeza de lente), POP-CFTV-02 (fixação e foco), POP-CLS-01 (limpeza e aferição da câmera de classificação), POP-LACO-01 (teste e inspeção de laço), POP-PFF-01 (intervenção em pórtico). Cada um com pré-requisitos de segurança, passos e profissional habilitado | simulado | 5 procedimentos | especificados (títulos e campos); texto na Sprint 03 |
| FT | Fichas técnicas | Especificações da câmera PTZ, da câmera de classificação, do laço indutivo e do pórtico Free Flow: alimentação, comunicação, faixa de operação, grau de proteção | real (características gerais de catálogos públicos) + simulado (modelos fictícios) | 4 fichas | a produzir |
| MC-01 | Matriz de criticidade e prioridade | Criticidade A/B/C por ativo e regra de prioridade P1 a P4 | simulado | 1 documento | pronto em [`matriz_criticidade.md`](matriz_criticidade.md) |
| GLO-01 | Glossário de siglas | CFTV, PMV, SAT, SCA, CCO, CBM, RCM, WCM, FSM, RGA, LDA, λ, MTBF, MTTR, WIM, OCR, POP, OS, NR | real | 1 documento | a produzir |
| NR-01 | Normas e requisitos de segurança | Resumo dos trechos aplicáveis da NR-10 (eletricidade) e da NR-35 (trabalho em altura) e regras de trabalho em via | real (resumo) | 1 documento | a produzir |
| BRF-01 | Briefing Motiva | Contexto, ondas de inovação, temas 1, 2 e 2.1 (kick-off de 16/09/2026) | real | 1 documento | disponível ao grupo; **não versionado** no repositório por ser material da empresa |
| BOL-01 | Boletins de ocorrência de campo | Relato textual do técnico: TAG, data, condição ambiental, o que foi encontrado, o que foi feito | simulado | 10+ boletins | a produzir, com nomes e placas já removidos |

### Uma fonte para cada dado

| Dado | Única fonte | Indexado no RAG? |
|---|---|---|
| Ficha e criticidade do ativo (CAT-01) | tool `consultar_ativo` | não |
| Histórico de falhas (FSM) | tool `consultar_historico_falhas` | não |
| Leituras das estações meteorológicas (SCA) | tool `consultar_condicao_ambiental` | não |
| Alarmes abertos | tool `listar_alarmes_abertos` | não |
| Procedimentos (POP) | tool `consultar_procedimento` | sim, para perguntas conceituais ("como é feita a limpeza da lente?") |
| Plano preventivo, matriz MC-01, fichas técnicas, glossário, normas, briefing, boletins | base de conhecimento | sim |

Dados estruturados e que mudam (cadastro, histórico, leituras, alarmes) chegam só pelas tools, que leem a fonte no momento da pergunta; o RAG indexa só documentos de texto. Assim nenhum dado aparece duas vezes com valores diferentes. Os POPs são o único documento com dois caminhos, e o conteúdo é o mesmo arquivo nos dois.

## Como a base é usada no RAG

1. Cada documento indexado (ver tabela acima) é dividido em trechos (chunks), transformado em vetores pelo modelo de embeddings `multilingual-e5-base` e guardado no ChromaDB.
2. A cada pergunta, o sistema busca os **4 trechos mais parecidos**, filtrando pelos metadados quando o TAG ou o tipo de ativo já são conhecidos.
3. Os trechos entram no contexto do modelo com o ID do documento, para que o assistente cite a fonte (regra 2 do [system prompt](../../prompts/system_prompt.md)).

### Tamanho do chunk

| Tipo de documento | Divisão | Tamanho |
|---|---|---|
| POP | 1 etapa do procedimento por chunk, sempre com os pré-requisitos de segurança repetidos no início | 300 a 450 tokens |
| Plano, matriz, fichas, normas, glossário, boletins | Por seção, com sobreposição de 50 tokens | 300 a 450 tokens |

**Por quê.** O modelo roda com janela de 16.384 tokens (Aula 02), dividida conforme a política de contexto do README. Reservamos no máximo 1.800 tokens para o RAG, o que dá 4 trechos de até 450 tokens. Trechos maiores reduziriam o número de fontes por pergunta; menores quebrariam um procedimento no meio de um passo. O limite de 450 tokens também respeita o máximo de 512 tokens de entrada do modelo de embeddings.

### Metadados guardados em cada trecho

`doc_id`, `tipo_documento`, `tag`, `tipo_ativo`, `km`, `praca`, `data`, `origem` (real ou simulado).

Com eles, a busca pode ser filtrada, por exemplo "só histórico do FSM do TAG LACO-RV01-P03-F2", em vez de procurar na base inteira.

## O que nunca entra na base

- Placas de veículos e imagens de ocupantes capturadas por OCR ou CFTV.
- Nomes, matrículas ou qualquer dado pessoal de empregados e de motoristas. Nos boletins, o técnico é identificado só pela função.
- Valores de arrecadação, contratos e dados financeiros da concessionária.
- Credenciais, endereços IP e senhas de equipamentos.
- Qualquer dado real da Motiva. Tudo o que é operacional é simulado e marcado como tal.

Essas exclusões são a mitigação dos riscos R-07 e R-08 da [matriz de riscos](../../docs/riscos_lgpd.md).
