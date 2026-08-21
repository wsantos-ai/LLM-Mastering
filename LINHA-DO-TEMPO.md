# Linha do tempo

Principais acontecimentos e marcos históricos na evolução dos LLMs (e de sistemas de processamento de linguagem natural que os precedem).

Pense nesta tabela como a árvore genealógica da família: tem o tataravô de 1966 que respondia
tudo com outra pergunta e mesmo assim deixava as visitas emocionadas, e tem a geração atual,
que escreve seu e-mail e ainda erra a data do próprio lançamento (ver a nota no rodapé).

Aqui embaixo, porém, a piada acaba: **a tabela é metadado, e metadado não faz graça.** Cada
linha carrega ano, marco e fonte. Data parcial se escreve `2017-06` e se marca `[data parcial]`
— dia não se inventa nem para melhorar a narrativa.

| Ano | Marco | Fonte |
|---|---|---|
| 1966 | ELIZA — programa de computador para o estudo da comunicação em linguagem natural entre homem e máquina | — |
| 2017-06-12 | *Attention Is All You Need* — arquitetura *transformer*, base de praticamente todos os LLMs seguintes; a atenção multi-cabeça definida aqui é o que torna a geração autorregressiva cara e, por consequência, o que o KV cache existe para baratear | [F-015] · [arxiv.org/abs/1706.03762](https://arxiv.org/abs/1706.03762) |
| 2018 | GPT-1 | [paper](https://cdn.openai.com/research-covers/language-unsupervised/language_understanding_paper.pdf) |
| 2019 | GPT-2 | [paper](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf) |
| 2020 | GPT-3 | [paper](https://arxiv.org/pdf/2005.14165) |
| 2023 | GPT-4 | [paper](https://arxiv.org/pdf/2303.08774) |
| 2023-09-12 | PagedAttention / vLLM — trata o KV cache como memória paginada de sistema operacional; mede que sistemas anteriores usavam só 20,4%–38,2% da memória reservada ao cache e entrega 2–4× de *throughput* com a mesma latência. Marco de *serving*: desloca o limite de usuários simultâneos de cálculo para gestão de memória | [F-018] · [arxiv.org/abs/2309.06180](https://arxiv.org/abs/2309.06180) |
| 2025 | GPT-5 | [anúncio oficial (OpenAI, 07/08/2025)](https://deploymentsafety.openai.com/gpt-5/introduction) · [paper](https://arxiv.org/pdf/2601.03267) |

> ⚠️ Nota: a publicação oficial da OpenAI que apresenta o GPT-5 é datada de 7 de agosto de 2025, mas o link do paper referenciado nela é datado de 2026 (ID arXiv `2601.03267`, padrão `YYMM` = janeiro/2026). A data do marco nesta tabela segue o anúncio (2025); o paper em si parece ter sido publicado/atualizado depois do anúncio.
