# Compressão de modelos

> Data: 2026-08-11
> Tópico: [Palavras-chave: Compressão de modelos](../PALAVRAS-CHAVE.md)
> Fontes: livro "Mastering Ollama: Run, Optimize, and Deploy AI Models Locally", Ted Winston

## Resumo

Conjunto de técnicas que reduzem o tamanho geral de um LLM sem comprometer significativamente sua capacidade de realizar tarefas com precisão, removendo cálculos redundantes, podando parâmetros pouco usados e otimizando requisitos de armazenamento.

## O que aprendi

- Objetivo: reduzir tamanho do modelo mantendo a precisão nas tarefas.
- Trade-offs (mesmo padrão observado em quantização):
  - Pequena perda de precisão — tipicamente mínima.
  - Às vezes, flexibilidade reduzida para fine-tuning.
  - Esses custos costumam ser pequenos frente aos ganhos de desempenho.
- Quatro métodos principais:
  - **Pruning**: remove parâmetros redundantes ou pouco importantes.
  - **Knowledge Distillation**: treina um modelo menor ("aluno") para replicar o comportamento de um modelo maior ("professor").
  - **LoRA (Low-Rank Adaptation)**: técnica de fine-tuning que reduz o número de parâmetros necessários para adaptar o modelo a uma tarefa específica.
  - **Weight Sharing**: agrupa pesos semelhantes para reduzir a quantidade de parâmetros distintos armazenados.
- Quando usar: principalmente em cenários de fine-tuning, para reduzir custos de treinamento.

## Detalhes

Compressão de modelos é um guarda-chuva de técnicas — nem todas atuam no mesmo momento do ciclo de vida do modelo:

- **Pruning** atua removendo pesos ou neurônios cuja contribuição para a saída é marginal (ex.: pesos próximos de zero). Reduz o número de parâmetros ativos, o que diminui tamanho e custo computacional.
- **Knowledge Distillation** não corta o modelo original — cria um modelo novo e menor, treinado para imitar as saídas (ou distribuições internas) do modelo grande. É útil quando se quer um modelo compacto para produção, mantendo boa parte da capacidade do modelo original.
- **LoRA** não comprime o modelo base em si; em vez disso, evita treinar todos os parâmetros do modelo durante o fine-tuning, treinando apenas matrizes de baixo posto (low-rank) inseridas em camadas específicas. Isso reduz drasticamente o número de parâmetros treináveis, tornando o fine-tuning mais barato e rápido.
- **Weight Sharing** reduz a quantidade de valores distintos armazenados fazendo com que múltiplas conexões usem o mesmo peso (agrupamento/clustering de pesos parecidos), em vez de cada uma ter seu próprio valor único.

## Minha análise

O livro trata compressão de modelos com os mesmos trade-offs usados para [Quantização](../07-ferramentas-ecossistema/quantizacao.md) — o que sugere que, na prática, essas técnicas são complementares e frequentemente combinadas (ex.: aplicar pruning e depois quantizar o modelo resultante). Vale notar que os quatro métodos citados não são equivalentes em propósito:

- Pruning e Weight Sharing comprimem o modelo existente diretamente.
- Knowledge Distillation cria um modelo novo e menor a partir de outro.
- LoRA, estritamente falando, é uma técnica de *fine-tuning eficiente* (reduz parâmetros treináveis), não uma técnica de compressão do modelo final — embora o resultado (adapters pequenos) também economize armazenamento comparado a um fine-tuning completo. Faz sentido o livro agrupá-la aqui pelo ganho prático de eficiência, mas vale manter essa distinção conceitual clara.

Isso reforça uma leitura mais ampla: "reduzir custo computacional de um LLM" é um objetivo que se persegue em várias frentes (quantização = precisão numérica dos pesos; compressão = quantidade/redundância de parâmetros; LoRA = quantidade de parâmetros *treináveis*), e o livro parece apresentá-las como parte de uma mesma caixa de ferramentas de otimização.

## Dúvidas / pontos a aprofundar

- Como pruning estruturado difere de pruning não-estruturado, e o impacto de cada um no ganho real de velocidade?
- Como funciona a distillation na prática (loss usado, o que é replicado — logits, respostas, ou representações internas)?
- Detalhar LoRA e QLoRA em uma nota própria de fine-tuning.
- Existe alguma ordem recomendada para combinar pruning + distillation + quantização no mesmo pipeline?

## Referências relacionadas

- [Palavras-chave: Compressão de modelos](../PALAVRAS-CHAVE.md)
- [Quantização](../07-ferramentas-ecossistema/quantizacao.md)
