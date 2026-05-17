---
title: "Quando a Orquestração de Agents É Otimização Prematura"
description: "14 runs, 3 linguagens, 10 dias. Por que eu passei esse tempo testando numa jornada sobre orquestração de agents"
date: 2026-05-17T10:39:43-03:00
author: "Troli"
posttype: ["rant"]
milestone: []
roadmap: []
tags: ["benchmark-agents", "llm", "A.I"]
draft: false
---

{{< meta-data >}}

TL;DR: Montei um pipeline de 5 agents para testar se pipelines de 5 agents agregam valor. Gastei 10 dias e 14 rodadas para descobrir que eu era o caso clássico de otimização prematura. A lição: meça antes de estruturar.

Entrei no mundo das LLMs quando o código-fonte do Claude Code vazou. Testei o OpenClaude — era mais interessante do que eu imaginava. Com isso em mente, criei agents para cada etapa do meu fluxo de desenvolvimento. Para automatizar tudo, montei um pipeline de 5 agents. Para testar se pipeline de 5 agents agrega valor. Sem perceber que eu era o exato caso de uso do meu próprio benchmark. Quando me toquei disso, já tinha feito 14 rodadas de testes, usado 3 linguagens durante uns 10 dias.

---

Quando descobri a orquestração de agentes, minha primeira reação foi imediata: "Vou usar pipelines para tudo". Por que usar um agente se posso usar cinco especializados em um fluxo elegante? Parecia o futuro. Precisei de 10 dias e 14 rodadas de testes para aprender que o simples funciona muito bem.

Donald Knuth já alertava em seu clássico artigo de 1974 que a otimização prematura é a raiz de todo o mal. Ele falava sobre a escrita de código, mas a regra se aplica a qualquer coisa relacionada: focar em eficiências complexas antes da hora cobra o seu preço em tempo e legibilidade.

## O Que Eu Fiz de Errado

Eu otimizei o **processo** antes de validar se o processo precisava de otimização. Montei um pipeline de 5 estágios — architect → codebase → code → refactor → write — e assumi que mais estrutura sempre equivale a melhor resultado.

Não medi. Não comparei. Só assumi.

A ironia só bateu depois: eu montei um pipeline de 5 agents _para testar se pipeline de 5 agents agrega valor_. A pesquisa em si era um caso de otimização prematura.

Eu fui, literalmente, a pessoa que mais precisava do meu próprio benchmark.

## O Que 14 Runs Mostraram

Depois de 14 runs, 3 linguagens, 3 repetições por tier, e 19.600 linhas de código geradas, a realidade foi mais nuançada do que minha empolgação inicial:

**Fácil (PHP — calculadora básica):** Pipeline entrega +9.8pts em score, mas custa 5.1x mais tempo. Overhead puro. Você está pagando por uma estrutura que a tarefa não exige.

**Médio (Go — habit tracker com TUI):** Solo é melhor _e_ mais rápido. Pipeline melhora com repetição, mas nunca alcança o desempenho do Solo nesse tier. A estrutura adiciona custo sem proporcionar benefício equivalente.

**Difícil (Rust — sistema de leilões concorrentes):** Pipeline entrega +24pts. Zero bugs críticos em 6 runs. Solo travou em 100% das tentativas iniciais e gerou bugs críticos em mais da metade das execuções.

Eu tinha certeza de que pipeline era melhor para tudo. Na verdade, pipeline só é indispensável onde o custo do bug é alto.

## O Pico dos dados

Solo cai **32 pontos** entre Médio e Difícil: de 89 para 57. Pipeline cai apenas **3 pontos**: de 84 para 81.

Quando a complexidade cruza o limiar entre Médio e Difícil, Solo não degrada gradualmente — ele colapsa. Pipeline mantém o nível quase intacto. O que muda entre os dois tiers? Bugs que só aparecem em situações que o modelo não consegue prever sozinho, sem estrutura forçada de processo.

Em Go, o habit tracker tem contratos óbvios: criar hábito, marcar dia, ver progresso. Os bugs são visíveis, o domínio cabe no context window. Em Rust, o sistema de leilões exige state machine (Aberto→Fechado/Cancelado), `Arc<Mutex>` thread-safe, f64 rounding para aritmética monetária. É exatamente nos edge cases que ninguém pensa espontaneamente que o audit agent faz a diferença. O audit agent pensa neles. O Solo não.

## O Paralelo com Código

**Tarefa simples:** O gargalo é latência de coordenação. Pipeline adiciona 5 agentes onde 1 bastava. O overhead de coordenação consome qualquer ganho hipotético de especialização.

**Tarefa média:** O gargalo é velocidade de execução pura. Solo já entrega zero bugs nesse tier; pipeline só adiciona overhead de ~1.5x sem contrapartida em qualidade.

**Tarefa complexa:** O gargalo é falta de estrutura. Solo se perde em state machines, concorrência, invariantes implícitos que precisam ser rastreados simultaneamente. Pipeline fornece o esqueleto que falta — e o audit agent pensa nos edge cases que o Solo esquece.

Otimizar o gargalo errado piorou os resultados. Eu otimizei coordenação quando o gargalo real era velocidade. O problema é que eu não sabia distinguir um do outro antes de medir.

## Pipeline Aprende. Solo Esquece.

Um dado que eu genuinamente não esperava: Pipeline Rep2 sistematicamente supera Rep1 **sem nenhuma intervenção humana** — Fácil +1pt, Médio +6pts, Difícil +6pts. O architect agent lê o audit da run anterior e incorpora as lições no plano seguinte, automaticamente.

Na Rep2 Difícil, zero bugs críticos — porque as 5 lições catalogadas no audit da Rep1 foram preventivamente resolvidas no design antes do primeiro arquivo ser escrito. Solo também melhora entre repetições (46→50→74), mas requer engenharia de prompt manual: um humano lendo código, catalogando o que deu errado, reescrevendo instruções. Pipeline aprende sozinho. Isso muda o cálculo: pipeline não é só mais seguro em tasks complexas — é mais **escalável** sem aumentar o esforço humano.

## O que Eu Deveria Ter Deduzido Sozinho

Se eu pudesse voltar ao dia zero, teria começado com Solo + revisão manual. Antes de montar qualquer pipeline, testaria Solo, revisaria o resultado com meus próprios olhos, catalogaria o que deu errado. Se a revisão manual encontrasse os mesmos bugs que o audit agent encontra automaticamente, eu saberia que pipeline agrega valor de automação — não de qualidade. E poderia justificar o custo de setup com evidência real.

Mas precisei dessa jornada inteira pra chegar nessa conclusão. E o benchmark justificou a si mesmo — só porque eu medi em vez de assumir.

Eu poderia ter começado com 2 agents — um para planejar, um para executar — e chegado às mesmas conclusões com metade do esforço de setup.

Se o custo do bug é baixo, use Solo. Se é alto, use Pipeline. Se não sabe, meça.

> Se a tarefa cabe na sua cabeça, um agente basta.
> Se a tarefa não cabe na sua cabeça, estruturar o prompt ajuda.
> Se está no meio termo: meça antes de decidir.

É o que Knuth diria sobre agents. É o que 14 runs confirmaram.

Otimização prematura em agents é sobre não perguntar _"quanto custa o bug?"_ antes de decidir quantos agents você precisa.

---

## Fontes

- [Structured Programming with go to Statements](https://dl.acm.org/doi/10.1145/356635.356640) — Donald Knuth, 1974
- [github.com/DevTroli/benchmark-pipeline-vs-solo](https://github.com/DevTroli/benchmark-pipeline-vs-solo) — dados brutos do benchmark

---

**Leia também nesta série:**

- [Benchmark agent solo vs multiagent](https://blog.pablotroli.com/posts/2026/05/pipeline-vs-solo/) — o benchmark completo com dados de todas as 14 runs
- [A Linha Tênue Entre "Funciona" e "Colapsa"](https://blog.pablotroli.com/posts/2026/06/colapsa-ou-funciona/) — por que Solo cai 32 pontos e Pipeline cai 3 no mesmo salto de complexidade
- [Toda Decisão de Engenharia é Econômica](/posts/2026/06/economia-do-bug/) — o framework custo(audit) vs custo(bug) aplicado a qualquer decisão técnica
- [Posso Só Melhorar o Prompt?](/posts/2026/06/prompt-ou-pipeline/) — sim, mas você não vai querer quando ver o custo invisível

_Dados brutos e logs completos: [github.com/DevTroli/benchmark-pipeline-vs-solo](https://github.com/DevTroli/benchmark-pipeline-vs-solo)_

---

{{< tags >}}
