---
title: "Deploy Continuos"
description: "Deploy frequente além de uma boa prática técnica - é filosofia de vida contra procrastinação."
date: 2026-02-17T11:38:34-03:00
author: "Troli"
posttype: ["careernotes"]     # OBRIGATÓRIO - escolha 1: rant, deepdives, careernotes, snippets, design, library, offtopic
milestone: []    # Opcional: qual saga editorial pertence
roadmap: []      # Opcional: qual guia/roadmap se relaciona
tags: ["deploy", "ci-cd", "devops", "boas-praticas"]
draft: false      # Sempre inicia como rascunho
---

{{< meta-data >}}

Deploy frequente não é sobre CI/CD ou automação. É sobre ver seus projetos tocando a realidade. Hoje isso é criminalmente simples com Vercel, Netlify e afins - e mesmo assim a maioria dos devs ainda deixa tudo apodrecendo no localhost.

---

## Botando a cara a tapas
No [primeiro post](/posts/2026/01/medo-do-fracasso/) falei sobre perfeccionismo como medo disfarçado. No [segundo](/posts/2026/02/como-fiz-deploy-blog/) documentei como finalmente tirei o Troli's Mind do localhost depois de mais de 1 mês adiando algo que levou 30 minutos.

Mas tem uma coisa que não contei direito: ***o que acontece quando você para de ter medo e começa a deployar de verdade***.

Não estou falando só do blog. Estou falando de todos os projetos - pessoais, freelances, experimentos, aquele CRUD que você fez pra aprender algo novo. Cada um deles vive numa bifurcação simples:

***A) Fica no localhost pra sempre*** ou ***B) Toca a realidade***

E a diferença entre os dois não é mais técnica. Nunca foi tão fácil quanto hoje.

---
## A nocividade de não fazer deploys
Não existe nada mais nocivo no desenvolvimento de software do que deixar projetos mofando no localhost por meses.

É como deixar um carro na garagem por três meses e se surpreender quando ele não quer dar partida. A bateria descarregou. Os pneus murcharam. Algum bicho fez ninho no motor. O abandono cria problemas que não existiriam com uso regular.

Com código é idêntico. Cada dia que você adia o deploy:
- Dependências ficam desatualizadas (e você só descobre quando tentar atualizar 47 pacotes de uma vez)
- Seu ambiente local diverge da produção (e você só descobre quando der pau)
- Bugs se acumulam silenciosamente (e você só descobre quando soltar tudo de uma vez)
- Sua confiança no processo diminui (porque deploy vira "evento traumático" em vez de rotina)

Mas o pior efeito colateral é outro: você perde a sensação de que o projeto é real.

Um projeto que só existe no seu HD é teoria. Um projeto que toca a internet é realidade. 
## Deploy Contínuo vs "Deploy Ideal"

| ***Deploy Contínuo***:         | ***Deploy "Ideal"***:               |
| ------------------------------ | -------------------------------------- |
| Bugs pequenos descobertos cedo | Bugs catastróficos descobertos tarde   |
| Fixes em minutos               | Fixes em dias (você perdeu o contexto) |
| Feedback real constante        | Suposições acumuladas por meses        |
| Confiança no processo cresce   | Ansiedade sobre deploy cresce          |
| Deploy vira rotina             | Deploy vira trauma                     |
| Projeto parece real            | Projeto parece eterno rascunho         |


## Antes era uma porre. Agora é ridiculamente simples
Aqui tá o ponto que me deixa mais cismado é quando vejo amigos desenvolvedores com projeto mofando no GitHub, como se fosse um cemitério, não um hub de repositórios:

***Anos atrás fazer deploy era um tanto sofrido:***
- Configurar servidor do zero
- Gerenciar SSH, NGINX, certificado SSL
- Configurar pipelines manualmente
- Rezar pra não dar merda em produção
- Quando dava, debugar sem ferramentas decentes

**Hoje com Vercel, Netlify, Railway, Render:**
```bash
# Isso é tudo que você precisa
git push origin main
# ↑ Isso já é o deploy. Sério.
```

Você conecta o repositório uma vez, configura em 5 minutos, e a partir daí **cada push é um deploy automático**. SSL grátis. CDN global. Preview deployments automáticos pra cada branch. Rollback em um clique.

Não tem mais desculpa técnica. A única barreira que sobrou é psicológica.
## Código parado É código morto
Código que não toca a realidade não existe de verdade.

Pode ser bonito, pode ser bem arquitetado, pode ter testes unitários perfeitos. Se ninguém usa, se não resolve problema nenhum, se vive eternamente no seu HD - **não existe**.

E hoje, com toda a infraestrutura disponível gratuitamente, deixar um projeto no localhost é uma escolha ativa. Você está escolhendo ativamente que ele não exista de verdade.

Não precisa ser perfeito. Não precisa ter todas as features. Não precisa impressionar ninguém.

Precisa estar **no ar**.

Faça o deploy hoje. Mesmo que seja feio. Mesmo que tenha bugs. Mesmo que seja só pra você ver funcionando num domínio real.

Porque código parado é código morto. E projeto morto não ajuda ninguém - nem você.

---

## Fontes

-  [Continuous Delivery - Martin Fowler](https://martinfowler.com/bliki/ContinuousDelivery.html)
- [Seven Principles Shipping - basecamp.com](https://basecamp.com/gettingreal/03.7-have-an-enemy)
- [Ship it - justinnoel](https://justinnoel.dev/2020/02/08/just-ship-it/)
- Post anterior: [Como Fiz o Deploy Desse Blog](/posts/2026/02/como-fiz-deploy-blog/)

---

{{< tags >}}
