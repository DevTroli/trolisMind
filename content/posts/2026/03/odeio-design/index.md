---
title: "Design é um problema técnico. Trate como tal."
description: "Design é uma das coisas que menos gosto de fazer, Escrevi sobre o que aconteceu quando você trata estética como engenharia."
date: 2026-03-13T08:56:14-03:00
author: "Troli"
posttype: ["design"]     
milestone: []    
roadmap: []      
tags: ["design", "figma", "UI/UX"]
draft: false
---

{{< meta-data >}}

Primeiro projeto profissional de design: 5 painéis pra stand de feira corporativa, CMYK, 300 PPI, entrega em 2 semanas. Não nada gosto de design, mas fiz. Esse post é sobre o processo de ser ruim em algo novo.

## Quando o design bate a porta

Houve a oportunidade de fazer um projeto simples no papel de design: criar 5 painéis pra um stand de uma feira comercial. Cliente de logística, identidade visual definida, requisitos técnicos claros.

Meus conhecimento eram apenas de Figma e o básico.
Experiência com projetos de impressão: zero. 
Vontade de fazer design: também zero.

Mas recusar seria fácil demais. E no fácil não se aprende nada.

## Parte 1: Contas que ninguém te ensina em design

O cliente passou as dimensões em milímetros. Faz sentido — é impressão física. O Figma trabalha em pixels. Primeira tarefa antes de abrir qualquer ferramenta seria converter.

A fórmula é direta:

```
(milímetros ÷ 25.4) × PPI = pixels
```

O 25.4 é a quantidade de milímetros numa polegada. PPI é a resolução alvo — no caso, 300.

Aplicando no painel principal de 2890mm de largura:

```
(2890 ÷ 25.4) × 300 = 34.133 pixels
```

34 mil pixels de largura. Uma tela 4K tem 3840. Esse painel tinha quase 9 vezes a largura de um monitor 4K.

Cada painel grande resultou em cerca de 900 milhões de pixels totais. Tenha em mente esse número porque voltará para assombrar mais tarde.

A lição aqui é banal mas ignorada com frequência até descobrir que as proporções estavam erradas depois de horas de trabalho custa muito mais do que 10 minutos de cálculo.

---

## Parte 2: o feedback genérico e o que fazer com ele

A primeira versão seguiu o briefing à risca. Painéis limpos, identidade visual aplicada, conteúdo correto.

O retorno do cliente: "está muito simples."

Feedback genérico é chato e alguns diriam que é um problema de especificação. "Simples" pode ser raso demais, falta de elementos visuais, aparência amadora — ou outra coisa completamente diferente. 

A abordagem foi pesquisar o padrão do setor. Stands de empresas de logística em feiras corporativas têm um vocabulário visual consistente: ícones de modal de transporte, mapas-múndi, conexões globais, dados e estatísticas. O que a Versão 1 não tinha.

A Versão 2 chegou já aplicando esse padrão: ícones ao lado de cada serviço, cards com hierarquia visual mais clara, mapa-múndi no fundo do painel principal, QR code e contato no banner. Nessa versão tornei mais funcional.

Mas aí que veio o problema real.

---

## Parte 3: a premissa errada

Depois da Versão 2 aprovada, o cliente apontou um problema no Painel #03: as fotos de navios, aviões e caminhões não representavam o negócio deles.

A empresa é _freight forwarder_ — despachante, agente de carga. Eles fazem a burocracia: documentação, desembaraço alfandegário, coordenação logística. Não operam os veículos.

Mostrar fotos de transporte estava posicionando a empresa como o que ela não é.

A solução foi refazer o painel com um mapa-múndi estilizado e conexões globais — Brasil no centro, linhas chegando nos principais hubs internacionais. Visual que comunica alcance e expertise em processos, não operação de frota.

Foram criadas duas versões: fundo branco e fundo verde.

A pergunta que teria evitado esse retrabalho: "vocês operam os veículos ou só coordenam a logística?" Duas frases no início do projeto economizam horas no meio. Se tivesse entendido melhor o negócio antes de criar os conceito visual

---

## Parte 4: o inferno técnico do CMYK no Linux

Aqui fica interessante.

Impressão usa CMYK. Telas usam RGB. São color spaces diferentes — CMYK é subtrativo (tinta), RGB é aditivo (luz), e algumas cores que existem em RGB simplesmente não existem em tinta. O Figma exporta só RGB. A conversão precisa acontecer depois.

A solução óbvia seria Adobe. Photoshop faz isso com três cliques. Mas o ambiente de trabalho é Arch Linux, sem licença Adobe.

A solução real é o ImageMagick:
```bash
convert input.png -colorspace CMYK -density 300 output.png
```

Simples. Exceto que não funcionou.

Nos painéis grandes, o processo morreu:
```
341697 Morto    magick "$file" -colorspace CMYK -density 300 "$output"
```

O motivo volta aos 900 milhões de pixels. CMYK usa 4 canais de cor contra 3 do RGB. A conversão na memória de um único painel:

```
34.133 × 26.397 pixels × 4 canais × 8 bits ≈ 3,6 GB
```

O sistema matou o processo por falta de RAM. O PNG é lossless — guarda tudo sem compressão, o que significa arquivo enorme e consumo proporcional de memória.

A solução foi trocar PNG por JPG com quality 95. Em impressão de grande formato, a diferença visual entre lossless e 95% de qualidade é indistinguível. O ganho em memória é drástico.

```bash
magick input.png \
    -colorspace CMYK \
    -density 300 \
    -units PixelsPerInch \
    -quality 95 \
    output.jpg
```

Funcionou. Verificação:

```bash
identify -format "Colorspace: %[colorspace] | Resolution: %xx%y %U\n" output.jpg
# Colorspace: CMYK | Resolution: 300x300 PixelsPerInch
```

Arquivos prontos pra impressão, sem Adobe, sem Windows, sem pagar nada além do tempo.

---

## Parte 5: documentação é parte do produto

Entregar só os arquivos finais seria incompleto.

Tive que criar um Style Guide documentando paleta de cores com códigos hex exatos, tipografia por painel, especificações técnicas, rationale de cada decisão de design, instruções de exportação e histórico de versões.

A estrutura de entrega:
```
Projeto_Exhibition_Booth_FINAL/
├── 01_Design_Files_CMYK/
│   ├── Painel_01_CMYK_300ppi.jpg
│   ├── Painel_02_CMYK_300ppi.jpg
│   ├── Painel_03_WHITE_CMYK_300ppi.jpg
│   ├── Painel_03_GREEN_CMYK_300ppi.jpg
│   ├── Painel_04_CMYK_300ppi.jpg
│   └── Painel_05_CMYK_300ppi.jpg
├── 02_Documentation/
│   └── Style_Guide_v2_FINAL.md
└── README_DELIVERY.txt
```

---

## O que fica

2 horas/dia distribuídas em 14 dias. Três versões. Divergências criativas com o cliente. Um crash pesado de memória.

E no final: 6 arquivos CMYK prontos pra impressão, cliente satisfeito, projeto entregue.

Não é necessário gostar de design pra fazer design bem feito. O que é necessário é tratar o problema como qualquer outro problema técnico: entender os requisitos, quebrar em partes menores, iterar com base em feedback, documentar o processo.

A zona de conforto é um lugar confortável pra ficar parado.

---

{{< tags >}}
