# Decisões técnicas

Regra da disciplina: o caminho de execução precisa ser 100% gratuito. Todas as opções abaixo têm custo zero; as pagas não foram consideradas.

## 1. Modelo de linguagem (LLM)

| Critério | Qwen 2.5 7B Instruct (Ollama local) | Llama 3.1 8B Instruct (Ollama local) | Gemini Flash (Google AI Studio, plano gratuito) | Llama 3.3 70B (Groq, plano gratuito) |
|---|---|---|---|---|
| Custo de uso | Zero, roda na máquina | Zero, roda na máquina | Zero, com limite de requisições por minuto e por dia | Zero, com limite de requisições e de tokens |
| Licença / termos | Apache 2.0 | Llama 3.1 Community License | Termos do Google AI Studio; no plano gratuito o conteúdo pode ser usado para melhorar os produtos | Llama 3.3 Community License + termos da Groq |
| Qualidade em pt-BR | Boa (treino multilíngue com português) | Boa | Muito boa | Muito boa |
| Janela de contexto | 32K por padrão, até 128K com YaRN | 128K | Da ordem de 1M tokens | 128K |
| Privacidade | Dados não saem da máquina | Dados não saem da máquina | Dados vão para o Google | Dados vão para a Groq |
| Tool calling | Suportado pelo Ollama | Suportado pelo Ollama | Suportado | Suportado (API compatível com OpenAI) |
| Integração com Python | SDK `ollama` | SDK `ollama` | SDK `google-genai` | SDK `groq` |
| Hardware | ~5 GB de RAM (quantização de 4 bits) | ~5 a 6 GB de RAM | Nenhum | Nenhum |
| Maturidade / documentação | Alta | Alta | Alta | Média |

**Escolha: Qwen 2.5 7B Instruct rodando local no Ollama.**

- **Privacidade e LGPD.** Histórico de falhas e dados operacionais de uma concessionária não devem ir para uma API de terceiros (risco R-08). Rodando local, nada sai da máquina.
- **Licença Apache 2.0**, a mais permissiva das opções.
- **Português bom o suficiente** para uma tarefa curta e estruturada (classificar e preencher uma tabela), ajudada pelo few-shot do prompt.
- **Sem limite de requisições**, o que importa quando o eval set roda várias vezes.

**Trade-off aceito.** Um modelo de 7B erra mais que um modelo grande em nuvem em perguntas abertas. Compensamos com temperatura baixa, formato fixo, few-shot, regras verificáveis e o guardrail de saída. Se um integrante não tiver 8 GB de RAM livres, o caminho alternativo é o Gemini ou a Groq no plano gratuito, **somente com os dados simulados deste repositório**.

## 2. Parâmetros

| Parâmetro | Valor | Justificativa (Aula 02) |
|---|---|---|
| `temperature` | 0.2 | Triagem precisa ser repetível: o mesmo alarme deve receber a mesma classificação. Temperatura alta aumenta a variação e a chance de inventar. |
| `top_p` | 0.9 | Corta tokens muito improváveis sem engessar o texto. |
| `num_predict` (o `max_tokens` do Ollama) | 1000 | Cabe o bloco `<raciocinio>` (até 250 tokens), a tabela de até 8 linhas (~500 tokens) e a linha de confiança. |
| `num_ctx` | 16384 | O Ollama usa uma janela menor por padrão. 16.384 cabe o orçamento da [política de contexto](../README.md#12-política-de-contexto) com folga para chamadas encadeadas de tools, e fica dentro dos 32K que o Qwen 2.5 suporta sem extensão. Custo: cerca de 1 GB a mais de RAM para o cache de contexto. |

## 3. Vector store

| Critério | ChromaDB | FAISS |
|---|---|---|
| Custo de uso | Zero, roda local | Zero, roda local |
| Licença | Apache 2.0 | MIT |
| Privacidade | Local, nada sai da máquina | Local, nada sai da máquina |
| Filtro por metadados | Nativo (`where` por tipo de documento, tipo de ativo, data) | Não tem; o filtro precisa ser feito fora do índice |
| Persistência | Grava em disco sozinho | É preciso salvar e carregar o índice manualmente |
| Escala | Milhares a milhões de vetores, suficiente para a base (~150 chunks) | Muito alta (bilhões), além do necessário |
| Integração com Python | `pip install chromadb`; guarda texto e metadados junto do vetor | `pip install faiss-cpu`; guarda só vetores, texto e metadados ficam em outra estrutura |
| Maturidade / documentação | Boa, projeto mais novo (2022) e muito usado em RAG | Alta, mantido pela Meta desde 2017 |

**Escolha: ChromaDB.** A busca filtrada por tipo de documento e tipo de ativo evita trazer, por exemplo, o POP do pórtico para uma pergunta sobre câmera. O FAISS é mais rápido em grande escala, mas a base tem poucas centenas de trechos e a velocidade não faz diferença aqui.

## 4. Embeddings

| Critério | nomic-embed-text (Ollama) | multilingual-e5-base (Sentence Transformers) |
|---|---|---|
| Custo e privacidade | Local, zero | Local, zero |
| Português | Treinado principalmente em inglês | Multilíngue, inclui português |
| Entrada máxima | 8.192 tokens | 512 tokens |
| Licença | Apache 2.0 | MIT |

**Escolha: multilingual-e5-base.** A base e as perguntas são em português, e a qualidade da busca depende disso. O limite de 512 tokens definiu o tamanho máximo do chunk (450 tokens, ver [inventário](../data/knowledge_base/inventario.md)).

## 5. Orquestração, interface e ambiente

| Camada | Escolha | Por quê |
|---|---|---|
| Orquestração | Chamada direta pelo SDK do Ollama, com as peças do Módulo 3: `chamar_llm()`, histórico e `montar_prompt()` | O fluxo é linear e cabe em poucas funções; o LangChain acrescentaria uma camada que o grupo não precisa nesta escala e esconderia a montagem do prompt, que é justamente o que queremos controlar. |
| Interface | Streamlit (Sprint 03), ver [mockup](mockup_interface.png) | Gera uma tela de chat em Python sem front-end separado. |
| Ambiente | Python 3.10+, VS Code e Jupyter local | Experimentos ficam fora deste repositório, como exige a Sprint 01. |
| Versionamento | Git + GitHub público | Exigência da disciplina. |
