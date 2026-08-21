# Compressão de modelos

> Data: 2026-08-11 · Reescrita em formato de aula: 2026-08-14
> Tópico: [Palavras-chave: Compressão de modelos](../PALAVRAS-CHAVE.md)
> Fontes: [B-002] Winston, *Mastering Ollama: Run, Optimize, and Deploy AI Models Locally*

## Chamada da aula

Toda família tem aquele parente que leva mala de 30 kg para um fim de semana. "Vai que faz
frio. Vai que tem festa. Vai que eu preciso de três carregadores." Compressão de modelos é a
arte de olhar para essa mala e perguntar, com muito carinho: *você usou o smoking na praia da
última vez?*

Spoiler: o LLM não usou. Tem parâmetro nesse modelo que nunca foi convocado para nada — o
estagiário que aparece na foto do time e some no resto do ano.

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

## Aula

Compressão de modelos é um guarda-chuva de técnicas — nem todas atuam no mesmo momento do ciclo de vida do modelo. Vou apresentar os quatro como se fossem quatro estratégias de quem
precisa caber num apartamento menor:

- **Pruning** atua removendo pesos ou neurônios cuja contribuição para a saída é marginal (ex.: pesos próximos de zero). Reduz o número de parâmetros ativos, o que diminui tamanho e custo computacional. *É a faxina do armário:* aquilo que você não veste há dois anos vai para a doação. O modelo não fica com "buracos de conhecimento" porque, por definição, você tirou o que quase não influenciava a resposta.
- **Knowledge Distillation** não corta o modelo original — cria um modelo novo e menor, treinado para imitar as saídas (ou distribuições internas) do modelo grande. É útil quando se quer um modelo compacto para produção, mantendo boa parte da capacidade do modelo original. *É o estagiário que fica seis meses ao lado do sênior anotando tudo:* ele não leu os mesmos dez mil livros que o chefe, ele copiou o **jeito de responder** do chefe. Mais barato de manter, e no dia a dia entrega quase o mesmo.
- **LoRA** não comprime o modelo base em si; em vez disso, evita treinar todos os parâmetros do modelo durante o fine-tuning, treinando apenas matrizes de baixo posto (low-rank) inseridas em camadas específicas. Isso reduz drasticamente o número de parâmetros treináveis, tornando o fine-tuning mais barato e rápido. *É o post-it na parede do escritório:* você não reformou o prédio para mudar o horário da reunião, colou um bilhete na porta.
- **Weight Sharing** reduz a quantidade de valores distintos armazenados fazendo com que múltiplas conexões usem o mesmo peso (agrupamento/clustering de pesos parecidos), em vez de cada uma ter seu próprio valor único. *É o uniforme escolar:* em vez de guardar a roupa de cada aluno, você guarda três tamanhos e distribui.

**Onde as analogias trincam:** todas elas sugerem que dá para saber *de antemão* o que é
supérfluo. Não dá — "peso próximo de zero" é heurística, não certeza, e camisa que você não usa
há dois anos pode ser justamente a do casamento do mês que vem. É por isso que compressão
sempre vem acompanhada de reavaliação: o critério de "pouco importante" é estatístico, e
estatística não sabe qual é a sua tarefa favorita.

## Exemplos práticos

1. **O pipeline combinado.** Aplicar pruning e **depois** quantizar é o caso típico: um reduz
   a *quantidade* de parâmetros, o outro reduz o *custo de cada um*. Funciona porque atacam
   eixos diferentes do mesmo problema — é como tirar peso da mala e depois usar um saco a
   vácuo. `[hipótese]` a ordem importa (podar antes evita gastar bits quantizando peso que
   ia ser jogado fora), mas não confirmei isso em fonte; virou pergunta aberta.
2. **O contra-caso: LoRA não é bem compressão.** Se o seu problema é "o modelo não cabe na
   GPU de inferência", LoRA sozinho não resolve — o modelo base continua do mesmo tamanho, com
   um adaptador pequeno pendurado. LoRA resolve outro problema: "não tenho GPU suficiente para
   **treinar** todos os parâmetros". Raciocínio: pergunte sempre *qual* custo está doendo,
   treino ou inferência. Cada técnica só cura uma dessas dores.

## Exercícios

1. **Complete a frase com bom humor:** "Pruning está para o armário assim como distillation
   está para ______."
2. **Ache o erro cômico:** "Fiz distillation do modelo de 70B para 7B e agora tenho dois
   modelos de 70B de qualidade, um deles pesando 7B — a matemática é linda."
3. **Transforme a frase:** reescreva "LoRA é uma técnica de compressão de modelos" de modo que
   fique tecnicamente correta sem perder a piada.

<details>
<summary>Gabarito comentado</summary>

1. Para **o estagiário** (ou "para o aprendiz que copia o mestre"). O ponto que a frase fixa é
   a diferença estrutural: pruning mexe no **modelo que já existe**; distillation **cria um
   modelo novo**. Duas caixas de ferramentas diferentes, e é o erro mais comum tratá-las como
   sinônimo de "deixar menor".
2. O erro é o "dois modelos de 70B de qualidade". Distillation transfere **comportamento**, não
   capacidade integral: o aluno aprende a imitar as saídas do professor, e mantém *boa parte*
   da capacidade — não toda. Se 7B pudessem carregar tudo que 70B carregam, ninguém teria
   treinado o de 70B.
3. Por exemplo: "LoRA é a técnica que comprime a **conta do fine-tuning**, não o modelo — ela
   emagrece o boleto, não o paciente." O que continuou igual: o ganho real de armazenamento dos
   *adapters* frente a um fine-tuning completo. O que mudou: o objeto que encolhe.

</details>

## Minha análise

O livro trata compressão de modelos com os mesmos trade-offs usados para [Quantização](../07-ferramentas-ecossistema/quantizacao.md) — o que sugere que, na prática, essas técnicas são complementares e frequentemente combinadas (ex.: aplicar pruning e depois quantizar o modelo resultante). Vale notar que os quatro métodos citados não são equivalentes em propósito:

- Pruning e Weight Sharing comprimem o modelo existente diretamente.
- Knowledge Distillation cria um modelo novo e menor a partir de outro.
- LoRA, estritamente falando, é uma técnica de *fine-tuning eficiente* (reduz parâmetros treináveis), não uma técnica de compressão do modelo final — embora o resultado (adapters pequenos) também economize armazenamento comparado a um fine-tuning completo. Faz sentido o livro agrupá-la aqui pelo ganho prático de eficiência, mas vale manter essa distinção conceitual clara.

Isso reforça uma leitura mais ampla: "reduzir custo computacional de um LLM" é um objetivo que se persegue em várias frentes (quantização = precisão numérica dos pesos; compressão = quantidade/redundância de parâmetros; LoRA = quantidade de parâmetros *treináveis*), e o livro parece apresentá-las como parte de uma mesma caixa de ferramentas de otimização.

## Resumo cômico

- **Pruning:** doação de roupa que você não usa. Corta parâmetro que quase não influencia.
- **Distillation:** estagiário aplicado. Modelo novo e menor imitando o grandão.
- **Weight sharing:** uniforme escolar. Menos valores distintos guardados.
- **LoRA:** post-it na porta. Barateia o *treino*, não o modelo.

> **Takeaway:** compressão é mala de viagem — a pergunta certa nunca é "cabe?", é "o que eu
> deixo em casa e quem vai sentir falta". Se ninguém sentir falta, você comprimiu. Se o modelo
> começar a gaguejar em raciocínio, você esqueceu o passaporte.

## Mapa da ignorância

- Como pruning estruturado difere de pruning não-estruturado, e o impacto de cada um no ganho real de velocidade?
- Como funciona a distillation na prática (loss usado, o que é replicado — logits, respostas, ou representações internas)?
- Detalhar LoRA e QLoRA em uma nota própria de fine-tuning.
- Existe alguma ordem recomendada para combinar pruning + distillation + quantização no mesmo pipeline? (Ver a `[hipótese]` do exemplo 1: podar antes de quantizar parece desperdiçar menos, mas não achei fonte.)

## Referências relacionadas

- [Palavras-chave: Compressão de modelos](../PALAVRAS-CHAVE.md)
- [Quantização](../07-ferramentas-ecossistema/quantizacao.md)
- [Deduplicação](../01-fundamentos/deduplicacao.md) — a mesma economia, mas feita nos dados em
  vez de no modelo
