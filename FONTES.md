# Fontes de estudo

Materiais usados como referência nos estudos sobre LLM (papers, documentação oficial, posts,
vídeos, cursos). **Livros ficam em [`BIBLIOGRAFIA.md`](BIBLIOGRAFIA.md)**, com IDs `[B-xx]`.

Cada fonte tem um ID estável `[F-xx]`, citado nas notas. Estado de leitura: `lida`,
`parcial`, `na fila`.

Se as notas são as aulas — com piada, analogia e exercício —, este arquivo é **a lista de
presença dos autores que fizeram o trabalho pesado**. Por isso aqui não tem trocadilho: linha
de fonte é dado, e dado errado numa tabela dessas contamina toda aula que citar o ID.
Regra prática: **fonte lida gera pelo menos uma nota ou uma pergunta.** Fonte lida e sem
desdobramento é aquele curso que a gente comprou na promoção e assistiu no 2×.

## Arquitetura e fundamentos

| ID | Título | Autores / Org. | Data | Tipo | Link | Estado | O que essa fonte resolve |
|---|---|---|---|---|---|---|---|
| F-001 | Transformer Architecture Explained With Self-Attention Mechanism | Codecademy | — | Artigo (secundário) | [codecademy.com](https://www.codecademy.com/article/transformer-architecture-self-attention-mechanism) | parcial | Primeira visão geral da arquitetura *transformer* e do mecanismo de *self-attention*. Tópico: [01-fundamentos](01-fundamentos/) |
| F-015 | Attention Is All You Need | Ashish Vaswani, Noam Shazeer, Niki Parmar, Jakob Uszkoreit, Llion Jones, Aidan N. Gomez, Lukasz Kaiser, Illia Polosukhin | 2017-06-12 (v1); v7 em 2023-08-02 | Paper (NeurIPS 2017) | [arxiv.org/abs/1706.03762](https://arxiv.org/abs/1706.03762) | na fila | Fonte primária da arquitetura *transformer* e da atenção multi-cabeça. Base para entender por que a geração autorregressiva recalcula chaves e valores — o problema que o KV cache resolve. |

## Semântica, significado e grounding

| ID | Título | Autores / Org. | Data | Tipo | Link | Estado | O que essa fonte resolve |
|---|---|---|---|---|---|---|---|
| F-002 | The Symbol Grounding Problem | Stevan Harnad | 1990-06 | Paper (*Physica D*, 42(1–3), 335–346) | [doi.org/10.1016/0167-2789(90)90087-6](https://doi.org/10.1016/0167-2789(90)90087-6) · [PDF](https://www.cs.ox.ac.uk/activities/ieg/e-library/sources/harnad90_sgproblem.pdf) | parcial | Formulação original do problema: como símbolos de um sistema formal adquirem significado sem depender de um intérprete externo. Origem histórica do tema de *grounding*. |
| F-003 | Climbing towards NLU: On Meaning, Form, and Understanding in the Age of Data | Emily M. Bender, Alexander Koller | 2020 | Paper (ACL 2020, p. 5185–5198) | [aclanthology.org/2020.acl-main.463](https://aclanthology.org/2020.acl-main.463/) | parcial | Distinção forma × significado e o argumento de que treino só em forma não permite aprender significado. Traz a experiência mental do polvo. |
| F-004 | The Vector Grounding Problem | Dimitri Coelho Mollo, Raphaël Millière | 2023-04-04 (v1; v3 em 2025-12-09) | Paper (arXiv; aceito em *Philosophy and the Mind Sciences*) | [arxiv.org/abs/2304.01481](https://arxiv.org/abs/2304.01481) | parcial | Separa *grounding referencial* dos demais sentidos do termo e defende que LLMs podem alcançá-lo sem multimodalidade — contraponto direto a [F-003]. |

| F-005 | Grounding 'Grounding' in NLP | Khyathi Raghavi Chandu, Yonatan Bisk, Alan W Black | 2021 | Paper (Findings of ACL-IJCNLP 2021, p. 4283–4305) | [aclanthology.org/2021.findings-acl.375](https://aclanthology.org/2021.findings-acl.375/) · [PDF](https://www.cs.cmu.edu/~awb/papers/2021.findings-acl.375.pdf) | na fila | Origem da definição de grounding citada em [B-001]. Mostra que PLN usa o termo de forma ampla (qualquer ligação de texto a dado ou modalidade não textual), enquanto a ciência cognitiva o define de forma mais estrita. É a fonte que justifica separar os sentidos do termo. |

## Dados de treino, memorização e privacidade

| ID | Título | Autores / Org. | Data | Tipo | Link | Estado | O que essa fonte resolve |
|---|---|---|---|---|---|---|---|
| F-006 | Deduplicating Training Data Makes Language Models Better | Katherine Lee, Daphne Ippolito, Andrew Nystrom, Chiyuan Zhang, Douglas Eck, Chris Callison-Burch, Nicholas Carlini | 2021-07-14 (v1); 2022-03-24 (v2) | Paper (ACL 2022) | [arxiv.org/abs/2107.06499](https://arxiv.org/abs/2107.06499) | parcial | Quantifica a duplicação em corpora de treino (C4, etc.) e mostra o efeito de deduplicar: 10× menos memorização, mesma ou melhor acurácia com menos passos, e correção da sobreposição treino–teste. Descreve os métodos (MinHash/LSH e *suffix array*). |
| F-007 | Deduplicating Training Data Mitigates Privacy Risks in Language Models | Nikhil Kandpal, Eric Wallace, Colin Raffel | 2022-02-14 (v1); rev. 2022-12-20 | Paper (ICML 2022) | [arxiv.org/abs/2202.06539](https://arxiv.org/abs/2202.06539) | parcial | Liga duplicação a risco de privacidade: relação superlinear entre nº de repetições e taxa de regeneração; ataques de extração dependem em grande parte da duplicação. |
| F-008 | Scaling Data-Constrained Language Models | Niklas Muennighoff, Alexander M. Rush, Boaz Barak, Teven Le Scao, Aleksandra Piktus, Nouamane Tazi, Sampo Pyysalo, Thomas Wolf, Colin Raffel | 2023-05-25 | Paper (arXiv) | [arxiv.org/abs/2305.16264](https://arxiv.org/abs/2305.16264) | parcial | Contraponto: em cenário de dados escassos, até 4 épocas de dados repetidos têm efeito desprezível na *loss*. Define quando repetir é aceitável. |
| F-009 | SemDeDup: Data-efficient learning at web-scale through semantic deduplication | Amro Abbas, Kushal Tirumala, Dániel Simig, Surya Ganguli, Ari S. Morcos | 2023-03-16 (v1); rev. 2023-03-22 | Paper (arXiv) | [arxiv.org/abs/2303.09540](https://arxiv.org/abs/2303.09540) | na fila | Deduplicação **semântica** via *embeddings*, além do casamento de string: em LAION, remover 50% dos dados com perda mínima e metade do tempo de treino. |

## Inferência e serving

| ID | Título | Autores / Org. | Data | Tipo | Link | Estado | O que essa fonte resolve |
|---|---|---|---|---|---|---|---|
| F-016 | Fast Transformer Decoding: One Write-Head is All You Need | Noam Shazeer | 2019-11-06 | Paper (arXiv) | [arxiv.org/abs/1911.02150](https://arxiv.org/abs/1911.02150) | parcial | Nomeia o gargalo real da inferência incremental: o custo de **largura de banda de memória** de recarregar repetidamente os tensores de chaves e valores. Propõe *multi-query attention* (MQA) para encolher esses tensores. É a fonte que explica por que o KV cache troca compute por memória — e por que a memória vira o novo limite. |
| F-017 | GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints | Joshua Ainslie, James Lee-Thorp, Michiel de Jong, Yury Zemlyanskiy, Federico Lebrón, Sumit Sanghai | 2023-05-22 | Paper (arXiv; EMNLP 2023) | [arxiv.org/abs/2305.13245](https://arxiv.org/abs/2305.13245) | parcial | Meio-termo entre MQA e atenção multi-cabeça: número intermediário de cabeças de chave-valor, com qualidade próxima à MHA e velocidade próxima à MQA, mais receita de *uptraining* usando 5% do compute de pré-treino. |
| F-018 | Efficient Memory Management for Large Language Model Serving with PagedAttention | Woosuk Kwon, Zhuohan Li, Siyuan Zhuang, Ying Sheng, Lianmin Zheng, Cody Hao Yu, Joseph E. Gonzalez, Hao Zhang, Ion Stoica | 2023-09-12 | Paper (arXiv; SOSP 2023) | [arxiv.org/abs/2309.06180](https://arxiv.org/abs/2309.06180) | parcial | Quantifica o custo do KV cache em produção (800 KB por token em OPT-13B) e mostra que sistemas anteriores usavam de fato só 20,4%–38,2% da memória reservada para ele. Origem do PagedAttention e do vLLM: ganho de 2–4× em *throughput* com a mesma latência. |
| F-019 | Efficiently Serving LLMs (curso) | Travis Addair (Co-Founder e CTO, Predibase) / DeepLearning.AI | data de publicação não confirmada (consulta em 2026-08-14) | Curso (vídeo + notebooks) | [deeplearning.ai/courses/efficiently-serving-llms](https://www.deeplearning.ai/courses/efficiently-serving-llms) | parcial | Curso onde o tema apareceu. KV caching é tratado dentro da lição **Text Generation** (não há lição com esse título); as lições são Introduction, Text Generation, Batching, Continuous Batching, Quantization, Low-Rank Adaptation, Multi-LoRA Inference, LoRAX, Conclusion, Quiz. A descrição promete implementar em código *"KV caching, continuous batching, and model quantization, and benchmark their impacts on inference throughput and latency"*. Fonte de didática e de código, não de resultado experimental. |

## Viés, justiça e impacto social

| ID | Título | Autores / Org. | Data | Tipo | Link | Estado | O que essa fonte resolve |
|---|---|---|---|---|---|---|---|
| F-010 | Semantics derived automatically from language corpora contain human-like biases | Aylin Caliskan, Joanna J. Bryson, Arvind Narayanan | 2017-04-14 | Paper (*Science*, 356(6334), 183–186) | [doi.org/10.1126/science.aal4230](https://doi.org/10.1126/science.aal4230) · [preprint](https://arxiv.org/abs/1608.07187) | parcial | Demonstra que associações aprendidas de corpora reproduzem vieses humanos documentados (replica o IAT). Base para tratar viés como herdado da distribuição do texto, não inventado pelo modelo. Origem do WEAT. |
| F-011 | Language (Technology) is Power: A Critical Survey of "Bias" in NLP | Su Lin Blodgett, Solon Barocas, Hal Daumé III, Hanna Wallach | 2020 | Paper (ACL 2020, p. 5454–5476) | [aclanthology.org/2020.acl-main.485](https://aclanthology.org/2020.acl-main.485/) | parcial | Revisa 146 artigos e mostra que "viés" é usado de forma vaga e sem raciocínio normativo. Exige nomear qual comportamento é danoso, de que forma, para quem e por quê. É a fonte que disciplina o vocabulário. |
| F-012 | On the Dangers of Stochastic Parrots: Can Language Models Be Too Big? | Emily M. Bender, Timnit Gebru, Angelina McMillan-Major, Shmargaret Shmitchell | 2021 | Paper (FAccT '21, p. 610–623) | [doi.org/10.1145/3442188.3445922](https://doi.org/10.1145/3442188.3445922) | na fila | Argumento de que escala de corpus não implica diversidade nem auditabilidade, e catálogo dos riscos de LLMs muito grandes. |
| F-013 | Documenting Large Webtext Corpora: A Case Study on the Colossal Clean Crawled Corpus | Jesse Dodge, Maarten Sap, Ana Marasović, William Agnew, Gabriel Ilharco, Dirk Groeneveld, Margaret Mitchell, Matt Gardner | 2021-04-18 (rev. 2021-09-30) | Paper (EMNLP 2021) | [arxiv.org/abs/2104.08758](https://arxiv.org/abs/2104.08758) | parcial | Mede o que a curadoria do C4 de fato faz: filtragem por lista de bloqueio remove desproporcionalmente texto de e sobre minorias, além de revelar contaminação de *benchmark* e texto gerado por máquina. |
| F-014 | Inherent Trade-Offs in the Fair Determination of Risk Scores | Jon Kleinberg, Sendhil Mullainathan, Manish Raghavan | 2016-09-19 (v1) | Paper (ITCS 2017) | [arxiv.org/abs/1609.05807](https://arxiv.org/abs/1609.05807) | parcial | Prova que três critérios usuais de *fairness* não podem ser satisfeitos ao mesmo tempo salvo em casos muito restritos. Converte "ser justo" de objetivo a maximizar em escolha com *trade-off* explícito. |

Notas que usam estas fontes: [01-fundamentos/grounding.md](01-fundamentos/grounding.md),
[01-fundamentos/deduplicacao.md](01-fundamentos/deduplicacao.md),
[01-fundamentos/bias-e-fairness.md](01-fundamentos/bias-e-fairness.md),
[01-fundamentos/kv-caching.md](01-fundamentos/kv-caching.md).
