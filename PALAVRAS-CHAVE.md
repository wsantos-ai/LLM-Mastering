# Palavras-chave

Descrições objetivas e curtas de termos usados no estudo de LLMs, em ordem alfabética.
Aprofundamentos ficam nas notas dos tópicos correspondentes (pasta indicada em "Ver mais"), não aqui.

| Termo | Definição objetiva | Ver mais |
|---|---|---|
| Bias (viés) | Disparidade sistemática no comportamento do modelo associada a grupos sociais, herdada da distribuição do texto de treino. É descritivo: mede-se. Distingue-se em dano alocativo, representacional e de qualidade de serviço. | [01-fundamentos/bias-e-fairness.md](01-fundamentos/bias-e-fairness.md) |
| Compressão de modelos | Conjunto de técnicas que reduz o tamanho geral de um modelo sem comprometer significativamente sua precisão, removendo cálculos redundantes, podando parâmetros e otimizando armazenamento. | [04-fine-tuning/compressao-de-modelos.md](04-fine-tuning/compressao-de-modelos.md) |
| Deduplicação (deduplication) | Remoção de trechos repetidos do conjunto de dados de treino — de documentos idênticos a sequências longas que reaparecem em milhares de páginas. Reduz memorização e risco de vazamento, corta custo de treino e evita contaminação do conjunto de teste. | [01-fundamentos/deduplicacao.md](01-fundamentos/deduplicacao.md) |
| Embedding | Representação numérica (vetor) de um token, palavra ou trecho de texto, que captura seu significado de forma que itens semanticamente parecidos fiquem próximos nesse espaço vetorial. | [01-fundamentos](01-fundamentos/) |
| Epoch | Uma passagem completa do modelo por todo o conjunto de dados de treinamento. | [04-fine-tuning](04-fine-tuning/) |
| Fairness (justiça/equidade) | Critério normativo que define qual disparidade é aceitável e qual não é. É prescritivo: escolhe-se. Os critérios formais usuais são mutuamente incompatíveis fora de casos muito restritos, então não existe "justo" sem dizer segundo qual critério. | [01-fundamentos/bias-e-fairness.md](01-fundamentos/bias-e-fairness.md) |
| Grounding | Conexão entre a forma linguística (os símbolos que o modelo manipula) e aquilo a que ela se refere fora do texto — objetos, estados do mundo, uma fonte verificável. Usado em três sentidos distintos: semântico/filosófico, perceptual/multimodal e de aplicação ("responder com base em fonte"). | [01-fundamentos/grounding.md](01-fundamentos/grounding.md) |
| Inferência | Etapa em que um modelo já treinado recebe uma entrada e gera uma saída (ex.: uma resposta a um prompt), sem alterar seus pesos. | [01-fundamentos](01-fundamentos/) |
| Knowledge Distillation | Técnica de compressão em que um modelo menor ("aluno") é treinado para replicar o comportamento de um modelo maior ("professor"). | [04-fine-tuning/compressao-de-modelos.md](04-fine-tuning/compressao-de-modelos.md) |
| LLM | Large Language Model — modelo de linguagem treinado em grandes volumes de texto. | [01-fundamentos](01-fundamentos/) |
| LoRA (Low-Rank Adaptation) | Técnica de fine-tuning eficiente que treina apenas um pequeno conjunto de matrizes de baixo posto, reduzindo drasticamente os parâmetros treináveis. | [04-fine-tuning/compressao-de-modelos.md](04-fine-tuning/compressao-de-modelos.md) |
| Overfitting | Quando o modelo se ajusta demais aos dados de treinamento e perde capacidade de generalizar para dados novos. | [04-fine-tuning](04-fine-tuning/) |
| Pruning | Remoção de parâmetros do modelo considerados redundantes ou pouco importantes, reduzindo o tamanho sem perda significativa de precisão. | [04-fine-tuning/compressao-de-modelos.md](04-fine-tuning/compressao-de-modelos.md) |
| Quantização | Técnica que reduz a precisão numérica dos pesos do modelo (ex.: de 32 para 8 ou 4 bits) para diminuir uso de memória e custo computacional. | [07-ferramentas-ecossistema/quantizacao.md](07-ferramentas-ecossistema/quantizacao.md) |
| Token | Unidade mínima de texto (palavra, parte de palavra ou caractere) que um LLM usa como entrada e saída. | [01-fundamentos](01-fundamentos/) |
| Tokenização | Processo de dividir um texto em tokens antes de ser processado pelo modelo. | [01-fundamentos](01-fundamentos/) |
| Weight Sharing | Técnica que agrupa pesos semelhantes do modelo para reduzir o número de parâmetros distintos armazenados. | [04-fine-tuning/compressao-de-modelos.md](04-fine-tuning/compressao-de-modelos.md) |
