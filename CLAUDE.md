# llm-mastering — diário de estudos sobre LLMs

Este repositório é um **diário de estudos**, não um projeto de software. O produto final é
entendimento registrado de forma verificável: cada afirmação sobre LLMs deve ser rastreável
até uma fonte, e cada lacuna de conhecimento deve estar explícita em vez de escondida.

Idioma de todo o conteúdo: **português do Brasil**. Termos técnicos consagrados em inglês
(*transformer*, *attention*, *fine-tuning*, *embedding*) ficam em inglês, em itálico na
primeira ocorrência de cada arquivo.

---

## Princípios que regem todo o conteúdo

1. **Nenhuma afirmação factual sem fonte.** Data, número, nome de modelo, benchmark ou
   citação exige link em `FONTES.md`. Se não há fonte, a afirmação vira uma pergunta aberta
   no Mapa da ignorância — não um texto afirmativo.
2. **Preferir fonte primária.** Paper no arXiv, post oficial do laboratório, model card,
   documentação da API. Blog de terceiro só quando explica melhor algo já confirmado na
   primária, e sempre marcado como secundário.
3. **Separar o que sei do que acho.** Use marcadores explícitos no texto: `[confirmado]`,
   `[hipótese]`, `[não verificado]`. Um parágrafo sem marcador é lido como confirmado, então
   marque as hipóteses.
4. **Datas absolutas, sempre.** Nunca "recentemente", "ano passado", "há pouco tempo".
   Escreva `2025-08-07`. Isso vale inclusive dentro de notas de estudo.
5. **Registrar o erro, não apagar.** Quando uma anotação se mostra errada, corrija o texto e
   deixe uma linha de errata com a data e o motivo. O histórico do erro é parte do estudo.
6. **Nota curta e frequente vence nota longa e adiada.** Uma nota que cabe em uma tela, com
   uma ideia central, é o formato-alvo.

---

## Estrutura e o papel de cada seção

### `FONTES.md` — bibliografia canônica
Índice único de tudo que foi lido. Nenhuma outra parte do diário linka para fora sem passar
por aqui.

- Uma entrada por fonte, com **ID estável** (`[F-012]`) usado como referência nas notas.
- Campos obrigatórios: título, autores/organização, data de publicação, URL, tipo
  (paper / doc oficial / post / vídeo / livro), estado de leitura (`lida` / `parcial` /
  `na fila`) e uma linha de "o que essa fonte resolve".
- Agrupar por tema (arquitetura, treinamento, inferência, avaliação, produto/API,
  segurança/alinhamento), não por ordem de descoberta.
- Ao adicionar fonte: verifique se ela já existe antes de duplicar; se for versão nova de
  algo já listado, atualize a entrada existente e anote a mudança.
- Fonte lida gera obrigatoriamente pelo menos uma nota ou uma pergunta. Fonte lida sem
  desdobramento é sinal de leitura passiva.

### `BIBLIOGRAFIA.md` — livros que explicam a disciplina
Bibliografia **de livros** (e obras de referência longas), separada de `FONTES.md`. Enquanto
`FONTES.md` registra o que foi lido no dia a dia — papers, docs, posts —, aqui se constrói o
acervo que dá **profundidade histórica**: as obras que explicam como o campo chegou ao estado
atual, incluindo as anteriores à era dos LLMs (fundamentos de estatística, redes neurais,
linguística computacional, teoria da informação, história da IA).

- Uma entrada por obra, com ID estável (`[B-007]`), citável nas notas como qualquer fonte.
- Campos obrigatórios: **título**, **autor(es)**, **data de publicação** (com a edição a que
  a data se refere), **editora** e **breve descrição** — 2 a 4 frases sobre o que a obra
  cobre e por que importa para entender LLMs.
- Campos complementares: edição/ISBN quando houver ambiguidade, idioma, e origem da entrada:
  `citado em [F-xx]` (apareceu no estudo) ou `sugerido` (veio de expansão da bibliografia).
- Estado de leitura: `lido` / `parcial` / `na fila` / `catalogado` (registrado, ainda sem
  intenção de leitura). Catalogar não é ler — não gere notas a partir de obra não lida.
- Organizar em duas dimensões: por **eixo temático** (fundamentos matemáticos e estatísticos,
  redes neurais e *deep learning*, PLN pré-*transformer*, era dos LLMs, aplicação e
  engenharia, impacto social e ética) e, dentro de cada eixo, por ordem cronológica de
  publicação. A ordem cronológica é o que revela a linhagem intelectual.
- Livro que marcou virada de paradigma também rende linha em `LINHA-DO-TEMPO.md`.

**Expansão da bibliografia — comportamento esperado do assistente.** A cada obra adicionada,
sugira de 2 a 4 outras obras para ampliar o acervo, sem se limitar ao que apareceu nos
estudos. Cada sugestão deve vir com título, autor, ano, editora, uma frase de justificativa e
o vínculo com a obra que a motivou. Priorize, nesta ordem:

1. **Antecedente** — a obra que a nova entrada pressupõe ou de que deriva.
2. **Contraponto** — obra que defende abordagem ou tese concorrente (ex.: conexionismo vs.
   IA simbólica), para evitar bibliografia de uma escola só.
3. **Aprofundamento** — tratamento mais técnico do mesmo tema.
4. **Ponte** — obra que liga o tema a outro eixo (matemática, linguística, sociedade).

Sugestões vão para uma seção **"Candidatas"** no fim do arquivo; só sobem para o acervo
principal quando eu aprovar. **Confirme que a obra existe antes de sugerir**: título, autor,
editora e ano precisam ser verificados, não presumidos. Livro plausível mas não confirmado
não entra — nem como candidata. Na dúvida entre edições, registre a que você conseguiu
confirmar e marque `[edição a confirmar]`.

### `LINHA-DO-TEMPO.md` — marcos históricos
Cronologia dos marcos técnicos e de produto que explicam como chegamos ao estado atual.

- Ordem cronológica crescente; formato de linha: `AAAA-MM-DD — Marco — por que importa — [F-xx]`.
- Só entra o que teve **consequência técnica ou de mercado rastreável**. Lançamento de modelo
  sem novidade arquitetural relevante não é marco; é nota de contexto.
- Quando só se conhece o ano ou o mês, escreva `2017-06` e marque `[data parcial]`. Não
  invente dia.
- Toda linha precisa de fonte. Correções de data (já aconteceu com o GPT-5) devem citar a
  fonte oficial que motivou a correção.

### Mapa da ignorância — o que ainda não sei
O documento mais importante do diário. É o que dirige o estudo.

- Duas listas: **perguntas abertas** e **perguntas respondidas**.
- Pergunta aberta tem: enunciado específico, por que ela importa, e o que já se tentou.
  "Como funciona atenção?" é vago demais; "Por que atenção multi-cabeça supera uma cabeça
  única com a mesma dimensão total?" é uma pergunta de trabalho.
- Ao responder: **mova** a pergunta para respondidas com a data, a resposta em 1–3 frases e
  o `[F-xx]` que a fechou. Não apague nem reescreva no lugar.
- Responder uma pergunta quase sempre gera novas perguntas abertas — registre-as na mesma
  edição. Um mapa que só encolhe indica que o estudo parou de aprofundar.
- Marque nível: `fundamento` (bloqueia o resto), `aplicação`, `curiosidade`.

### Notas de estudo + template
Uma nota por ideia, criada a partir do template existente — não improvise um formato novo.

- Nome do arquivo em kebab-case descritivo do conceito, não da fonte:
  `atencao-multi-cabeca.md`, não `notas-paper-vaswani.md`.
- Estrutura da nota: o conceito em uma frase, explicação com as próprias palavras, exemplo
  concreto ou trecho de código mínimo, limites/quando não se aplica, fontes `[F-xx]`,
  perguntas que a nota abriu.
- **Explicação com as próprias palavras é obrigatória.** Copiar o abstract não conta como
  nota; se você não consegue reescrever, a nota vira pergunta aberta.
- Ligue notas entre si por links relativos. Conceito citado em duas notas sem nota própria é
  candidato a virar nota.
- Se alterar o template, aplique a mudança de forma incremental; não reformate notas antigas
  em massa junto com a mudança.

---

## Como trabalhar neste repositório

**Ao adicionar conhecimento novo, o fluxo é:** fonte em `FONTES.md` → nota → atualizar Mapa
da ignorância (fechar e/ou abrir perguntas) → linha do tempo, se houver marco datado.

**Quando um livro for mencionado em qualquer leitura, nota ou conversa:** catalogue-o em
`BIBLIOGRAFIA.md` (mesmo que eu não vá lê-lo agora) e, na sequência, ofereça as obras
candidatas para expandir o acervo. Livro citado e não catalogado é bibliografia perdida.

**Ao me pedir para pesquisar algo:** traga a fonte primária e a citação exata que sustenta a
afirmação. Se a busca não confirmar, o resultado correto é registrar a pergunta como aberta
com `[não verificado]` — não preencher com plausibilidade. Nunca invente URL, DOI, número de
parâmetros, data ou resultado de benchmark.

**Ao me pedir explicação de um conceito:** verifique antes se já existe nota sobre ele.
Existindo, estenda a nota em vez de criar outra; havendo divergência entre a nota e o que
você sabe, aponte a divergência explicitamente em vez de sobrescrever silenciosamente.

**Sobre modelos e APIs:** informação sobre modelos evolui rápido e conhecimento de memória
envelhece. Para preço, limites, nomes de modelo e parâmetros de API, consulte documentação
atual antes de escrever, e registre a data da consulta ao lado do dado.

**Commits:** um assunto por commit, mensagem no imperativo descrevendo o que mudou no
conhecimento (`Responde pergunta sobre RoPE e abre duas sobre contexto longo`), não o
arquivo mexido. Sem emoji.

**Escopo:** este repositório contém texto e, no máximo, código de exemplo curto para ilustrar
conceito. Não adicione build, dependências, CI ou estrutura de aplicação sem pedido explícito.
