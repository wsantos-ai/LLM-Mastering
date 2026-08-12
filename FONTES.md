# Fontes de estudo

Materiais usados como referência nos estudos sobre LLM (papers, documentação oficial, posts,
vídeos, cursos). **Livros ficam em [`BIBLIOGRAFIA.md`](BIBLIOGRAFIA.md)**, com IDs `[B-xx]`.

Cada fonte tem um ID estável `[F-xx]`, citado nas notas. Estado de leitura: `lida`,
`parcial`, `na fila`.

## Arquitetura e fundamentos

| ID | Título | Autores / Org. | Data | Tipo | Link | Estado | O que essa fonte resolve |
|---|---|---|---|---|---|---|---|
| F-001 | Transformer Architecture Explained With Self-Attention Mechanism | Codecademy | — | Artigo (secundário) | [codecademy.com](https://www.codecademy.com/article/transformer-architecture-self-attention-mechanism) | parcial | Primeira visão geral da arquitetura *transformer* e do mecanismo de *self-attention*. Tópico: [01-fundamentos](01-fundamentos/) |

## Semântica, significado e grounding

| ID | Título | Autores / Org. | Data | Tipo | Link | Estado | O que essa fonte resolve |
|---|---|---|---|---|---|---|---|
| F-002 | The Symbol Grounding Problem | Stevan Harnad | 1990-06 | Paper (*Physica D*, 42(1–3), 335–346) | [doi.org/10.1016/0167-2789(90)90087-6](https://doi.org/10.1016/0167-2789(90)90087-6) · [PDF](https://www.cs.ox.ac.uk/activities/ieg/e-library/sources/harnad90_sgproblem.pdf) | parcial | Formulação original do problema: como símbolos de um sistema formal adquirem significado sem depender de um intérprete externo. Origem histórica do tema de *grounding*. |
| F-003 | Climbing towards NLU: On Meaning, Form, and Understanding in the Age of Data | Emily M. Bender, Alexander Koller | 2020 | Paper (ACL 2020, p. 5185–5198) | [aclanthology.org/2020.acl-main.463](https://aclanthology.org/2020.acl-main.463/) | parcial | Distinção forma × significado e o argumento de que treino só em forma não permite aprender significado. Traz a experiência mental do polvo. |
| F-004 | The Vector Grounding Problem | Dimitri Coelho Mollo, Raphaël Millière | 2023-04-04 (v1; v3 em 2025-12-09) | Paper (arXiv; aceito em *Philosophy and the Mind Sciences*) | [arxiv.org/abs/2304.01481](https://arxiv.org/abs/2304.01481) | parcial | Separa *grounding referencial* dos demais sentidos do termo e defende que LLMs podem alcançá-lo sem multimodalidade — contraponto direto a [F-003]. |

| F-005 | Grounding 'Grounding' in NLP | Khyathi Raghavi Chandu, Yonatan Bisk, Alan W Black | 2021 | Paper (Findings of ACL-IJCNLP 2021, p. 4283–4305) | [aclanthology.org/2021.findings-acl.375](https://aclanthology.org/2021.findings-acl.375/) · [PDF](https://www.cs.cmu.edu/~awb/papers/2021.findings-acl.375.pdf) | na fila | Origem da definição de grounding citada em [B-001]. Mostra que PLN usa o termo de forma ampla (qualquer ligação de texto a dado ou modalidade não textual), enquanto a ciência cognitiva o define de forma mais estrita. É a fonte que justifica separar os sentidos do termo. |

Notas que usam estas fontes: [01-fundamentos/grounding.md](01-fundamentos/grounding.md).
