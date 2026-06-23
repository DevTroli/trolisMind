---
title: "Quanto custa o bug?"
description: ""
date: 2026-06-23T17:38:26-03:00
author: "Troli"
posttype: ["deepdives"]
milestone: []
roadmap: []
tags: ["llm", "benchmark-agents", "A.I" ]
draft: false
---

{{< meta-data >}}

Depois do último post me deu uma sobrecarga enorme desse papo de I.A, mas agora recarreguei as baterias e continuarei com a saga, que vai totalizar 5 posts, esse sendo o segundo.

Enfim, todo mundo tem uma opinião sobre agents. "Múltiplos agents é o futuro." "Um modelo forte basta." "Orquestração de modelos é overengineering." Eu queria ir além da opinião. Montar um benchmark, coletar dados, ver o que acontece. Resultado: **14 runs, 3 linguagens, 3 modos, 10 dias.**

## O Setup

Usei o OpenClaude (CLI open-source baseado no Claude Code) com modelos gratuitos da NVIDIA NIM. Os modelos gratuitos do NIM têm limitações reais de context window, respostas truncadas em runs longas, agente travando em tasks extensas. Isso afeta o Solo diretamente e é parte do que os dados medem. Comparei duas abordagens com o mesmo modelo-base:

**Solo:** Um agente code executando diretamente, sem estrutura forçada.

**Pipeline:** 5 estágios obrigatórios — architect → codebase → code → refactor → write. Cada estágio consome o output do anterior

Testei em 3 níveis de complexidade com stacks radicalmente diferentes, escolhidas para eliminar viés de familiaridade, o modelo não pode "decorar" padrões de uma linguagem só:

- **Fácil:** PHP (calculadora com operações básicas)
- **Médio:** Go (habit tracker com interface TUI)
- **Difícil:** Rust (sistema de leilões concorrentes com `Arc<Mutex>`)

3 repetições Solo, 2 repetições Pipeline por tier. Avaliação por rubrica de 8 dimensões numa escala 0-100.

## Os Números

| **Fácil (PHP)**   | **Fácil (PHP)** | **Médio (Go)** | **Médio (Go)** | **Difícil (Rust)** | **Difícil (Rust)** |                    |
| ----------------- | :-------------: | :------------: | :------------: | :----------------: | :----------------: | :----------------: |
|                   |      Solo       |    Pipeline    |      Solo      |      Pipeline      |        Solo        |      Pipeline      |
| **Score médio**   |      79.7       |      89.5      |      89.0      |        84.0        |        56.7        |        81.0        |
| **Melhor rep**    |       87        |       90       |       90       |         87         |         74         |         84         |
| **Duração média** |     ~19min      |     ~97min     |     ~75min     |      ~111min       |       ~90min       |      ~130min       |
| **Bugs críticos** |    0.33 avg     |       0        |       0        |         0          |      0.67 avg      |       **0**        |
| **Travou?**       |       Não       |      Não       |      Não       |        Não         |      **100%**      |       **0%**       |
| **Veredito**      |   Solo vence    |                |   Solo vence   |                    |                    | **Pipeline vence** |

**Fácil e Médio:** Solo é melhor ou equivalente, e consistentemente mais rápido. Pipeline entrega +9.8pts em Fácil, mas custa 5.1x mais tempo. Em Médio, Solo supera Pipeline em score _e_ em velocidade.

**Difícil:** a diferença é qualitativa. Solo trava em 100% das tentativas iniciais em Rust. Pipeline entrega zero bugs críticos em todas as runs, sem travar uma vez sequer. Solo cai 32 pontos entre Médio e Difícil (89→57). Pipeline cai apenas 3 pontos (84→81).

## Por Que Pipeline Funciona

Pense na construção de uma casa. Se você vai trocar uma torneira, não contrata um arquiteto para redesenhar a planta. Vai lá e troca. Mas se vai construir um prédio de 10 andares, o arquiteto desenha a planta antes da primeira parede sair do chão,  porque sem planta ele não sabe onde colocar cada tijolo.

Com agents é a mesma lógica:

**O architect** desenha a planta antes do código existir, contratos entre módulos, padrões a seguir, trade-offs explícitos. Em Rust especificamente, isso significa definir a state machine _antes_ de escrever um único `match`.

**O codebase** prepara o terreno: scaffold dos arquivos, `@purpose` e `@responsibilities` de cada módulo. O code agent preenche contratos, não inventa estrutura no meio da execução.

**O code** implementa seguindo o plano. Crítico em Rust, onde ownership e tipos não admitem meios-termos, pois implementar sem um plano em Rust é garantia de retrabalho.

**O refactor** é a vistoria: audita segurança, thread safety, edge cases, stubs esquecidos. É o estágio que encontra o `lock` global onde deveria haver `lock` fino, o estado `Cancelado` ausente na state machine, o `f64` arredondando errado em aritmética monetária.

Na prática: **Pipeline teve zero bugs críticos em 6 runs**. Solo teve bugs críticos em 4 de 8 runs nas tasks Difícil. Para produção, essa é a diferença entre um "deploy com confiança" e "faz o deploy e reza".

## Pipeline Aprende Sozinho

Repetição 2 sistematicamente supera Repetição 1 sem nenhuma intervenção humana, Fácil +1pt, Médio +6pts, Difícil +6pts. O mecanismo é direto: ao final de cada run, o estágio write gera um audit com os problemas encontrados. Na run seguinte, o architect lê esse audit automaticamente e incorpora as lições no design antes do primeiro arquivo ser escrito.

Na Repetição 2 Difícil, as 5 lições catalogadas no audit da Repetição 1 foram preventivamente resolvidas no design. Solo também melhora entre repetições (46→50→74), mas requer que um humano leia o código da tentativa anterior, catalogue os bugs, e reescreva o prompt. O custo humano é zero. O custo de compute é maior, entretanto o compute da maquina é barato. Seu tempo não é.

## O Que Akita Mediu, e Onde Divergimos

O Fabio Akita publicou um [post de benchmark](https://akitaonrails.com/2026/04/25/llm-benchmarks-vale-a-pena-misturar-2-modelos/) que foi minha inspiração para esse aqui. Sua conclusão foi direta, não misture modelos, use um forte sozinho. O overhead de coordenação consome o ganho hipotético de especialização.

Meu benchmark corrobora, com um nuance importante: **o valor do pipeline está na estrutura forçada do processo**. O Pipeline Difícil usou o mesmo modelo como architect _e_ como code agent, e ainda assim superou o Solo em +24pts. O ganho veio de planejar antes de codar, mapear antes de implementar, auditar antes de entregar. Estrutura de processo.
## A Regra Prática

|Tamanho da task|Abordagem|Razão|
|---|---|---|
|1–2 arquivos, lógica direta|**Solo**|Audit não agrega valor que justifique 5x tempo|
|3–8 arquivos CRUD|**Solo**|Zero bugs, pipeline só adiciona overhead|
|9+ arquivos com regras de negócio|**Pipeline**|Audit elimina bugs críticos, learning melhora iterações|
|Concorrência, state machines, invariantes implícitos|**Pipeline**|Zero bugs críticos, sem travamento, +24pts|
## Limitações

Este benchmark tem restrições que merecem ser ditas em voz alta: 2 repetições Pipeline por tier (pouco para significância estatística), avaliação por rubrica subjetiva, modelos instáveis em runs longas, e viés de permissão nos subagentes que inflacionou alguns tempos de Solo na primeira bateria. A direção dos dados é clara; a magnitude exata merece verificação com mais repetições.

O projeto inteiro consumiu ~12h de tempo humano e ~18h de compute em 10 dias, gerando 19.600 linhas de código e 149 testes com modelos gratuitos — esse número é o custo de infraestrutura, não uma afirmação de que seu tempo é gratuito.

Na próxima vez que for pedir a um LLM algo, se pergunte: **quanto custa o bug?**

---

## Posts derivados deste benchmark

Este post é a âncora de uma série de 4 artigos que aprofundam aspectos específicos dos dados. Cada um pode ser lido de forma independente, mas faz mais sentido na ordem:

- **[Otimização Prematura em Agents](https://blog.pablotroli.com/posts/2026/05/otimizacao-prematura/)** — a ironia de montar um pipeline para testar se pipeline agrega valor, e o que Knuth diria sobre isso
- **[A Linha Tênue Entre "Funciona" e "Colapso"](https://blog.pablotroli.com/posts/2026/06/colapso-ou-funciona/)** — por que Solo cai 32 pontos e Pipeline cai 3 pontos no mesmo salto de complexidade
- **[Toda Decisão de Engenharia é Econômica](https://blog.pablotroli.com/posts/2026/07/economia-do-bug/)** — o framework custo(audit) vs custo(bug) e como aplicar a qualquer decisão técnica
- **[Posso Só Melhorar o Prompt?](https://blog.pablotroli.com/posts/2026/07/prompt-ou-pipeline/)** — sim, mas você não vai querer quando descobrir o custo invisível de ser o seu próprio audit agent

_Dados brutos, logs completos e rubrica de avaliação: [github.com/pablotroli/benchmark-pipeline-vs-solo](https://github.com/pablotroli/benchmark-pipeline-vs-solo)_

---

{{< tags >}}
