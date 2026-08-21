# Quantização

> Data: 2026-08-11 · Reescrita em formato de aula: 2026-08-14
> Tópico: [Palavras-chave: Quantização](../PALAVRAS-CHAVE.md)
> Fontes: [B-002] Winston, *Mastering Ollama: Run, Optimize, and Deploy AI Models Locally*

## Chamada da aula

Você já reparou que ninguém diz "vou encontrar você às 14h03min47s e 219 milissegundos"? A
gente fala "duas da tarde" e o mundo continua girando. Parabéns: você acabou de quantizar um
horário. Perdeu precisão, ganhou uma frase que cabe na boca — e, o mais importante, chegou no
mesmo lugar.

É exatamente isso que fazemos com os pesos de um LLM. A diferença é que, no caso do modelo, a
economia não é de fôlego: é de gigabyte.

## Resumo

Técnica que reduz a precisão numérica dos pesos (e às vezes das ativações) de um LLM já treinado, para diminuir o espaço que ele ocupa em memória e tornar a inferência mais rápida.

## O que aprendi

- Reduz o consumo de memória: pesos representados com menos bits ocupam menos espaço (ex.: de 32 bits para 8 ou 4 bits).
- Acelera a inferência: menos dados para mover e processar significa respostas mais rápidas.
- Traz trade-offs:
  - Pequena perda de precisão nas respostas do modelo — segundo a fonte, tipicamente mínima.
  - Flexibilidade reduzida para fine-tuning em alguns casos.
- Apesar dos trade-offs, os ganhos de desempenho (memória e velocidade) costumam compensar, segundo o autor.

## Aula

Quantização atua sobre os pesos de um modelo já treinado, convertendo valores de ponto flutuante de alta precisão (ex.: FP32 ou FP16) para representações com menos bits (ex.: INT8, INT4). Isso reduz diretamente o tamanho do modelo em disco/memória e o volume de dados movimentado durante a inferência — por isso o ganho de velocidade.

**A analogia:** pense na mudança de casa. O modelo em FP32 é aquele amigo que embala cada
taça de cristal individualmente, com plástico-bolha, etiqueta e uma oração. O modelo em INT8 é
você, às 23h, enfiando tudo em três caixas escritas "COZINHA". Chega tudo? Chega. Chega
*intacto*? Quase tudo. E o caminhão, que antes precisava de duas viagens, faz uma só — que é o
ponto: o gargalo nunca foi o cuidado, foi o transporte.

O trade-off de precisão existe porque menos bits significam menos "resolução" para representar cada peso, introduzindo um erro de arredondamento. Na prática, esse erro costuma ser pequeno para quantizações moderadas (ex.: 8 bits), mas pode crescer em quantizações mais agressivas (ex.: 4 bits ou menos), variando por técnica e modelo.

A perda de flexibilidade para fine-tuning acontece porque pesos quantizados têm menos granularidade para ajustes finos de gradiente — por isso técnicas como QLoRA existem especificamente para permitir fine-tuning sobre modelos quantizados sem perder essa capacidade. Traduzindo a metáfora: depois que você amassou tudo dentro da caixa "COZINHA", boa sorte para
fazer um ajuste delicado no jogo de taças sem abrir a caixa inteira.

**Onde a analogia trinca:** na mudança, o objeto continua o mesmo dentro da caixa — só o
empacotamento mudou. Na quantização, o valor do peso **realmente muda**: 0,7182 vira 0,72 e o
0,7182 não existe mais em lugar nenhum. Não é compressão sem perda, é arredondamento assumido.
E o erro não fica isolado num peso: ele atravessa dezenas de camadas, e é aí que 4 bits
começam a cobrar a conta em tarefas de raciocínio longo.

## Exemplos práticos

1. **O laptop que virou servidor.** Um modelo de 7 bilhões de parâmetros em FP16 pede
   aproximadamente 2 bytes por parâmetro só para os pesos; a mesma coisa em 4 bits pede
   aproximadamente meio byte. `[hipótese, aritmética minha — não verificada na fonte]` isso é
   o fator de ~4× que costuma decidir se o modelo cabe ou não numa GPU de consumo. O raciocínio
   é direto: nenhum truque de software faz caber o que não cabe: ou o peso encolhe, ou o modelo
   não roda.
2. **O contra-caso: o resumo que ficou "quase" certo.** Em tarefa tolerante (reescrever um
   e-mail, classificar tom), o arredondamento se dilui — se a palavra escolhida foi a segunda
   melhor, ninguém morre. Em tarefa de raciocínio encadeado, cada passo herda o erro do
   anterior, e "quase certo" no passo 3 vira "confiantemente errado" no passo 9. Mesma
   quantização, veredictos opostos: o que muda é quanto a tarefa perdoa.

## Exercícios

1. **Complete a frase com bom humor:** "Quantizar um modelo é como pedir para o seu amigo
   contar o filme: você perde ______, mas ganha ______."
2. **Ache o erro cômico:** "Quantizei meu modelo de FP32 para INT4 e ele ficou 8× menor,
   8× mais rápido e 8× mais inteligente, porque com menos bits ele se distrai menos."
3. **Transforme a frase:** passe para a negativa, mantendo a verdade técnica — "Quantização é
   sempre uma boa ideia."

<details>
<summary>Gabarito comentado</summary>

1. Você perde **os detalhes** (a resolução de cada peso) e ganha **tempo e espaço** (memória e
   velocidade). A graça pedagógica da analogia é que ninguém pede o filme contado *para ser
   mais fiel*: você já aceitou o trade-off antes de começar. Quantizar é a mesma negociação,
   feita explicitamente.
2. Dois erros. Primeiro: de 32 para 4 bits o fator sobre os pesos é ~8× em memória, mas o ganho
   de **velocidade não é o mesmo número** — depende de a operação estar limitada por memória ou
   por cálculo, e do suporte do hardware àquele formato. Segundo, e é a piada: menos bits nunca
   aumentam a capacidade do modelo. Quantização, na melhor das hipóteses, **preserva**
   qualidade; ela não adiciona nenhuma.
3. "Quantização **não** é sempre uma boa ideia — ela deixa de compensar quando a tarefa é
   sensível a raciocínio longo, quando o nível é agressivo (4 bits ou menos) ou quando ainda se
   pretende fazer *fine-tuning* sem QLoRA." Repare no que continuou igual: os ganhos de memória
   e velocidade seguem existindo. O que mudou foi o *veredicto*, porque veredicto depende da
   tarefa — e essa é a moral da aula inteira.

</details>

## Minha análise

O balanço "pequena perda de precisão vs. grande ganho de desempenho" mencionado no livro é a razão pela qual quantização virou padrão para rodar LLMs em hardware limitado (GPUs de consumo, edge, mobile) — sem ela, muitos modelos grandes simplesmente não caberiam ou seriam inviáveis de rodar localmente. Vale registrar, porém, que "pequena" é relativo: depende do nível de quantização (8 bits costuma ser quase imperceptível; 4 bits ou menos pode degradar tarefas mais exigentes, como raciocínio complexo) e da técnica usada (quantização pós-treinamento simples vs. técnicas mais sofisticadas como GPTQ, AWQ, GGUF). Isso é um ponto a aprofundar quando eu estudar técnicas específicas de quantização.

Dito de outro jeito: "perda mínima" é uma afirmação sobre **a média de um benchmark**, e eu
não uso modelo na média — uso na tarefa específica que me interessa, que pode estar bem na
cauda ruim dessa distribuição.

## Resumo cômico

- Quantizar = arredondar os pesos de propósito, com testemunhas.
- Ganha memória e velocidade; paga em resolução numérica.
- 8 bits: quase ninguém percebe. 4 bits ou menos: comece a desconfiar, principalmente em
  raciocínio longo.
- Quer *fine-tuning* depois de quantizar? O nome do socorro é QLoRA.

> **Takeaway:** quantização é o "duas da tarde" dos LLMs — ninguém precisa dos milissegundos
> para chegar no compromisso, mas se você marcar a decolagem de um foguete assim, prepare o
> seguro.

## Mapa da ignorância

- Quais são as principais técnicas de quantização (GPTQ, AWQ, GGUF, bitsandbytes) e como elas diferem?
- Em que ponto a perda de precisão deixa de ser "mínima" e passa a afetar a qualidade das respostas de forma perceptível?
- Como o QLoRA contorna a perda de flexibilidade para fine-tuning?
- A conta de memória do exemplo 1 (bytes por parâmetro × nº de parâmetros) bate com o consumo
  real medido, ou o *overhead* de ativações e cache de atenção domina na prática?

## Referências relacionadas

- [Palavras-chave: Quantização](../PALAVRAS-CHAVE.md)
- [Compressão de modelos](../04-fine-tuning/compressao-de-modelos.md) — a outra frente de
  redução de custo, atuando na quantidade de parâmetros em vez da precisão de cada um
- [KV caching](../01-fundamentos/kv-caching.md) — a outra metade da conta de memória na
  inferência: quantização encolhe os pesos, o KV cache é o que **cresce** durante a geração
