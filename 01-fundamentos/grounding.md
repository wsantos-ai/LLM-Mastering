# Grounding

> Data: 2026-08-12 · Reescrita em formato de aula: 2026-08-14
> Tópico: [01-fundamentos](../) · [Palavras-chave: Grounding](../PALAVRAS-CHAVE.md)
> Fontes: [B-001] Pai, *Designing Large Language Model Applications* · [F-002] Harnad (1990) · [F-003] Bender & Koller (2020) · [F-004] Mollo & Millière (2023)

## Chamada da aula

Você já tentou aprender uma palavra nova consultando o dicionário e caindo naquele ciclo
maravilhoso? "Perscrutar: examinar minuciosamente." Ótimo. "Minuciosamente: de modo minucioso."
Excelente. "Minucioso: relativo a minúcia." Obrigado, volto sempre.

Agora imagine passar a **vida inteira** dentro desse ciclo, sem nunca ter visto nada do lado de
fora do dicionário. Bem-vindo ao problema de *grounding* — e a hipótese incômoda de que talvez
seja exatamente essa a situação do modelo que você usou hoje de manhã.

## Resumo

*Grounding* (aterramento) é o problema de conectar a **forma linguística** — os símbolos que o
modelo manipula — àquilo a que ela se refere **fora do texto**: objetos, estados do mundo,
ações, contexto do usuário. Importa porque um modelo treinado só em forma não tem, por
construção, acesso ao referente; toda a discussão sobre "o LLM entende ou só prevê tokens?"
passa por aqui.

## O que aprendi

- **A distinção central é forma × significado.** [confirmado] Bender & Koller definem *form*
  como "any observable realization of language" e *meaning* como "the relation between a
  linguistic form and communicative intent", e argumentam que um sistema treinado apenas em
  forma "a priori has no way to learn meaning" [F-003]. Não é uma afirmação sobre desempenho,
  é sobre o que os dados de treino tornam aprendível em princípio.
- **O problema é anterior aos LLMs.** [confirmado] Harnad formulou o *symbol grounding
  problem* em 1990 [F-002]: como os símbolos de um sistema formal adquirem significado sem
  depender de outro intérprete humano na ponta? A analogia dele é aprender chinês tendo
  acesso só a um dicionário chinês-chinês — os símbolos remetem uns aos outros, nunca ao
  mundo. (Sim: a piada da chamada desta aula tem 1990 e é do Harnad. Eu só troquei o chinês
  por "perscrutar".)
- **"Grounding" não é um conceito único.** [confirmado] Mollo & Millière separam o
  *grounding referencial* — "the connection between a representation and its worldly
  referent" — de outros sentidos do termo, e sustentam que só o referencial resolve o
  problema de Harnad [F-004]. Isso é importante porque na prática de engenharia a palavra é
  usada em pelo menos três acepções diferentes (ver a Aula).
- **A definição formal vem da ciência cognitiva, e é sobre interlocutores.** [confirmado]
  [B-001] (cap. 2) adota a definição de [F-005]: *"The process of establishing what mutual
  information is required for successful communication between two interlocutors"*. Note que
  ela não fala em "mundo" — fala em **informação mútua entre dois falantes**. Ver a análise
  abaixo, porque isso não é detalhe.
- **A questão está formalmente em aberto na literatura.** [confirmado] [B-001] fecha a seção
  assim: *"But do multimodal models really help with the grounding problem? Can we instead
  achieve the effect of grounding by just feeding the model with massive amounts of diverse
  text? These are unsolved questions, and there are good arguments in both directions"*. Ou
  seja: a divergência entre [F-003] e [F-004] não é ruído de duas opiniões — é o estado real
  do conhecimento.
- **O autor tem posição própria, e ela não é neutra.** [confirmado como posição do autor]
  Sobre o argumento de [F-003], [B-001] escreve: *"While a section of the research community
  argues that one cannot learn meaning from form alone, recent language models are
  increasingly proving otherwise"*. É uma afirmação forte e o livro não a sustenta com
  evidência no trecho lido — ver Mapa da ignorância.
- **Há tese de que texto puro pode bastar.** [confirmado como posição, não como consenso]
  Mollo & Millière propõem que LLMs podem alcançar grounding referencial por duas condições
  de origem teleossemântica — estados internos com a relação causal-informacional adequada
  com o mundo, e pressão seletiva que atribuiu a esses estados a função de transmitir
  informação — e que isso **não** exige multimodalidade nem corporeidade [F-004]. É uma
  posição de contraponto direto a [F-003].

## Aula

### Três usos do termo que convém não misturar

Isso não é impressão minha: é a tese central de [F-005], que observa que PLN usa "grounding"
de forma ampla para *qualquer* ligação de texto a dado ou modalidade não textual, enquanto a
ciência cognitiva define o termo de modo mais estrito. Em texto de engenharia de LLM, os
sentidos que aparecem são:

1. **Grounding filosófico/semântico** — o problema de Harnad e Bender & Koller: como a forma
   adquire referência. É a pergunta de fundo. [F-002] [F-003] [F-004]
2. **Grounding perceptual/multimodal** — associar linguagem a imagem, áudio, vídeo, sensores,
   ação em ambiente. É a resposta clássica ao problema: dar ao sistema um canal para o mundo
   além do texto.
3. **Grounding de aplicação ("responder com base em fonte")** — o que se chama de *grounded
   generation* em produto: forçar a resposta a se apoiar em documentos recuperados, resultado
   de ferramenta ou base de dados. É o sentido usado quando se diz que RAG "aterra" o modelo.

Os três compartilham a intuição — ligar a fala a algo que a sustenta —, mas o (3) é um
mecanismo de fidelidade a uma fonte, **não** uma solução do (1). Um sistema pode citar
corretamente um documento e continuar sem qualquer acesso ao referente do documento.

**Guarde essa imagem:** o sentido (3) é o estagiário que responde tudo citando o manual da
empresa. Ele nunca erra a página. Ele também nunca pisou na fábrica. Se o manual estiver
errado, ele erra com nota de rodapé — e uma nota de rodapé é a forma mais elegante de estar
redondamente enganado.

### A experiência mental do "polvo" [F-003]

Bender & Koller propõem o *octopus test*: duas pessoas em ilhas distintas trocam mensagens
por cabo; um polvo hiperinteligente intercepta o canal, aprende perfeitamente as
regularidades estatísticas das mensagens e passa a responder no lugar de uma delas. Enquanto
a conversa for social, ele engana. Quando um lado descreve um objeto novo e pede ajuda para
construí-lo, o polvo falha — nunca teve acesso àquilo de que as palavras falam. É o argumento
de que forma sem referente não vira compreensão, por mais bem modelada que a forma esteja.

Traduzindo para o século XXI: o polvo é ótimo em *small talk* e péssimo em suporte técnico. Ele
passaria fácil numa entrevista comportamental e explodiria no primeiro chamado de "meu
equipamento está fazendo um barulho estranho, o que eu faço?".

**Onde a analogia do polvo trinca** — e é bom saber disso antes de brigar com alguém na
internet: ela é um argumento sobre o que é aprendível **em princípio** a partir de forma, não
uma medição de desempenho de nenhum modelo real. Quem responde "mas o meu modelo resolveu esse
tipo de problema ontem" está respondendo a outra pergunta. E quem usa o polvo para dizer "logo,
LLMs nunca servirão para nada" também extrapolou: [F-004] aceita o enquadramento e chega à
conclusão oposta.

### Por que isso é decisão de projeto, e não só filosofia

O grau de grounding que a aplicação exige muda a arquitetura:

- Tarefa cujo critério de acerto está **dentro do texto** (reescrever, resumir, traduzir,
  classificar tom) tolera pouco grounding.
- Tarefa cujo critério está **fora do texto** (o preço está certo? esse cliente existe? o
  arquivo compila?) precisa de um canal para o mundo: *retrieval*, chamada de ferramenta,
  execução, verificação.
- Alucinação, vista por esta lente, deixa de ser "o modelo mentiu" e vira "não havia canal
  entre a afirmação gerada e aquilo que a tornaria verdadeira ou falsa".

Essa última linha é a mais útil da nota inteira para quem constrói sistema. Alucinação para de
ser um defeito de caráter do modelo e vira uma **lacuna de arquitetura** que você desenhou —
provavelmente sem perceber, provavelmente numa sprint apertada.

## Exemplos práticos

1. **"Compila?" é uma pergunta aterrada; "está bonito?" não é.** Peça ao modelo para
   refatorar uma função e ele te devolve algo plausível. Rode o teste e o mundo responde:
   passa ou não passa. O raciocínio: o executor de testes é literalmente o canal para o mundo
   que o texto sozinho não tem — por isso agente com ferramenta acerta onde chat puro chuta.
   Note que o modelo não ficou mais inteligente; ele ficou mais **conectado**.
2. **O contra-caso, que é o mais importante:** RAG citando corretamente um documento errado.
   O sistema recupera a política de reembolso de 2019, cita a página, formata bonito — e o
   cliente recebe uma regra revogada. Fidelidade à fonte: perfeita. Grounding no sentido (1):
   zero. É a demonstração de que o sentido (3) desloca a pergunta em vez de resolvê-la: agora
   o que precisa estar aterrado é o **corpus**, e ninguém perguntou quem aterra o corpus.

## Exercícios

1. **Complete a frase com bom humor:** "O polvo de Bender & Koller é excelente em ______ e
   desastroso em ______, porque ele tem acesso a ______ e nunca a ______."
2. **Ache o erro cômico:** "Coloquei RAG no meu chatbot, então agora ele está *grounded* e o
   problema do symbol grounding, que atormentava a filosofia desde 1990, está resolvido. De
   nada, Harnad."
3. **Transforme a frase:** reescreva "o modelo alucinou" usando o vocabulário desta aula, de
   modo que a frase aponte para um culpado consertável.
4. **Verdadeiro ou vergonhoso:** "Se um modelo multimodal vê imagens, o problema de grounding
   está resolvido para ele."

<details>
<summary>Gabarito comentado</summary>

1. Excelente em **conversa social** (*small talk*), desastroso em **tarefa que depende de um
   objeto do mundo** (o pedido de ajuda para construir algo novo); tem acesso a **forma** — as
   regularidades estatísticas das mensagens — e nunca ao **referente**. A frase é a estrutura
   inteira do argumento de [F-003] em uma linha.
2. O erro é confundir os sentidos (3) e (1). RAG entrega **fidelidade a uma fonte recuperada**;
   o problema de Harnad é sobre como o símbolo adquire referência, e citar um documento não dá
   ao modelo acesso ao referente **do documento**. Bônus: RAG desloca a pergunta para "o que
   aterra o corpus?", que continua sem resposta. Harnad segue sem agradecer.
3. Por exemplo: "não havia canal entre a afirmação gerada e aquilo que a tornaria verdadeira ou
   falsa." O culpado consertável é a **arquitetura** — falta *retrieval*, chamada de
   ferramenta, execução ou verificação. Repare no que mudou: saiu a acusação moral ("o modelo
   mentiu"), entrou um item de backlog.
4. **Vergonhoso** — mas por um motivo sutil: a resposta correta não é "não resolve", é **"não
   se sabe"**. [B-001] registra explicitamente como questão não resolvida se modelos
   multimodais realmente ajudam, e [F-004] sustenta que multimodalidade nem sequer é
   necessária. Quem responde com certeza para qualquer um dos lados está mais confiante que a
   literatura. É a pergunta 6 do [Mapa da ignorância](../MAPA-DA-IGNORANCIA.md).

</details>

## Minha análise

**Há uma tensão dentro do próprio trecho de [B-001].** O livro diz que "the linguistic form
needs to be grounded **to the real world**" e, na frase seguinte, adota uma definição que não
menciona o mundo: informação mútua necessária para comunicação bem-sucedida **entre dois
interlocutores** [F-005]. São coisas diferentes. Pela definição de Chandu et al., grounding é
uma propriedade da *situação comunicativa* — o que os dois lados precisam compartilhar para
se entenderem —, e não da relação entre símbolo e objeto, que é o problema de Harnad [F-002]
e o *grounding referencial* de [F-004]. `[hipótese]` Suspeito que essa oscilação seja
justamente o que [F-005] denuncia: o termo cognitivo é emprestado para dar peso formal a um
uso de engenharia mais frouxo. Preciso ler [F-005] direto para confirmar se estou lendo bem.

A consequência prática não é pequena. Se grounding é informação mútua entre interlocutores,
então **contexto do usuário conta como grounding** — histórico da conversa, quem é o usuário,
o que ele já sabe. Isso é um alvo de engenharia bem mais tangível do que "conectar símbolos
ao mundo", e mudaria minha lista de mecanismos no item (3) acima.

Sobre a afirmação de que modelos recentes estão "provando o contrário" de [F-003]: `[hipótese]`
ela me parece deslocar o argumento. Bender & Koller fazem uma afirmação sobre o que é
aprendível *em princípio* a partir de forma; desempenho crescente em *benchmarks* não
responde isso, porque a discordância é sobre o que conta como evidência de significado, não
sobre quão bem o modelo se sai. É exatamente por isso que abri a pergunta sobre qual
experimento decidiria a disputa.

O que me parece mais útil na leitura conjunta é que [F-003] e [F-004] discordam sobre a
**conclusão** mas concordam sobre o **enquadramento**: a pergunta certa não é "o modelo
entende?", e sim "que relação existe entre os estados internos do modelo e as coisas de que
ele fala, e o que estabeleceu essa relação?". Formulada assim, a pergunta admite resposta
técnica — dá para perguntar o que muda quando se adiciona visão, ferramentas, RLHF ou
execução — em vez de virar disputa de definição de "entender".

Também vale registrar um viés meu a vigiar: é tentador tratar RAG como se resolvesse o
problema de grounding, porque a palavra é a mesma. Pelo que li, não resolve — desloca a
questão do modelo para o corpus, e a pergunta "o que aterra o corpus?" continua aberta.

## Resumo cômico

- Grounding = ligar a palavra àquilo de que ela fala. O dicionário chinês-chinês de Harnad é o
  pesadelo de origem, e ele é de 1990 [F-002].
- A palavra tem três empregos e nenhum deles avisa qual está usando: filosófico, multimodal e
  "responder com base em fonte".
- O polvo [F-003] arrasa no bate-papo e afunda no chamado técnico.
- RAG não resolve o problema — muda o endereço dele para o corpus.
- Multimodalidade resolve? A literatura, com todas as letras, diz **não se sabe** [B-001].

> **Takeaway:** alucinação não é o modelo mentindo, é você tendo esquecido de instalar o fio
> terra. E fio terra, na sua arquitetura, tem nome: *retrieval*, ferramenta, execução,
> verificação.

## Mapa da ignorância

- Ler [F-005] na íntegra: a definição citada em [B-001] é sobre interlocutores, não sobre
  mundo. Confirmar se a tensão que apontei em "Minha análise" se sustenta no texto original.
- Que evidência [B-001] apresenta para "recent language models are increasingly proving
  otherwise"? O trecho lido afirma sem sustentar. Verificar o restante do cap. 2.
- [B-001] remete a um "debate" ao dizer que há bons argumentos dos dois lados. Identificar a
  que debate o link aponta e registrá-lo em `FONTES.md`.
- Modelos multimodais (visão + texto) resolvem, atenuam ou apenas deslocam o problema de
  Harnad? Há resultado experimental que separe as duas hipóteses? *(Pergunta reconhecida como
  aberta pela própria literatura — [B-001], cap. 2.)*
- Se a questão está em aberto, **o que decidiria a disputa empiricamente?** Que experimento
  distinguiria "texto massivo bastou" de "faltou o canal perceptual"? Enquanto não houver
  critério de decisão, o debate corre risco de ser sobre definição, não sobre fato.
- Existe métrica ou benchmark que meça grounding referencial, e não só fidelidade à fonte
  recuperada (*faithfulness*)?
- Qual o papel do RLHF/feedback humano como "canal para o mundo"? Ele fornece o tipo de
  pressão seletiva que [F-004] exige?
- *Grounded generation* em RAG: quais mecanismos concretos (citação obrigatória, verificação
  pós-hoc, decodificação restrita) e qual o custo de cada um?

## Referências relacionadas

- [Palavras-chave: Grounding](../PALAVRAS-CHAVE.md)
- [03-rag](../03-rag/) — grounding no sentido (3), a implementar como nota própria
- [BIBLIOGRAFIA.md](../BIBLIOGRAFIA.md) — [B-001]
- [FONTES.md](../FONTES.md) — [F-002], [F-003], [F-004]
