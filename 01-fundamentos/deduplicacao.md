# Deduplicação (*deduplication*)

> Data: 2026-08-13 · Reescrita em formato de aula: 2026-08-14
> Tópico: [01-fundamentos](../) · [Palavras-chave: Deduplicação](../PALAVRAS-CHAVE.md)
> Fontes: [F-006] Lee et al. (2022) · [F-007] Kandpal et al. (2022) · [F-008] Muennighoff et al. (2023) · [F-009] Abbas et al. (2023)

## Chamada da aula

Imagine que você estuda para um concurso com uma apostila de dez mil páginas. Só que, por um
acidente da gráfica, a frase *"o presente contrato é regido pelas leis da República"* aparece
sessenta mil vezes ao longo do material. No dia da prova, você não sabe responder nada — mas
sabe recitar aquela frase de trás para frente, com entonação.

Parabéns: você acabou de simular um LLM treinado em corpus não deduplicado. E o pior é que não
é exagero didático — o número saiu de um paper de verdade, e ele está três parágrafos abaixo.

## Resumo

*Deduplication* é a remoção de trechos repetidos do conjunto de dados de treino — desde
documentos idênticos até sequências longas que reaparecem em milhares de páginas diferentes.
Importa porque corpus raspado da web é muito mais repetitivo do que parece, e a repetição
degrada o modelo em quatro frentes ao mesmo tempo: memorização, privacidade, custo de treino
e validade da avaliação.

## O que aprendi

- **A escala do problema é maior que a intuição.** [confirmado] Em C4, [F-006] encontrou *"a
  single 61 word English sentence that is repeated over 60,000 times"*. Não é caso isolado:
  o mesmo trabalho relata que *"over 1% of the unprompted output of language models trained
  on these datasets is copied verbatim from the training data"*.
- **Deduplicar reduz memorização em ~10×.** [confirmado] Modelos treinados em dados
  deduplicados emitem texto memorizado *"ten times less frequently"*, e atingem acurácia
  igual ou melhor **com menos passos de treino** [F-006]. É o raro caso em que a mesma
  mudança melhora qualidade e reduz custo.
- **A relação entre repetição e memorização é superlinear.** [confirmado] Uma sequência que
  aparece 10 vezes no treino é gerada cerca de **1000 vezes mais** que uma sequência que
  aparece uma única vez [F-007]. Ou seja: duplicação não é um problema que cresce devagar —
  poucas repetições já mudam o regime.
- **É questão de privacidade, não só de qualidade.** [confirmado] [F-007] mostra que o
  sucesso de ataques de extração de dados de treino se deve em grande parte à duplicação, e
  que métodos existentes de detectar memorização têm acurácia *"near-chance"* em sequências
  **não** duplicadas. Deduplicar é uma das defesas mais baratas contra vazamento.
- **Contaminação de avaliação.** [confirmado] Deduplicar reduz sobreposição treino–teste que
  afeta *"over 4% of the validation set of standard datasets"* [F-006]. Sem isso, parte do
  *benchmark* mede memorização, não capacidade.
- **Repetir não é sempre ruim — há um regime seguro.** [confirmado] Em cenário de dados
  escassos, treinar com **até 4 épocas** de dados repetidos produz *"negligible changes to
  loss compared to having unique data"*; além disso, o retorno cai rápido até o valor de
  adicionar computação decair a zero [F-008]. Isso é o contraponto importante: o inimigo é a
  duplicação **descontrolada e desconhecida** dentro do corpus, não a repetição deliberada e
  medida.

## Aula

### Dois níveis de "duplicado"

1. **Duplicação exata / quase exata** — o mesmo documento ou a mesma sequência longa de
   tokens aparecendo várias vezes. [F-006] ataca isso com duas famílias de método:
   - **Documento inteiro, aproximado**: assinatura tipo *MinHash* + LSH para achar pares com
     alta similaridade sem comparar todos contra todos. *É o porteiro que reconhece o morador
     pelo andar e pelo casaco, sem conferir o RG de cada um dos oitocentos.*
   - **Substring exata**: *suffix array* para encontrar toda sequência longa repetida entre
     documentos, mesmo que os documentos como um todo sejam diferentes. Esse é o caso comum
     na web — *boilerplate*, avisos legais, textos de licença, cabeçalhos. *É o "aceito os
     termos de uso" que você assinou 4 mil vezes na vida e nunca leu uma.*
2. **Duplicação semântica** — textos diferentes que dizem essencialmente a mesma coisa.
   Escapam de qualquer casamento de string. [F-009] (SemDeDup) usa *embeddings* de um modelo
   pré-treinado para agrupar pares semanticamente próximos: em LAION, remover **50%** dos
   dados custou perda mínima de desempenho e cortou o tempo de treino pela metade. *É o grupo
   da família mandando a mesma corrente reescrita por sete pessoas diferentes: nenhum caractere
   bate, e é tudo a mesma mensagem.*

A progressão importa: quanto mais "semântica" a deduplicação, maior o ganho de eficiência e
maior o risco de jogar fora variação legítima.

### Por que a web é tão duplicada

Não é acidente de coleta. É *boilerplate* de template, republicação de notícia por agência,
espelhos e *scrapers*, termos de uso, texto gerado por template, e — cada vez mais — texto de
LLM republicado. O corpus herda a estrutura econômica da web.

**A analogia central:** o modelo não decora por burrice, decora por *insistência alheia*. Se
alguém te repete a mesma frase sessenta mil vezes, você também aprende — e a graça amarga é
que [F-007] mostra que bastam **dez** repetições para o efeito explodir mil vezes. Não é uma
subida suave: é interruptor.

**Onde a analogia trinca:** o aluno humano percebe que está sendo repetido e se irrita; o
modelo não tem esse mecanismo. Ele não distingue "isso apareceu muito porque é importante" de
"isso apareceu muito porque um scraper duplicou o site". Toda a diferença entre sabedoria e
papagaio, aqui, foi decidida *antes* do treino, por quem montou o corpus.

### Onde a deduplicação aparece no ciclo de vida

- **Pré-treino**: o caso clássico acima.
- ***Fine-tuning***: um exemplo repetido em um conjunto pequeno pesa desproporcionalmente e
  vira candidato a *overfitting*.
- **RAG / base de conhecimento**: `[hipótese]` chunks quase idênticos ocupam várias posições
  do top-k da recuperação, gastando janela de contexto sem adicionar informação. Não confirmei
  em fonte — virou pergunta aberta.
- **Avaliação**: separar o conjunto de teste do de treino é, na prática, um problema de
  deduplicação.

## Exemplos práticos

1. **O benchmark que mede a própria cola.** [F-006] mostra sobreposição treino–teste afetando
   *"over 4% of the validation set of standard datasets"*. Traduzindo: em 4% da prova, o modelo
   já tinha visto o gabarito. O raciocínio é o que interessa — a nota sobe, a capacidade não, e
   você só descobre a diferença quando coloca o sistema na frente de um caso novo, em produção,
   numa sexta-feira à noite.
2. **O contra-caso que salva a técnica:** língua com pouco dado disponível. Segundo [F-008],
   em cenário de dados escassos, até 4 épocas de repetição têm efeito desprezível na *loss*.
   Ou seja: passar o mesmo corpus quatro vezes, de propósito e por igual, é decisão de
   engenharia; ter uma frase repetida 60 mil vezes enquanto o resto aparece uma vez é acidente.
   Mesma palavra ("repetição"), dois fenômenos — e é justamente essa distinção que virou a
   pergunta 12 do [Mapa da ignorância](../MAPA-DA-IGNORANCIA.md).

## Exercícios

1. **Complete a frase com bom humor:** "Treinar sem deduplicar é como estudar por uma apostila
   em que a página do ______ foi fotocopiada sessenta mil vezes: você gabarita ______ e
   reprova em ______."
2. **Ache o erro cômico:** "Deduplicação é aquela etapa chata de limpeza de dados, tipo tirar
   espaço em branco — não muda nada no comportamento do modelo, só deixa o arquivo menor."
3. **Transforme a frase:** pegue "repetir dados de treino é ruim" e reescreva como uma frase
   verdadeira, com a condição que faltava.
4. **Verdadeiro ou vergonhoso:** "Se dois textos não compartilham nenhum caractere idêntico,
   eles não são duplicatas."

<details>
<summary>Gabarito comentado</summary>

1. Sugestão: "...a página do **contrato** foi fotocopiada sessenta mil vezes: você gabarita
   **a cláusula de foro** e reprova em **todo o resto**." O que a piada fixa: a memorização não
   é distribuída, é concentrada exatamente onde houve repetição — e é por isso que ela aparece
   como *emissão verbatim* na saída, e não como uma melhora geral.
2. Errado em três frentes ao mesmo tempo, e é o ponto central da aula: deduplicar muda
   **memorização** (~10× menos, [F-006]), muda **risco de privacidade** (ataques de extração
   dependem em boa parte da duplicação, [F-007]) e muda **a validade do benchmark**
   (sobreposição treino–teste, [F-006]). Menor no disco é o efeito menos interessante.
3. Por exemplo: "repetir dados de treino **de forma desigual e desconhecida** é ruim — repetir
   de forma deliberada e medida, até cerca de 4 épocas em cenário de dados escassos, tem efeito
   desprezível na *loss* [F-008]." O que mudou foi a condição; o que continuou igual é que
   ninguém defende sequência repetida 60 mil vezes.
4. **Vergonhoso.** É exatamente a duplicação **semântica**, que escapa de qualquer casamento de
   string e por isso exige *embeddings* — [F-009] removeu 50% do LAION assim, com perda mínima
   e metade do tempo de treino. Casamento de caractere é o piso da deduplicação, não o teto.

</details>

## Minha análise

O que me chama atenção é que deduplicação é normalmente apresentada como "limpeza de dados",
uma etapa chata de engenharia, quando [F-006] e [F-007] mostram que ela é uma **alavanca
sobre o comportamento do modelo**: muda quanto ele memoriza, quanto vaza e quanto se pode
confiar no *benchmark*. Isso a coloca mais perto de segurança e avaliação do que de ETL.

A leitura conjunta de [F-006] e [F-008] resolve uma aparente contradição que eu tinha ao ler
os dois: um diz "deduplique", o outro diz "repetir até 4 épocas não dói". `[hipótese]` A
diferença é *controle*, não repetição em si — repetir o corpus inteiro de forma uniforme e
deliberada é diferente de ter um punhado de sequências repetidas 60 mil vezes enquanto o
resto aparece uma vez. O dano parece vir da **distribuição desigual** de repetição, que é
exatamente o que [F-007] mede como superlinear. Preciso confirmar se os autores enquadram
assim ou se estou construindo a ponte sozinho.

## Resumo cômico

- Corpus da web é mais repetitivo do que playlist de festa junina.
- Deduplicar: ~10× menos memorização, mesma ou melhor acurácia, com menos passos [F-006].
- Dez repetições já geram ~1000× mais emissão [F-007]. Não é rampa, é degrau.
- Duplicata semântica não tem caractere em comum — só *embedding* pega [F-009].
- Repetir de propósito e por igual, até ~4 épocas em dados escassos: liberado [F-008].

> **Takeaway:** o modelo não decora o que é importante, decora o que insistiram com ele.
> Deduplicar é decidir quem tem direito de insistir.

## Mapa da ignorância

- A hipótese acima se sustenta? [F-008] discute explicitamente a interação entre repetição
  uniforme e duplicação desigual, ou são literaturas que não conversam?
- Qual o limiar prático: a partir de quantas repetições uma sequência passa a ser
  problemática? [F-007] dá a forma da curva (superlinear), não um ponto de corte operacional.
- Deduplicação em RAG: chunks quase duplicados de fato degradam a recuperação? Existe medida
  de diversidade no top-k (ex.: MMR) que resolva isso melhor que deduplicar o índice?
- Como [B-001] trata preparação de dados e deduplicação? O livro cobre o tema — verificar e
  ligar a esta nota.
- Deduplicar demais tem custo? Textos legitimamente repetidos (citações canônicas, código
  padrão, fórmulas) são justamente os que se quer que o modelo saiba de cor.
- Como se mede memorização na prática? [F-007] diz que os métodos existentes têm acurácia
  quase aleatória em sequências não duplicadas — então o que se usa hoje?

## Referências relacionadas

- [Palavras-chave: Deduplicação](../PALAVRAS-CHAVE.md)
- [06-avaliacao-seguranca](../06-avaliacao-seguranca/) — contaminação de *benchmark* e
  extração de dados de treino
- [03-rag](../03-rag/) — deduplicação de *chunks*
- [Compressão de modelos](../04-fine-tuning/compressao-de-modelos.md) — outra frente de
  redução de custo, mas atuando no modelo, não nos dados
- [Bias e fairness](bias-e-fairness.md) — a mesma lição por outro ângulo: toda decisão de
  curadoria é uma decisão sobre o que o modelo vai virar
