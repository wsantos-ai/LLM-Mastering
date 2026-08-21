# KV caching (cache de chaves e valores)

> Data: 2026-08-14
> Tópico: [01-fundamentos](../) · [Palavras-chave: KV cache](../PALAVRAS-CHAVE.md)
> Fontes: [F-019] Addair / DeepLearning.AI, curso *Efficiently Serving LLMs* · [F-015] Vaswani et al. (2017) · [F-016] Shazeer (2019) · [F-017] Ainslie et al. (2023) · [F-018] Kwon et al. (2023)

## Chamada da aula

Todo mundo tem um amigo que conta história e, se for interrompido, **recomeça do começo**.
"Então, sábado eu acordei…" — o garçom chega, ele para. Volta: "Então, sábado eu acordei, tomei
café, peguei o carro…". Alguém pergunta as horas. "ENTÃO, SÁBADO EU ACORDEI…". Duas horas
depois, ninguém sabe o que aconteceu no sábado, mas todos conhecem o café da manhã dele em
nível molecular.

Pois é: um LLM gerando texto **sem** KV cache é exatamente esse amigo. Cada palavra nova exige
reprocessar a história inteira desde o "sábado". O KV cache é a intervenção civilizatória que
diz: *anota aí onde você parou*.

## Resumo

KV caching é guardar, durante a geração autorregressiva, os vetores de **chave (K)** e
**valor (V)** já calculados para os tokens anteriores, em vez de recalculá-los a cada novo
token. Como esses vetores não mudam depois de calculados, o cache troca **computação
repetida** por **memória ocupada** — e é essa troca que torna a geração viável em produção.

## O que aprendi

- **O gargalo da inferência incremental não é o mesmo do treino.** [confirmado] [F-016] abre
  exatamente por aí: treinar é rápido porque paraleliza ao longo da sequência, mas *"incremental
  inference (where such paralleization is impossible) is often slow, due to the memory-bandwidth
  cost of repeatedly loading the large 'keys' and 'values' tensors"*. Ou seja: depois de
  cachear, o custo dominante deixa de ser conta e passa a ser **tráfego de memória**.
- **O cache é caro em memória, e o número é maior do que a intuição.** [confirmado] Em um
  OPT de 13B, segundo [F-018], *"the KV cache of a single token demands 800 KB of space,
  calculated as 2 (key and value vectors) × 5120 (hidden state size) × 40 (number of layers) ×
  2 (bytes per FP16)"*. **Por token.** De um único usuário.
- **Gerenciar mal esse cache desperdiça a maior parte dele.** [confirmado] [F-018] mediu que
  *"only 20.4% - 38.2% of the KV cache memory is used to store the actual token states in the
  existing systems"* — o resto era fragmentação e duplicação. A correção proposta (PagedAttention
  + vLLM) rendeu *"2-4×"* de *throughput* com a mesma latência.
- **Encolher o cache virou linha de pesquisa própria.** [confirmado] MQA compartilha chaves e
  valores entre todas as cabeças de atenção, *"greatly reducing the size of these tensors and
  hence the memory bandwidth requirements of incremental decoding"* [F-016]; GQA generaliza
  isso com um número intermediário de cabeças de chave-valor e alcança *"quality close to
  multi-head attention with comparable speed to MQA"* [F-017].
- **No curso, o tema mora dentro da lição de geração de texto.** [confirmado] [F-019] não tem
  uma lição intitulada "KV caching": ela aparece em *Text Generation*, e a proposta do curso é
  implementar e medir *"KV caching, continuous batching, and model quantization, and benchmark
  their impacts on inference throughput and latency"*.

## Aula

### Primeiro, por que existe repetição para cachear

Um LLM decodifica **um token por vez**, e cada token novo olha para todos os anteriores. Na
atenção [F-015], cada token vira três vetores por camada: **query (Q)**, **key (K)** e
**value (V)**. Grosseiramente: a query é *"o que eu estou procurando"*, a chave é *"por que
alguém procuraria por mim"* e o valor é *"o conteúdo que eu entrego se for escolhido"*.

O detalhe que decide tudo: por causa do mascaramento causal, **K e V de um token dependem só
daquele token e dos anteriores** — nunca dos que virão depois. Logo, o K e o V do token 7 são
*idênticos* quando você está gerando o token 8, o 900 e o 5000.

Sem cache, o passo ingênuo é: para gerar o token seguinte, rode o modelo inteiro sobre a
sequência inteira outra vez. Você recalcula, em todas as camadas, K e V de todos os tokens que
já estavam lá — e joga fora todo esse trabalho no passo seguinte, para refazê-lo.

**A analogia:** é a diferença entre o contador que refaz a soma da coluna inteira a cada nova
nota fiscal e o contador que mantém o subtotal anotado. Mesmo resultado, e um dos dois vai
embora no horário.

E aqui o trocadilho que ajuda a lembrar: o modelo não tem *memória* — ele tem **caderninho**.
O KV cache não é o modelo "lembrando" da conversa em algum sentido cognitivo; é um bloco de
rascunho com estados intermediários, que morre no fim da requisição.

### O que o cache faz, mecanicamente

1. **Prefill (a primeira passada):** o prompt inteiro entra de uma vez. Como todos os tokens já
   existem, dá para processá-los em paralelo — e essa passada **preenche o cache** com o K e o
   V de cada token, em cada camada, em cada cabeça.
2. **Decode (um token por vez):** para cada token novo, o modelo calcula apenas o Q, K e V
   **daquele token**, anexa o K e o V ao cache, e faz a atenção contra tudo que já está lá.

O que se economiza: o *forward pass* completo — atenção e camadas *feed-forward* inclusive —
deixa de ser recalculado para o prefixo inteiro a cada passo e passa a rodar para **um token
só**. `[hipótese, raciocínio meu — não retirado de fonte]` a leitura que eu faço é que o
trabalho total de gerar *n* tokens cai de algo proporcional a *n²* (cada passo reprocessa todo
o prefixo) para algo proporcional a *n* nessa parte, sobrando como custo crescente apenas a
atenção do token atual contra o prefixo cacheado, que continua crescendo a cada passo. Preciso
confirmar essa contabilidade em fonte antes de repetir como fato — virou pergunta aberta.

**Onde a analogia do caderninho trinca** — e trinca de um jeito caro:

- Caderninho de gente é **resumo**; o KV cache é **cópia integral do estado intermediário**.
  Ele não comprime nada. São duas matrizes por camada, por cabeça, **por token**.
- Caderninho para de crescer quando a reunião acaba. O KV cache cresce **a cada token gerado**,
  e a conta é a de [F-018]: 800 KB por token no OPT-13B. Um contexto de 2.000 tokens são cerca
  de **1,6 GB de VRAM** — para um usuário. `[cálculo meu a partir do número de F-018]`
- E o pior: você não sabe de antemão quanto o cache vai crescer, porque não sabe quantos tokens
  o modelo vai gerar. É alocar quarto de hotel sem saber quantas pessoas vão chegar.

### A reviravolta: o cache resolve um gargalo e cria outro

Esta é a parte que eu não tinha entendido antes de ler [F-016], e é a mais bonita da história.
Com o cache, o modelo para de fazer conta repetida — mas passa a **arrastar** esses tensores da
memória para a unidade de cálculo em todo passo de decodificação. O gargalo migra de *fazer
contas* para *buscar dados*, o que [F-016] chama de *memory-bandwidth cost*.

Consequência: as otimizações seguintes atacam o cache, não a conta.

- **Encolher o cache por token** — MQA [F-016] e GQA [F-017], compartilhando chaves e valores
  entre cabeças.
- **Parar de desperdiçar o que foi reservado** — PagedAttention [F-018], que trata o cache como
  memória virtual paginada de sistema operacional, chegando a *"near-zero waste in KV cache
  memory"* e permitindo *"flexible sharing of KV cache within and across requests"*.

Repare no formato do problema, que se repete em toda a engenharia de LLM: **você não elimina o
custo, você escolhe em que moeda pagar.** Aqui, pagou-se compute com memória — e depois foi
preciso um paper inteiro para administrar a nova dívida.

## Exemplos práticos

1. **Por que o primeiro token demora e o resto sai rápido.** Prompt longo = *prefill* pesado:
   o modelo processa milhares de tokens de uma vez para encher o cache. Depois, cada token novo
   sai a um custo quase constante. É por isso que a experiência típica é "pensou… pensou… e aí
   despejou o texto". O raciocínio: são **duas fases com gargalos diferentes**, e otimizá-las é
   trabalho separado — por isso a indústria mede a latência do primeiro token separada da
   latência entre tokens (`[não verificado]` os nomes usuais para essas duas métricas, TTFT e
   TPOT, eu ouço repetidos mas não confirmei em fonte primária).
2. **O contra-caso: quando o cache é o motivo de o servidor não escalar.** Você tem GPU
   sobrando em FLOPs e mesmo assim não consegue atender mais usuários simultâneos — porque cada
   requisição carrega seu próprio cache crescente, e a memória acaba antes da capacidade de
   cálculo. É exatamente o cenário de [F-018]: o KV cache *"grows and shrinks dynamically"* e,
   mal administrado, só 20,4%–38,2% do que foi reservado guardava estado de verdade. O
   raciocínio a levar embora: em *serving*, **o limite de usuários simultâneos costuma ser um
   problema de memória, não de processamento** — e é por isso que um sistema como o vLLM
   consegue 2–4× de *throughput* sem trocar de GPU nem de modelo.

## Exercícios

1. **Complete a frase com bom humor:** "KV caching troca ______ por ______ — e é por isso que
   o próximo problema do time não vai ser a GPU pensar devagar, e sim ______."
2. **Ache o erro cômico:** "Ativei KV caching e agora meu modelo lembra das conversas
   anteriores dos usuários, então posso desligar o banco de dados de histórico."
3. **Transforme a frase:** reescreva "KV cache deixa a inferência mais rápida" acrescentando a
   condição e o preço que a frase esconde.
4. **Verdadeiro ou vergonhoso:** "Como o cache economiza cálculo, dobrar o tamanho do contexto
   é praticamente de graça."

<details>
<summary>Gabarito comentado</summary>

1. Troca **computação repetida** por **memória ocupada** — e o próximo gargalo é **a largura de
   banda de memória**, ou seja, o custo de recarregar os tensores de K e V a cada passo, que é
   literalmente o problema que [F-016] levanta na primeira frase do abstract. Quem entendeu essa
   frase entendeu por que MQA, GQA e PagedAttention existem.
2. Dois erros, um técnico e um grave. Técnico: o cache guarda **estados intermediários da
   passada atual** (K e V por camada e por cabeça), não texto nem "lembrança" — e normalmente
   vive e morre dentro da requisição. Grave: tratar cache de inferência como armazenamento de
   histórico de usuário é confundir buffer de execução com banco de dados — e, num serviço
   multiusuário, reaproveitar cache entre requisições sem critério é assunto de **isolamento
   entre clientes**, não de performance. Vale notar que [F-018] *permite* compartilhamento
   deliberado de cache entre requisições — mas isso é uma decisão de projeto explícita, não um
   efeito colateral que se ganha de brinde.
3. Por exemplo: "KV cache deixa a inferência mais rápida **enquanto o cache couber na memória**,
   ao preço de ~800 KB por token por requisição num modelo de 13B [F-018] — memória que sai do
   orçamento do tamanho do lote." O que continuou igual: a economia de cálculo. O que apareceu:
   quem paga a conta.
4. **Vergonhoso.** O cache cresce linearmente com o número de tokens, então dobrar o contexto
   dobra a memória de cache por requisição — e memória é justamente o recurso que limita quantos
   usuários cabem ao mesmo tempo. Dobrar contexto costuma significar **metade dos usuários
   simultâneos**, o que é o oposto de "de graça".

</details>

## Minha análise

O que mais me marcou é que KV caching é **memoização** — uma das ideias mais velhas e mais
simples da computação — aplicada no ponto certo de um sistema caro. Não há nada de sofisticado
no conceito: é guardar o que não muda. O que é sofisticado é tudo que veio **depois** que o
conceito virou padrão, e essa é a lição transferível: uma otimização bem-sucedida não encerra o
problema, ela **realoca** o gargalo, e o próximo ciclo de pesquisa nasce exatamente onde ela o
depositou. [F-016] mira largura de banda; [F-017] mira o tamanho por cabeça; [F-018] mira o
desperdício de alocação. Três papers, um cache.

`[hipótese]` Isso também me sugere um jeito de ler qualquer técnica de otimização de inferência
que eu encontrar daqui pra frente: perguntar **qual moeda ela paga e qual ela cobra**.
Quantização paga precisão numérica e cobra memória de peso; KV caching paga memória e cobra
computação; MQA/GQA pagam um pouco de qualidade e cobram memória. Se essa lente se sustentar,
ela organiza a pasta inteira de inferência — mas por enquanto é arrumação minha, não achado de
fonte.

Uma observação metodológica sobre a fonte de origem: [F-019] é curso, não paper. Serve
maravilhosamente para **implementar e medir**, e foi o que motivou esta nota, mas o número que
eu cito aqui não pode vir dele — por isso as afirmações quantitativas desta nota estão todas
ancoradas em [F-016], [F-017] e [F-018].

## Resumo cômico

- Sem cache, o modelo é o amigo que recomeça a história do "sábado eu acordei" a cada
  interrupção.
- K e V dos tokens antigos **não mudam** — recalculá-los é trabalho voluntário não remunerado.
- *Prefill* enche o caderninho de uma vez; *decode* escreve uma linha por vez.
- O modelo não lembra: ele **anota**. E a anotação custa 800 KB por token no OPT-13B [F-018].
- Resolveu o gargalo de conta e criou o de memória — daí MQA [F-016], GQA [F-017] e
  PagedAttention [F-018].

> **Takeaway:** KV caching não faz o modelo pensar mais rápido; faz ele parar de pensar de novo
> a mesma coisa. E toda vez que você para de pagar em CPU, alguém te manda o boleto em VRAM.

## Mapa da ignorância

- A contabilidade que fiz na Aula (custo total caindo de ~*n²* para ~*n* na parte recalculada)
  se sustenta? Preciso de fonte que formalize o custo com e sem cache, em vez do meu raciocínio.
- Os nomes usuais das duas métricas de latência (latência do primeiro token e latência entre
  tokens) são mesmo TTFT e TPOT? `[não verificado]` — falta fonte primária. Liga-se à pergunta
  3 do Mapa central ("Latência: o que é e como medir?").
- Existe regime em que **recomputar** o KV é melhor do que guardá-lo (memória muito escassa,
  contexto muito longo)? Sistemas de *serving* fazem esse *trade-off* explicitamente?
- Quantizar o próprio KV cache (ex.: FP8/INT8) é prática corrente? Qual a perda de qualidade, e
  como isso interage com a [quantização de pesos](../07-ferramentas-ecossistema/quantizacao.md)?
- *Prefix caching*: [F-018] menciona *"flexible sharing of KV cache within and across requests"*.
  Quanto isso rende na prática quando muitos usuários compartilham o mesmo *system prompt*? E
  qual o risco de isolamento entre clientes?
- Quanto do ganho de MQA/GQA vem de reduzir **memória ocupada** e quanto de reduzir **banda**?
  [F-016] cita os dois juntos; queria ver os efeitos separados.
- GQA custa qualidade — *"close to"* MHA [F-017] é quanto, em que tarefas?
- Como KV caching interage com *continuous batching*, o tema da lição seguinte de [F-019]?

## Referências relacionadas

- [Palavras-chave: KV cache](../PALAVRAS-CHAVE.md)
- [Quantização](../07-ferramentas-ecossistema/quantizacao.md) — a outra metade da conta de
  memória na inferência: uma encolhe os pesos, esta nota trata do que cresce durante a geração
- [Compressão de modelos](../04-fine-tuning/compressao-de-modelos.md) — mesma família de
  perguntas ("qual moeda essa otimização paga?"), aplicada ao modelo
- [FONTES.md](../FONTES.md) — [F-015], [F-016], [F-017], [F-018], [F-019]
