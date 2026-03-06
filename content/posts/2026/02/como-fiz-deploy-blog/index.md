---
title: "Como fiz o deploy desse blog"
description: "Hugo + Hextra + Vercel em 30 minutos de setup"
date: 2026-01-31T12:11:13-03:00
author: "Troli"
posttype: ["snippets"]
milestone: []    # Opcional: qual saga editorial pertence
roadmap: []      # Opcional: qual guia/roadmap se relaciona
tags: ["deploy ", "vercel ", "devops"]
draft: false
---

{{< meta-data >}}

No [post anterior](/posts/2026/01/medo-do-fracasso/), escrevi sobre como perfeccionismo é medo disfarçado. Depois de dizer tudo aquilo não posso ficar só na teoria — este post documenta o deploy real que finalmente tirou o Troli's Mind do localhost. Porque o deploy de um blog estático com Hugo + Vercel levaria literalmente 10 minutos. Eu levei mais de ***1 mês***. O problema nem foi devido a complexidade na configuração — foi psicológico.

Sendo direto o processo técnico para mim levou uns 30 minutos. O processo mental de "pressionar os botões" levou semanas.

Se você também tem projetos mofando no seu GitHub que nunca tocaram na realidade, este post pode ser útil para você que está lendo e para o meu eu do futuro, quando esquecer como configurei tudo isso.

### Do SDD para internet

#### 0. Setup Local

```bash
# Comandos que você realmente executou
hugo new site trolismind
cd trolismind
git init.
```

#### 1. Setup Local → GitHub

Depois de ter o blog rodando em `localhost:1313` com o Hugo+Hextra, o primeiro passo foi versionar isso no Git (não farei um tutorial de git, mas no futuro irei fazer um guia melhor). Agora Vou listar os comandos que usei para versiornar o código para mostrar como git é bem simples na real

```bash
# Inicializar repositório Git
git init

# Adicionar todos os arquivos que quero versionar
git add nome_arquivo ou .

# Primeiro commit
git commit -m "🎉 Initial commit: Hugo + Hextra setup"

# Conectar com GitHub (crie o repo lá antes)
git remote add origin https://github.com/DevTroli/trolisMind.git

# Trocar o nome padrão da branch para Main por boas práticas
git branch -M main

# Enviar todo o código para o GitHub para versionar o código
git push -u origin main
```

---

#### 2. Vercel: Conectar e fazer o Deploy


***A) Criar conta na Vercel***

- Acessei [vercel.com](https://vercel.com)
- Fiz login com minha conta do GitHub


![Screenshot: Lista de repositórios na Vercel](https://servidor-estaticos-ashy.vercel.app/v2/trolismind/post2_login.png)

***B) Importar o projeto do GitHub***

- Cliquei em "Add New Project"
- A Vercel detectou automaticamente meu repo `trolisMind`
- Selecionei ele

![Screenshot: Lista de repositórios na Vercel](https://servidor-estaticos-ashy.vercel.app/v2/trolismind/post2_newproject.png)

<!-- TODO: Adicionar print mostrando a tela de seleção de repo -->

***C) Configuração do Build***
A Vercel não detectou automaticamente que era Hugo logo precisei ajustar

![Screenshot: Build settings na Vercel](https://servidor-estaticos-ashy.vercel.app/v2/trolismind/post2_build.png)

<!-- TODO: Adicionar print das configurações de build -->

Configurações que usei:

- **Framework Preset:** Hugo
- **Build Command:** `hugo --gc --minify`
- **Output Directory:** `public`
- **Install Command:** (deixei vazio, Vercel resolve automaticamente)

***4) Variáveis de ambiente*** (se necessário) No meu caso, não precisei configurar nenhuma env var nesse primeiro deploy. Mas se você tiver coisas como tokens de API ou configurações sensíveis, é aqui que vai.

***5) Deploy!*** Cliquei em "Deploy" e espere a mágica acontecer

![Screenshot: Build em progresso](https://servidor-estaticos-ashy.vercel.app/v2/trolismind/post2_deploy.png)


**Tempo total:** ~2 minutos até a vercel fazer o build da aplicação e fazer o blog estar no ar.

***Gerando a URL padrão da Vercel [nome_repo]+vercel.app:*** `trolismind.vercel.app`

---

#### 3. O Primeiro bug em produção

***Spoiler:*** O primeiro deploy não funcionou de primeira.

**O erro:**

```
Error: failed to render pages: render of "posttypes" failed
```

**O que aconteceu:** Tive um problema de layout nos `posttypes` e ele só apareceu ***depois do deploy em produção***. No localhost tava funcionando, mas na Vercel deu pau.

***Como resolvi:***

1. Li o log de erro completo na Vercel (não só a primeira linha)
2. Identifiquei que era um problema no template `layouts/posttypes/list.html`
3. Voltei pro código local, corrigi o bug
4. Fiz commit + push pro GitHub
5. **Vercel fez redeploy automático** (bom demais a propósito)

***Tempo pra resolver:*** ~15 minutos debugando + 2 minutos esperando o novo deploy.

***Lição aprendida:*** Bugs que não aparecem no `hugo server -D` podem aparecer em produção. Por isso é importante fazer deploy cedo, mesmo que o blog não esteja "perfeito".

---

### Stack Escolhida

##### Função vs Estética

Existe um dilema clássico em qualquer projeto pessoal: escolher entre **o que resolve** (função) e **o que te empolga** (estética). Um código impecável mas sem identidade é como um robô eficiente. Um design deslumbrante mas sem propósito é um castelo de areia.

Queria uma stack perfeita: moderna, escalável, impressionante. Gastei horas comparando frameworks, lendo benchmarks, planejando arquiteturas. ***E não escrevi uma linha de conteúdo.***

Até que li [este post no blog do Akita](https://akitaonrails.com/2025/09/10/meu-novo-blog-como-eu-fiz/) sobre como ele fez o blog dele. E uma mensagem fixou na minha mente:

> _"O blog perfeito não existe — just fucking write!"_

Percebi que estava usando a escolha de tecnologia como **desculpa sofisticada para não começar**. Paralisia por análise disfarçada de "pesquisa técnica".

A real é que as melhores escolhas não são as mais modernas, mas **as que te deixam focar no que importa**. E pro Troli's Mind, o que importa é escrever.

##### Hugo + Hextra

***Por que escolhi:***
Hugo é um gerador de sites estáticos notório por uma razão simples: **é absurdamente rápido e direto ao ponto**. Perfeito pra quem vem de contexto tech, mas quer desenvolver habilidades de escrita, não virar especialista em build pipelines.

O workflow é super simples:
1. Escrevo markdown no Obsidian
2. Rodo `hugo server` pra ver localmente
3. Faço `git push`
4. Deploy automático acontece

Não tem nada para se configurar. Não tem "hmm, deixa eu atualizar essas 17 dependências que ficaram vulneráveis".

***Sobre o Hextra:*** Me pareceu o tema ideal - minimalista, limpo, focado em **conteúdo**. E de graça. Quando vi o demo, pensei: "Perfeito. Não preciso customizar nada. Posso começar a escrever hoje."

E foi exatamente o que fiz.

**Alternativas que considerei:**
- **Django?** Seria como usar uma bazuca pra matar uma formiga. Framework web completo com ORM, admin panel, autenticação... pra servir markdown estático? Absurdo Total.
- **Next.js?** Era minha ideia original, mas... o setup inicial me travou. Configurar TypeScript, ESLint, Prettier, escolher entre Pages Router vs App Router, decidir sobre SSR vs SSG vs ISR... Cara, eu só queria escrever um post para começar o blog :(

##### Vercel

***Por que escolhi:***
Uso Vercel há anos. Nunca me deu problema. É aquilo: _"time que está ganhando não se mexe"_.

Além da familiaridade, outros motivos seriam:
- **Grátis** pra projetos pessoais (com limites generosos)
- **Deploy automático** a cada push no GitHub
- **Analytics embutido** (simples & funcional)
- **Preview deployments** automáticos pra pull requests
- **UI/UX excelente** - não preciso ler docs pra entender

Conectei o repo, cliquei em "Deploy", esperei 2 minutos. Funcionou.

***Alternativas:***
Considerei brevemente **Netlify** e **GitHub Pages**, mas honestamente? Não tinha motivo pra trocar. Netlify oferece funcionalidades similares à Vercel (ambas são ótimas). GitHub Pages é ainda mais simples, mas tem algumas limitações com builds customizados.

No fim, escolhi Vercel porque já estava na minha zona de conforto. E nesse projeto, reduzir fricção era mais importante que explorar alternativas. Já tinha fricção mental suficiente. Não precisava adicionar fricção técnica também.

Se você nunca usou nenhuma das três, todas funcionariam bem pro mesmo propósito. Escolha a que tiver melhor documentação pro seu caso.

##### Ferramentas São Temporárias

O lance é que em 5 anos talvez eu migre esse blog pra outra stack. Ou não. Tanto faz.

***Tecnologias mudam.*** Hugo pode cair em desuso. Vercel pode virar paga. JavaScript pode inventar um novo framework revolucionário (de novo).

Mas ***minha capacidade de adaptar, criar e recomeçar*** — isso não muda. E o mais importante: **o conteúdo que eu escrever vai continuar relevante**, independente da ferramenta que usei pra publicá-lo.

Markdown é Markdown. Posts sobre comunicação, carreira, e decisões técnicas não expiram porque mudei de gerador de site.

No fim, ferramentas são temporárias. Sua criatividade e habilidade de comunicar, não.

### O Que Esse Deploy Ensinou

Fazer o deploy desse blog me ensinou mais sobre processo do que sobre tecnologia. Aqui vão as lições práticas que levei:

***Técnico:***
- Bugs em produção não são um bicho de 7 cabeças. Aquele erro de build que deu na Vercel? Foi resolvido em 15 minutos. Não foi um bicho de sete cabeças.
- **É impressionante como Hugo compila tão rápido.** `hugo server` → ver resultado em 2 segundos. Isso acelera MUITO o desenvolvimento.

***Psicológico:***
- O pensamento de "ainda não estar pronto" me paralisava mais que qualquer bug real. Passei semanas preocupado com o que poderia dar errado. Quando finalmente fiz o deploy, a maioria dos meus medos era imaginários
- Feedback do mundo real > mil horas de planejamento. No fim descobri que o layout dos posttypes tinha bug. No localhost, tava "funcionando". Produção é o teste real.

### Agora é só continuar

No post anterior, falei sobre perfeccionismo como medo disfarçado. Este post seria meu jeito superar — mesmo que temporariamente — essa paralisia.
 
O blog não está perfeito. Ainda tem bugs. Ainda falta muito conteúdo. ***E está tudo bem por agora.***

Se você também tem um projeto mofando no seu GitHub esperando o momento ideal: faça o deploy hoje. O feedback do mundo real vale mais que 100 horas de planejamento no localhost.
 
Hora de escrever o próximo post. E o próximo. E o próximo...

---

### Fontes

**Documentação:**
- [Hugo Docs](https://gohugo.io/documentation/)
- [Hextra Theme](https://imfing.github.io/hextra/)
- [Vercel Docs](https://vercel.com/docs)

**Posts/recursos que ajudaram:**
 [Meu Novo Blog - Como Eu Fiz](https://imfing.github.io/hextra/)

**Repositório do blog:**
 [Troli's Mind](github.com/DevTroli/trolismind)

---

{{< tags >}}
