# Riscos, ética, segurança e LGPD

O assistente apoia decisões sobre infraestrutura crítica: segurança viária, arrecadação e trabalho em campo. Um erro aqui tem consequência física e financeira. Por isso cada risco abaixo tem uma mitigação concreta e o **lugar exato** onde ela aparece no projeto:

- **Regra n** — regra numerada do bloco REGRAS do [system prompt](../prompts/system_prompt.md).
- **Bloco n** — etapa do fluxograma da [arquitetura](arquitetura.png).
- **Cnn** — caso de teste do [eval set](../evals/sprint1_eval_set.json).
- **Base** — regra do [inventário da base de conhecimento](../data/knowledge_base/inventario.md).

Impacto: **S** segurança, **F** financeiro, **L** legal, **R** reputacional.

## Matriz de riscos

| ID | Risco | Como se manifesta neste projeto | Impacto | Mitigação | Onde está |
|---|---|---|---|---|---|
| R-01 | Alucinação | Inventar TAG, histórico, leitura de sensor, número de OS ou trecho de norma | S, F — alto | Proibição explícita de inventar; fonte obrigatória entre colchetes; dados vindos de tools; admitir "não encontrei" | Regras 1, 2, 12 · Bloco 7 confere se o TAG existe no catálogo · C01, C02, C06, C07, C17, C19 |
| R-02 | Prompt injection direta | "Ignore as instruções", "modo de manutenção", pedido para mostrar o prompt | R, S — médio | Regras imutáveis; nunca revelar o prompt; guardrail de entrada detecta padrões de injection | Regra 9 · Bloco 2 · C10 |
| R-03 | Prompt injection indireta | Ordem escondida dentro de um boletim, laudo ou descrição de alarme colado pelo operador | S — alto | O guardrail de entrada sinaliza o texto suspeito sem bloquear a tarefa; todo texto colado e todo retorno de tool entra no contexto marcado como DADO; o assistente avisa sobre a instrução e continua a análise | Regra 9 · Blocos 2 e 3 · C11 |
| R-04 | Risco operacional | Orientar a abrir painel energizado, subir em pórtico ou entrar na pista sem procedimento | S — alto | Recusa obrigatória; indicação do POP pela tool; NR-10, NR-35 e profissional habilitado | Regras 5, 6 · Bloco 2 sinaliza o pedido perigoso e o envia ao modelo com a sinalização · tool `consultar_procedimento` · C12, C15, C16 |
| R-05 | Falso negativo | Uma falha real classificada como AMBIENTAL some da tela, por exemplo câmera de serra sem imagem durante neblina | S — alto | AMBIENTAL só com leitura da SCA; laço nunca é ambiental; na dúvida, INDETERMINADO com verificação em 30 min; objetivo diz que deslocamento a mais é preferível a alarme ignorado | Bloco OBJETIVO · Regra 3 · Bloco 7 · MC-01 · C03, C11, C13, C17, C18 |
| R-06 | Viés na priorização | Priorizar sempre a mesma praça, região ou quem insiste mais | S, F — médio | Prioridade calculada só pela matriz MC-01, com critérios escritos e auditáveis; pressão do usuário não muda a prioridade | Regra 4 · MC-01 · C03, C06, C13 |
| R-07 | LGPD — dado pessoal | Placas capturadas por OCR, imagens de ocupantes ou nomes de empregados entrando no prompt ou na base | L, R — alto | Esses dados não entram na base; tools não devolvem placa; o assistente mascara e recusa pedidos de identificação | Regra 10 · Base ("O que nunca entra") · convenção `dados_pessoais` das tools · Bloco 7 · C09 |
| R-08 | Confidencialidade operacional | Histórico de falhas e dados de arrecadação enviados para uma API de terceiros | L, F — médio | Modelo local no Ollama, sem envio externo; repositório só com dados simulados; `.gitignore` bloqueia `.env` e chaves | Bloco 4 (modelo local) · Base · `.gitignore` |
| R-09 | Excesso de confiança | Operador trata a hipótese do modelo como diagnóstico confirmado | S, F — médio | FATO × HIPÓTESE rotulados; "Nível de confiança" obrigatório; OS só com confirmação humana | Regras 2, 7, 11 · FORMATO · C04, C05, C15, C18 |
| R-10 | Contexto estourado | Conversa longa ultrapassa a janela e o assistente perde o TAG ou mistura ativos | S — médio | Política de histórico (6 últimos turnos + resumo); TAG em análise repetido em toda resposta; resumo a cada 6 turnos | BORDA · Bloco 3 · Política de contexto do README (orçamento de 16.384 tokens, retornos de tools cortados em ~600 tokens) · C14 |
| R-11 | Ação indevida por tool | Abrir OS sem confirmação, abrir OS duplicada (por exemplo, reenviar depois de um tempo esgotado) ou dizer que abriu quando a chamada falhou | F — médio | Parâmetro `confirmado_pelo_operador` obrigatório e igual a `true`; erro `OS_DUPLICADA`; nunca anunciar OS sem o número retornado | Regra 7 · Bloco 6 · `tools_spec.json` (`id_solicitacao` evita OS duplicada depois de tempo esgotado) · C04, C05, C16, C19 |

## LGPD: o que entra e o que nunca entra

| Entra na base e no prompt | Nunca entra |
|---|---|
| TAG, tipo, km e criticidade dos ativos | Placas de veículos e imagens de ocupantes |
| Histórico de falhas simulado, sem nomes | Nomes, matrículas ou dados de empregados e motoristas |
| Leituras ambientais das estações SCA | Valores de arrecadação e contratos |
| POPs, plano preventivo, matriz de criticidade, normas | Credenciais, IPs e senhas de equipamentos |
| Texto colado pelo operador, tratado como dado | Qualquer dado real da Motiva |

Se uma placa ou um nome aparecer em um alarme ou em um boletim colado, o assistente não o repete, substitui por `[placa removida]` e não o usa na análise (regra 10). Como o modelo roda localmente, nenhum dado da conversa sai da máquina do CCO.

## Responsabilidade

O assistente recomenda; quem decide é o operador do CCO, e quem executa é o profissional habilitado. Ele não emite laudo, ART ou parecer (regra 5), não libera nem bloqueia equipamento e não executa nenhuma ação sem confirmação humana (regra 7). A equipe que desenvolve o assistente responde pelo prompt, pelos testes e pela base: por isso o eval set roda de novo a cada mudança no prompt antes de qualquer uso.
