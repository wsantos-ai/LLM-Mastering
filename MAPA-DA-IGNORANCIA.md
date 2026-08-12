# Mapa da ignorância

Lista central de tudo que eu sei que ainda não sei — perguntas em aberto e pontos a aprofundar, vindos de qualquer nota ou leitura. Quando uma pergunta for respondida em uma nota de tópico, ela é marcada como concluída aqui e o link passa a apontar para a resposta.

Cada nota individual também pode ter sua própria seção "Mapa da ignorância" local (ver [`_template.md`](_template.md)); os itens relevantes migram para cá quando viram uma lacuna de estudo mais ampla, não só um detalhe pontual da nota.

## Em aberto

1. [ ] O que é truncamento de contexto?
2. [ ] Quantização: o que é, técnicas, prós e contras — *nota inicial já existe em [07-ferramentas-ecossistema/quantizacao.md](07-ferramentas-ecossistema/quantizacao.md), mas falta detalhar técnicas específicas (GPTQ, AWQ, GGUF, bitsandbytes).*
3. [ ] Latência: o que é e como medir?
4. [ ] Layers: o que são e o que fazem?
5. [ ] Arquitetura Transformer
6. [ ] `fundamento` — Modelos multimodais (visão + texto) resolvem, atenuam ou apenas deslocam o problema de *symbol grounding*? *Importa porque define se "dar olhos ao modelo" é solução de fundo ou paliativo — e isso muda o que esperar de VLMs. **Esta pergunta está reconhecidamente em aberto na própria literatura** ([B-001], cap. 2): não se sabe se texto massivo e diverso basta ou se multimodalidade é obrigatória. Já se tentou: leitura de [F-002], [F-003] e [F-004]; [F-004] afirma que multimodalidade não é necessária, mas não é consenso. Ver [01-fundamentos/grounding.md](01-fundamentos/grounding.md).*
7. [ ] `aplicação` — Existe métrica ou benchmark que meça *grounding referencial*, e não apenas fidelidade à fonte recuperada (*faithfulness*)? *Importa porque, sem distinguir os dois, "sistema aterrado" vira sinônimo de "sistema que cita bem". Nada tentado ainda.*
8. [ ] `aplicação` — Quais mecanismos concretos implementam *grounded generation* em RAG (citação obrigatória, verificação pós-hoc, decodificação restrita) e qual o custo de cada um? *Importa para escolher arquitetura em [03-rag](03-rag/). Nada tentado ainda.*
9. [ ] `curiosidade` — O RLHF/feedback humano funciona como "canal para o mundo"? *Importa porque [F-004] exige pressão seletiva que atribua aos estados internos a função de transmitir informação, e RLHF é candidato óbvio a essa pressão. Já se tentou: só a leitura de [F-004], que não trata do ponto diretamente.*
10. [ ] `fundamento` — Que experimento decidiria empiricamente a disputa "texto massivo basta × multimodalidade é obrigatória"? *Importa porque, sem critério de decisão, o debate vira disputa de definição de "grounding" em vez de questão factual. Desdobramento direto da pergunta 6. Nada tentado ainda.*
11. [ ] `fundamento` — A definição de grounding de [F-005] é sobre **informação mútua entre interlocutores**, não sobre relação símbolo–mundo. Os dois sentidos são compatíveis ou o campo empilhou dois problemas sob o mesmo nome? *Importa porque muda o alvo de engenharia: se for informação mútua, contexto do usuário e histórico de conversa contam como grounding; se for referência ao mundo, não contam. Já se tentou: leitura do trecho de [B-001] que cita [F-005] — a tensão aparece dentro do próprio parágrafo. Falta ler [F-005] na íntegra.*
12. [ ] `aplicação` — Que evidência sustenta a afirmação de [B-001] de que modelos recentes estão "provando o contrário" de [F-003]? *Importa porque, se a evidência for desempenho em benchmark, ela não responde a um argumento sobre o que é aprendível em princípio — e a afirmação seria um deslocamento do debate, não uma resposta. Já se tentou: o trecho lido afirma sem sustentar; verificar o restante do cap. 2.*

## Respondidas

1. [x] 2026-08-12 — Qual a definição literal de *grounding* adotada em [B-001], e onde? Cap. 2, adotando a definição de [F-005] (Chandu et al., 2021): *"the process of establishing what mutual information is required for successful communication between two interlocutors"*. O livro registra explicitamente que "texto massivo basta × multimodalidade é obrigatória" são questões não resolvidas. Ver [01-fundamentos/grounding.md](01-fundamentos/grounding.md). *Abriu as perguntas 11 e 12.*
2. [x] Data/fonte do lançamento do GPT-5 — anúncio oficial da OpenAI é de 07/08/2025 (o paper linkado nele é datado de 2026). Ver [LINHA-DO-TEMPO.md](LINHA-DO-TEMPO.md).
