# Bias e Fairness no pré-treinamento

> Data: 2026-08-14
> Tópico: [01-fundamentos](../) · [Palavras-chave: Bias, Fairness](../PALAVRAS-CHAVE.md)
> Fontes: [F-010] Caliskan et al. (2017) · [F-011] Blodgett et al. (2020) · [F-012] Bender et al. (2021) · [F-013] Dodge et al. (2021) · [F-014] Kleinberg et al. (2016) · [B-003] Barocas, Hardt & Narayanan (2023)

## Resumo

*Bias* (viés) é uma **disparidade sistemática** no comportamento do modelo associada a grupos
sociais — algo que se mede. *Fairness* (justiça/equidade) é o **critério normativo** que diz
qual disparidade é aceitável e qual não é — algo que se escolhe. A distinção é o ponto
central: medir viés é problema técnico; decidir o que é justo não é, e nenhuma quantidade de
dados resolve por você.

## O que aprendi

- **Viés no corpus é mensurável e espelha o viés humano.** [confirmado] Caliskan et al.
  mostraram, na *Science*, que associações extraídas automaticamente de corpora de texto
  reproduzem vieses humanos documentados — replicando resultados do Teste de Associação
  Implícita (IAT) a partir apenas de estatística de coocorrência [F-010]. O modelo não
  "inventa" o viés: ele o herda da distribuição do texto.
- **"Bias" é usado de forma vaga na própria literatura.** [confirmado] Blodgett et al.
  revisaram **146 artigos** sobre viés em PLN e concluíram que *"their motivations are often
  vague, inconsistent, and lacking in normative reasoning"*, com métodos quantitativos
  frequentemente desconectados dos objetivos declarados [F-011]. A recomendação central é
  articular explicitamente *"what kinds of system behaviors are harmful, in what ways, to
  whom, and why"*. Sem isso, "reduzimos o viés em X%" não quer dizer nada.
- **Escala não é representatividade.** [confirmado como argumento] [F-012] sustenta que
  corpus grande não é corpus diverso: o que está na web reflete quem tem acesso, tempo e
  poder para publicar, e o tamanho dificulta — em vez de facilitar — a auditoria e a
  documentação do que entrou.
- **A filtragem "de limpeza" introduz viés por si só.** [confirmado] Este é o achado mais
  específico do pré-treinamento: ao documentar o C4, Dodge et al. mostraram que a filtragem
  por lista de bloqueio *"disproportionately removes text from and about minority
  individuals"* [F-013]. A tentativa de remover conteúdo tóxico acabou removendo a voz de
  quem se queria proteger.
- **Não existe "o" critério de justiça — eles são matematicamente incompatíveis.**
  [confirmado] Kleinberg et al. provaram que, *"except in highly constrained special cases,
  there is no method that can satisfy these three conditions simultaneously"* [F-014],
  tratando de três critérios usuais de fairness em classificação. Consequência direta:
  "tornar o modelo justo" não é uma especificação técnica — é preciso dizer *justo segundo
  qual critério*, e assumir o que se perde ao escolher.

## Detalhes

### Onde o viés entra na cadeia de pré-treino

Cada etapa é uma decisão editorial, mesmo quando apresentada como técnica:

1. **Seleção de fonte** — quais domínios, línguas e períodos entram. [F-013] encontrou no C4
   volume inesperado de patentes e de sites militares dos EUA.
2. **Filtragem de qualidade e toxicidade** — onde ocorre o efeito de [F-013] acima.
3. **[Deduplicação](deduplicacao.md)** — decidir o que é "o mesmo texto" também é decidir o
   que sobrevive. `[hipótese]` Não vi trabalho medindo o efeito demográfico da deduplicação
   como [F-013] mediu o da filtragem; virou pergunta aberta.
4. **Amostragem e pesos** — repetir Wikipédia e reduzir fóruns muda a voz do modelo. É a
   distribuição final, não o corpus bruto, que treina.
5. **Tokenização** — `[hipótese]` línguas sub-representadas tendem a gastar mais tokens por
   palavra, o que encarece o uso e reduz o contexto efetivo. Efeito plausível e citado
   informalmente, mas **não verifiquei em fonte primária**.

### Tipos de dano, que é o que [F-011] pede para nomear

Falar em "viés" no singular esconde danos de naturezas diferentes:

- **Dano alocativo** — o sistema distribui oportunidade ou recurso de forma desigual (triagem
  de currículo, análise de crédito).
- **Dano representacional** — o sistema reforça estereótipo, apaga ou descreve
  pejorativamente um grupo, mesmo sem alocar nada. É o dano típico de um LLM gerando texto.
- **Qualidade de serviço** — o sistema simplesmente funciona pior para um grupo (ex.: pior
  desempenho em variedades linguísticas minorizadas).

### Pré-treino × pós-treino

Boa parte do esforço de mitigação hoje acontece **depois** do pré-treino (RLHF, *guardrails*,
filtro de saída). `[hipótese]` Isso trata a manifestação, não a representação interna — o
modelo continua tendo aprendido as associações de [F-010], apenas fica menos disposto a
verbalizá-las. Se estiver certo, viés de pré-treino e recusa de saída são camadas
independentes, e medir só a saída superestima a mitigação. Não confirmei em fonte.

## Minha análise

O que me parece mais importante — e é o mesmo padrão que encontrei em
[deduplicação](deduplicacao.md) — é que a curadoria de dados é apresentada como **higiene
técnica** e opera como **decisão normativa**. [F-013] é a demonstração limpa disso: a mesma
operação que a documentação chama de "clean" no nome do corpus (*Colossal Clean Crawled
Corpus*) é a que remove desproporcionalmente texto de e sobre minorias. Ninguém decidiu isso;
foi efeito colateral de uma lista de palavras. Curadoria sem medição de efeito demográfico é
uma política implícita.

A segunda coisa que reorganizou minha cabeça foi [F-014]. Eu tratava fairness como um
objetivo a maximizar, algo em que se pode sempre melhorar um pouco mais. Se os critérios são
mutuamente incompatíveis fora de casos degenerados, então fairness é um **espaço de escolhas
com trade-off explícito**, e a pergunta útil deixa de ser "esse modelo é justo?" para virar
"que critério esse sistema privilegia, e quem paga a conta dessa escolha?". Isso também
explica por que [F-011] insiste tanto em nomear *a quem* o dano ocorre: sem sujeito, não há
como avaliar o trade-off.

`[hipótese]` Suspeito que [F-014] não se transfira diretamente para LLM generativo — ele é
formulado para classificação com rótulo e grupo definidos, e geração de texto aberto não tem
nem "predição positiva" nem grupo explícito. Se for esse o caso, a incompatibilidade é uma
analogia útil, não um teorema aplicável. Precisa de verificação antes de eu repetir isso como
fato.

## Mapa da ignorância

- O resultado de impossibilidade de [F-014] se aplica formalmente a modelos generativos, ou
  só a classificadores? É a hipótese acima e não devo repeti-la sem fechar.
- A [deduplicação](deduplicacao.md) tem efeito demográfico mensurável, como [F-013] mostrou
  para a filtragem? Alguém replicou aquela metodologia para outras etapas do *pipeline*?
- Mitigação em pré-treino (curadoria, rebalanceamento) versus pós-treino (RLHF, filtro de
  saída): há comparação empírica de eficácia e de custo?
- Como se mede viés em modelo generativo, já que WEAT [F-010] foi feito para *embeddings*
  estáticos? O que substituiu esse instrumento?
- A hipótese da tokenização (línguas sub-representadas gastam mais tokens) tem fonte
  primária com números?
- Como [B-001] trata bias/fairness na preparação de dados? Verificar e ligar a esta nota.

## Referências relacionadas

- [Palavras-chave: Bias, Fairness](../PALAVRAS-CHAVE.md)
- [Deduplicação](deduplicacao.md) — outra etapa de curadoria com efeito não neutro
- [Grounding](grounding.md) — [F-012] e [F-003] compartilham autora (Emily M. Bender) e a
  mesma preocupação de fundo com o que o texto sozinho carrega
- [06-avaliacao-seguranca](../06-avaliacao-seguranca/) — medição de viés e *guardrails*
- [BIBLIOGRAFIA.md](../BIBLIOGRAFIA.md) — [B-003]
