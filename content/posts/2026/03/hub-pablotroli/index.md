---
title: "Hub Pablotroli.com"
description: ""
date: 2026-03-05T20:11:54-03:00
author: "Troli"
posttype: ["snippets"]
milestone: []
roadmap: []
tags: ["homemade", "dev-web"]
draft: false
---

{{< meta-data >}}

Tinha um domínio comprado, um blog no ar, e nada na raiz. Transformei o `pablotroli.com` num hub pessoal com identidade própria — estética de terminal, HTML/CSS/JS puro, zero dependências. Esse post é sobre as decisões que levaram até isso: da concepção às referências, da escolha de stack ao deploy.

## Construindo meu hub pessoal

Quando o domínio foi comprado não fazia ideia do que `pablotroli.com` poderia ser. Queria usar como base pro blog - de início pensei em `pablotroli.com/mind`, mas ainda não tinha aprendido sobre proxys e subdiretórios, então optei por um subdomínio separado.

A virada veio quando percebi que ter um subdomínio ativo e o domínio principal sem nada era quase uma contradição. Quem chegasse em `pablotroli.com` não encontraria nada. E pra alguém que acredita que presença digital importa, mas passa boa parte do tempo no terminal, isso não fazia sentido.

Não uso redes sociais há anos e não curto muito o celular. Então pensei: esse domínio podia ser um catalisador de tudo que faço na internet — GitHub, LinkedIn, blog — num lugar só.

Porque é aquilo: "quem não é visto não é lembrado".

---

## A concepção — qual era minha ideia principal

Antes de abrir qualquer aba de inspiração, já sabia algumas coisas com clareza: não queria um portfólio genérico com foto de perfil centralizada e três cards de projeto. Queria algo reconhecidamente meu — que um visitante olhasse e entendesse algo sobre quem eu sou antes de ler uma palavra.

A palavra que me veio foi _hub_. Não portfólio, não página pessoal — um ponto central de onde tudo que faço na internet pode ser acessado. Blog, projetos, formas de contato. Simples, direto, mas com personalidade.

---

## As referências — o que cada uma ensinou

Antes de escrever uma linha, pesquisei hubs de devs. Três em especial influenciaram o resultado final de formas distintas.

**taniarascia.com** ensinou que simplicidade não é ausência de conteúdo — é a escolha cuidadosa do que mostrar e como organizar.

**cassie.codes** mostrou que uma página pessoal pode ser genuinamente expressiva sem perder funcionalidade.

**joshwcomeau.com** foi um estudo de sobriedade como identidade: direto, objetivo, mas inconfundível.

---

## A identidade visual

Essa foi a decisão mais fácil de tomar e a mais difícil de explicar sem soar clichê.

Escolhi um visual terminal/hacker por ser uma estética icônica pra mim. Sou um fã assumido de Linux (I use Arch, btw) e a maior parte dos meus dias, seja no lazer, no estudo ou no trabalho, passa dentro do terminal. Faço tudo que consigo sem encostar no mouse. Faz sentido que minha presença na web reflita isso.

A paleta resultante foi deliberada: verde neon `#39ff14` como cor primária — referência direta aos monitores de fósforo dos anos 80 — e âmbar `#ffb700` como destaque exclusivo pro blog, criando hierarquia visual sem precisar de tamanhos ou pesos diferentes.

---

## A decisão de stack

Essa foi a parte que mais exigiu reflexão, e vale documentar o raciocínio porque a conclusão não é óbvia à primeira vista.

Considerei três caminhos:

**Next.js** foi descartado rapidamente. O hub não tem estado, não tem rotas complexas, não tem nada que justifique um framework. E o ecossistema JavaScript muda rápido demais — daqui a dois anos teria manutenção periódica sem ter adicionado nada de valor real ao projeto.

**C# ou Java** foi a opção mais tentadora intelectualmente. Estou aprendendo essas linguagens na Fatec agora, e existe algo genuinamente hacker em rodar um servidor C# pra servir uma página HTML estática. Mas a dívida técnica seria alta: deploy complexo, configuração de servidor, problemas que ainda não tenho maturidade pra resolver sem transformar o projeto numa dor de cabeça.

**HTML, CSS e JS puro** foi a escolha honesta. Não por falta de ambição — mas porque essa tríade é a web em sua forma mais fundamental. Existe desde o início, vai existir por muito tempo, e não tem dependências que quebram. Pra um hub que precisa de durabilidade acima de tudo, é a stack mais responsável.

---

## Decisões técnicas que valem explicação

Aqui o post muda de tom. O que vem a seguir é o processo técnico, as partes que achei mais interessantes de construir e que talvez sejam úteis pra quem quiser se inspirar e fazer algo parecido.

### A estrutura de arquivos

Três arquivos. Só isso.

```
portfolio-hub/
├── index.html   ← estrutura e semântica
├── style.css    ← toda a identidade visual
└── main.js      ← apenas o que CSS não consegue fazer
```

Essa separação garante que cada arquivo tem uma responsabilidade única e não invade a do outro. O HTML não tem estilos inline. O CSS não tem lógica. O JS não manipula visual além do estritamente necessário.

### O efeito CRT com CSS puro

O efeito de monitor antigo que você vê na página é feito inteiramente em CSS, sem imagem externa ou biblioteca. São três camadas sobrepostas usando pseudo-elementos e um elemento auxiliar no HTML.

As **scanlines** — as listras horizontais finas — são criadas com `repeating-linear-gradient` no `::before` do body. A ideia é simples: alternar faixas transparentes com faixas levemente escuras a cada 4px, simulando as linhas de varredura de monitores CRT.

O **vignette** — o escurecimento nas bordas — usa um `radial-gradient` no `::after` do body. Monitores CRT escurecem naturalmente nas bordas por causa da curvatura do tubo; aqui replicamos isso com um gradiente que vai de transparente no centro até preto nas bordas.

O **ruído** é gerado por um filtro SVG de turbulência fractal embutido como `data URI` diretamente no CSS. Zero requisições extras, zero dependências — o browser renderiza o ruído nativamente.

### O efeito de digitação sem JavaScript

A animação da tagline — aquele texto que parece estar sendo digitado — é feita só com CSS usando uma combinação de três propriedades que precisam funcionar juntas:

```css
.tagline {
  overflow: hidden;
  white-space: nowrap;
  width: 0;
  animation: type 1.8s steps(60, end) forwards 0.6s;
}
```

O `width` começa em zero e cresce até 100% via `@keyframes`. O `overflow: hidden` esconde o texto enquanto o width ainda não chegou lá. O `white-space: nowrap` impede que o texto quebre linha durante a animação. E o `steps()` — em vez de uma transição suave — faz o crescimento acontecer em saltos discretos, imitando a digitação caractere por caractere. Fontes monospace são essenciais aqui: cada caractere tem exatamente a mesma largura, tornando a ilusão perfeita.

### O relógio em tempo real

O único JavaScript necessário pro hub todo são basicamente 10 linhas: uma função que pega o horário atual com `toLocaleTimeString` e a chama a cada segundo com `setInterval`. Essa simplicidade é intencional — o JS existe aqui pra fazer exatamente o que o CSS não consegue: ler o relógio do sistema.

---

## Deploy — GitHub Pages + Cloudflare

Em vez de Vercel ou Netlify, optei pelo GitHub Pages pela natureza simples que combina com a simplicidade do hub. Com o deploy acontecendo a cada `git push`, só restou configurar o DNS pra ter um domínio personalizado — mas não vou me estender nisso aqui, porque estou escrevendo um post específico sobre DNS 101: o mínimo que você precisa saber pra ser produtivo com redes e infraestrutura.

---

## O que ficou pra v2

Nem tudo que imaginei entrou na primeira versão. Deixei de fora conscientemente algumas coisas que poderiam virar scope creep e atrasar o lançamento:

Os **posts recentes do blog** via RSS feed são tecnicamente simples — o Hugo já gera o feed, seria um `fetch()` no JS — mas não eram necessários pro hub funcionar. Ficam pra uma iteração futura.

Um **terminal interativo** como easter egg mais elaborado também ficou de fora. A ideia é boa mas pedia mais tempo de design do que queria investir agora.

Lançar algo funcional e com identidade forte é mais valioso do que segurar esperando perfeição.

---

_Você pode ver o resultado em [pablotroli.com](https://pablotroli.com) e o código no [GitHub](https://github.com/DevTroli/portfolio-hub)._

---

{{< tags >}}
