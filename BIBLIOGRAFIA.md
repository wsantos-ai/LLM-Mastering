# Bibliografia — livros

Acervo de **livros** e obras de referência longas, separado de [`FONTES.md`](FONTES.md)
(papers, docs, posts). Aqui se constrói a profundidade histórica: as obras que explicam como o
campo chegou ao estado atual, incluindo as anteriores à era dos LLMs.

ID estável `[B-xx]`, citável nas notas. Estado: `lido` / `parcial` / `na fila` / `catalogado`
(registrado, ainda sem intenção de leitura — **catalogar não é ler**, obra não lida não gera
nota). Dentro de cada eixo, ordem cronológica de publicação.

---

## Eixo: aplicação e engenharia

### [B-001] Designing Large Language Model Applications: A Holistic Approach to LLMs
- **Autor:** Suhas Pai
- **Publicação:** 2025-03-06 · **Editora:** O'Reilly Media
- **ISBN:** 9781098150501 (impresso) · 9781098150464 (ebook) · 366 p.
- **Estado:** parcial · **Origem:** citado em conversa de estudo (2026-08-12)
- **Descrição:** Guia prático voltado à passagem de protótipos de LLM para aplicações em
  produção. Cobre preparação de dados, arquitetura *transformer* e variações, adaptação de
  domínio, *fine-tuning*, otimização de inferência, integração com ferramentas e sistemas
  externos, e os paradigmas de RAG e agentes. Importa aqui por tratar decisões de projeto
  (quando customizar, quando recuperar, quando dar ferramentas ao modelo) em vez de só
  descrever técnicas isoladas.
- **Desdobramentos:** [01-fundamentos/grounding.md](01-fundamentos/grounding.md)
- **Trecho de interesse:** capítulo 2 trata de *grounding*, adota a definição de [F-005]
  (Chandu et al., 2021) e registra como **questões não resolvidas** se modelos multimodais
  realmente ajudam e se o efeito pode ser obtido só com volumes massivos de texto diverso.
  Citação literal transcrita em
  [01-fundamentos/grounding.md](01-fundamentos/grounding.md).
- **Errata:** 2026-08-12 — a entrada dizia `[não verificado]` sobre onde o tema aparecia e
  qual definição o autor usava. Ambos confirmados na mesma data pela citação literal do
  cap. 2. Pendência: a afirmação do autor de que modelos recentes vêm "provando o contrário"
  de [F-003] não vem acompanhada de evidência no trecho lido (pergunta 12 do Mapa da
  ignorância).

### [B-002] Mastering Ollama: Run, Optimize, and Deploy AI Models Locally
- **Autor:** Ted Winston
- **Publicação:** 2025-02-22 · **Editora:** `[a confirmar]` (publicação independente/Amazon;
  editora não confirmada na consulta de 2026-08-12)
- **Estado:** parcial · **Origem:** citado em
  [04-fine-tuning/compressao-de-modelos.md](04-fine-tuning/compressao-de-modelos.md)
- **Descrição:** Guia operacional de execução local de LLMs com Ollama. Cobre otimização e
  implantação, incluindo quantização e compressão de modelos. Serve como referência de prática
  (custo, memória, *trade-offs* de rodar modelo na própria máquina), não como fundamentação
  teórica.
- **Desdobramentos:** [04-fine-tuning/compressao-de-modelos.md](04-fine-tuning/compressao-de-modelos.md),
  [07-ferramentas-ecossistema/quantizacao.md](07-ferramentas-ecossistema/quantizacao.md)

---

## Eixo: impacto social e ética

### [B-003] Fairness and Machine Learning: Limitations and Opportunities
- **Autores:** Solon Barocas, Moritz Hardt, Arvind Narayanan
- **Publicação:** 2023-12-19 · **Editora:** MIT Press (série *Adaptive Computation and Machine
  Learning*)
- **ISBN:** 9780262048613 · 340 p. · Texto integral disponível em
  [fairmlbook.org](https://fairmlbook.org/) (CC BY-NC-ND 4.0)
- **Estado:** catalogado · **Origem:** `sugerido` — obra de referência do campo ao estudar
  [F-011] e [F-014] (Barocas é coautor de [F-011]; Narayanan, de [F-010])
- **Descrição:** Tratamento sistemático de justiça em aprendizado de máquina: de onde vem a
  disparidade, os critérios formais de *fairness* e por que eles conflitam entre si, e os
  limites do que uma correção estatística consegue resolver quando o problema é social. Serve
  como base conceitual para não tratar viés em LLM como um bug isolado de corpus, e cobre em
  profundidade o resultado de impossibilidade que [F-014] formaliza.
- **Desdobramentos:** [01-fundamentos/bias-e-fairness.md](01-fundamentos/bias-e-fairness.md)

---

## Candidatas

Sugestões de expansão do acervo. **Só sobem para o acervo principal quando aprovadas.** Todas
verificadas quanto a título, autor, editora e ano.

### Motivadas por [B-001] / pelo tema *grounding*

| Obra | Autor(es) | Ano · Editora | Papel | Por que |
|---|---|---|---|---|
| Understanding Computers and Cognition: A New Foundation for Design | Terry Winograd, Fernando Flores | 1986 · Ablex Publishing | **Antecedente** | Anterior à formulação de Harnad, argumenta que sistemas computacionais não têm acesso ao contexto de ação que dá sentido à linguagem — a raiz do problema de *grounding* aplicada a projeto de sistemas. Winograd vinha do SHRDLU, o caso emblemático de "mundo de brinquedo" onde o grounding era artificialmente resolvido. |
| The Embodied Mind: Cognitive Science and Human Experience | Francisco J. Varela, Evan Thompson, Eleanor Rosch | 1991 · MIT Press | **Contraponto / ponte** | Uma das obras fundadoras da cognição corporificada: significado emerge da interação de um corpo com um ambiente, não de manipulação de representações. É a tese contrária à de que texto puro basta ([F-004]), e liga o eixo técnico ao eixo filosofia/ciência cognitiva. |
| Linguistics for the Age of AI | Marjorie McShane, Sergei Nirenburg | 2021 · MIT Press (ISBN 9780262045582) | **Contraponto / aprofundamento** | Defesa contemporânea de agentes que interpretam linguagem via modelagem cognitiva explícita e conhecimento estruturado, em oposição à abordagem puramente estatística. Evita que a bibliografia fique restrita à escola conexionista. Disponível em acesso aberto pela MIT Press. |

### Motivadas por [B-003] / pelo tema *bias e fairness*

| Obra | Autor(es) | Ano · Editora | Papel | Por que |
|---|---|---|---|---|
| Weapons of Math Destruction: How Big Data Increases Inequality and Threatens Democracy | Cathy O'Neil | 2016 · Crown (ISBN 9780553418811) | **Antecedente** | Popularizou, antes da era dos LLMs, a tese de que sistemas estatísticos em escala reproduzem e amplificam desigualdade sob aparência de neutralidade. É o antecedente direto do enquadramento de [B-003], em registro não técnico. |
| Algorithms of Oppression: How Search Engines Reinforce Racism | Safiya Umoja Noble | 2018 · NYU Press (ISBN 9781479837243) | **Ponte** | Estuda viés em sistemas de **linguagem e busca** especificamente — o caso mais próximo de um LLM antes dos LLMs. Liga a discussão estatística de [B-003] a como o corpus da web codifica representação social, que é exatamente o achado de [F-013]. |
| Atlas of AI: Power, Politics, and the Planetary Costs of Artificial Intelligence | Kate Crawford | 2021 · Yale University Press (ISBN 9780300209570) | **Ponte / contraponto** | Desloca o problema de "corrigir o modelo" para as condições materiais que o produzem: dados, trabalho, energia, extração. Contraponto à leitura de que viés é uma propriedade ajustável do sistema. |
