# Quantização

> Data: 2026-08-11
> Tópico: [Palavras-chave: Quantização](../PALAVRAS-CHAVE.md)
> Fontes: livro (título não registrado — completar depois)

## Resumo

Técnica que reduz a precisão numérica dos pesos (e às vezes das ativações) de um LLM já treinado, para diminuir o espaço que ele ocupa em memória e tornar a inferência mais rápida.

## O que aprendi

- Reduz o consumo de memória: pesos representados com menos bits ocupam menos espaço (ex.: de 32 bits para 8 ou 4 bits).
- Acelera a inferência: menos dados para mover e processar significa respostas mais rápidas.
- Traz trade-offs:
  - Pequena perda de precisão nas respostas do modelo — segundo a fonte, tipicamente mínima.
  - Flexibilidade reduzida para fine-tuning em alguns casos.
- Apesar dos trade-offs, os ganhos de desempenho (memória e velocidade) costumam compensar, segundo o autor.

## Detalhes

Quantização atua sobre os pesos de um modelo já treinado, convertendo valores de ponto flutuante de alta precisão (ex.: FP32 ou FP16) para representações com menos bits (ex.: INT8, INT4). Isso reduz diretamente o tamanho do modelo em disco/memória e o volume de dados movimentado durante a inferência — por isso o ganho de velocidade.

O trade-off de precisão existe porque menos bits significam menos "resolução" para representar cada peso, introduzindo um erro de arredondamento. Na prática, esse erro costuma ser pequeno para quantizações moderadas (ex.: 8 bits), mas pode crescer em quantizações mais agressivas (ex.: 4 bits ou menos), variando por técnica e modelo.

A perda de flexibilidade para fine-tuning acontece porque pesos quantizados têm menos granularidade para ajustes finos de gradiente — por isso técnicas como QLoRA existem especificamente para permitir fine-tuning sobre modelos quantizados sem perder essa capacidade.

**Minha análise:** o balanço "pequena perda de precisão vs. grande ganho de desempenho" mencionado no livro é a razão pela qual quantização virou padrão para rodar LLMs em hardware limitado (GPUs de consumo, edge, mobile) — sem ela, muitos modelos grandes simplesmente não caberiam ou seriam inviáveis de rodar localmente. Vale registrar, porém, que "pequena" é relativo: depende do nível de quantização (8 bits costuma ser quase imperceptível; 4 bits ou menos pode degradar tarefas mais exigentes, como raciocínio complexo) e da técnica usada (quantização pós-treinamento simples vs. técnicas mais sofisticadas como GPTQ, AWQ, GGUF). Isso é um ponto a aprofundar quando eu estudar técnicas específicas de quantização.

## Dúvidas / pontos a aprofundar

- Quais são as principais técnicas de quantização (GPTQ, AWQ, GGUF, bitsandbytes) e como elas diferem?
- Em que ponto a perda de precisão deixa de ser "mínima" e passa a afetar a qualidade das respostas de forma perceptível?
- Como o QLoRA contorna a perda de flexibilidade para fine-tuning?
- Qual o nome do livro que originou essa nota (registrar em "Fontes").

## Referências relacionadas

- [Palavras-chave: Quantização](../PALAVRAS-CHAVE.md)
