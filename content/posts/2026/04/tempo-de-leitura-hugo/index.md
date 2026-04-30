---
title: "Tempo de leitura: a feature mais barata do seu blog"
description: "Tempo estimado de leitura não é enfeite — é a menor feature de UX que mais impacta a decisão do leitor. Como implementei no meu blog com Hugo e o que aprendi no caminho."
date: 2026-04-30T10:00:00-03:00
author: "Troli"
posttype: ["design"]
tags: ["ux", "hugo", "tempo-de-leitura", "blog"]
draft: false
---

{{< meta-data >}}

Tempo de leitura estimado é a feature mais barata que você pode adicionar ao seu blog e que mais muda o comportamento do leitor. Implementei no Troli's Mind — e descobri que a parte difícil se trata de escolher o número certo e não sobre o algoritmo em si.

---

## O problema de básico de UX em blogs

Você entra num blog. Vê um título interessante. Abre o post.

É um texto longo ou curto? Você não sabe. Então faz o que todo mundo faz: rola a página pra tentar estimar pelo tamanho. Ou simplesmente fecha a aba e deixa "pra depois" — o que na prática significa "nunca".

Isso é a **incerteza**. O leitor está decidindo entre "consigo fazer isso agora?" ou "isso exige um momento que eu não tenho?". Sem nenhum info, a resposta segura é não ler.

## O que as plataformas já sabem

Quem resolveu isso primeiro foram as plataformas que vivem de retenção — elas não podiam perder leitores pra incerteza. Medium, dev.to, Substack, Notion, Ghost — praticamente toda plataforma de conteúdo moderno mostra tempo de leitura.

A lógica é simples: **informação reduz incerteza, e incerteza reduz ação**. Quando o leitor vê "5 min de leitura", ele pode decidir. Essa decisão informada tem três efeitos práticos:

1. **Reduz bounce rate** — o leitor que fica sabendo que são 3 min tende a ficar, mesmo que estivesse em dúvida
2. **Aumenta retorno** — o leitor que descobre que são 15 min salva pra depois em vez de fechar e esquecer
3. **Sinaliza respeito** — mostrar o tempo é um contrato implícito: "esse texto respeita seu tempo"

Tudo isso com um algoritmo de 5 linhas com uma melhor UX do leitor.

## A matemática por trás

A fórmula é direta:

```
tempo = palavras ÷ velocidade_de_leitura
```

A pergunta que define tudo não é **"qual velocidade usar?"**. E a resposta varia por público:

| Referência         | Velocidade  | Contexto                                                      |
| ------------------ | ----------- | ------------------------------------------------------------- |
| Hugo (nativo)      | 213 pal/min | Velocidade interna usada por `.ReadingTime` (quando funciona) |
| Medium             | 238 pal/min | Artigos em inglês, leitura fluida                             |
| dev.to             | 275 pal/min | Técnico em inglês, ritmo otimista                             |
| Meu blog (prosa)   | 200 pal/min | Texto corrido em pt-BR                                        |
| Meu blog (código)  | 80 pal/min  | Blocos de código — interpretar lógica consome mais            |
| Meu blog (imagens) | 12 seg cada | Tempo pra processar o visual                                  |

Por que pesos diferentes? Porque ler prosa é escorrer os olhos. Interpretar código exige ler, voltar, comparar com o contexto, reler. Uma imagem exige olhar, processar, conectar com o texto. São ritmos cognitivos diferentes — e um algoritmo honesto precisa respeitar isso.

Testei os valores lendo meus próprios posts cronometrando. Um snippet com 3 linhas de YAML demora mais pra digerir do que 3 linhas de prosa. Um post com diagrama exige mais tempo do que o mesmo post sem.

## Como implementei no blog

A implementação parece trivial. O Hugo tem `{{ .ReadingTime }}` nativo — uma variável, pronto. Exceto que não funciona dentro de shortcodes. Retorna 0. Sempre.

Isso é uma limitação documentada do Hugo: o cálculo de `WordCount` e `ReadingTime` acontece depois da renderização de shortcodes. Então se você quer mostrar o tempo dentro de um componente (o que é o caso natural), a variável nativa te abandona.

A saída é contornar o Hugo e fazer o cálculo na mão — com as palavras brutas do markdown, antes dele processar qualquer coisa. A solução foi processar o `.Page.RawContent` linha a linha, separando tipos de conteúdo e aplicando pesos diferentes pra cada um:

```text
# pseudocódigo — a implementação real está no partial
pra cada linha do markdown:
  se dentro de bloco de código → conta como código
  senão → conta como prosa
  se a linha tem imagem → marca como imagem

tempo = (prosa ÷ vel_prosa) + (código ÷ vel_código) + (imagens × tempo_por_imagem)
```

A terceira linha é a mais interessante. No Hugo, divisão entre inteiros trunca — `561 / 100` vira `5`, não `5.61`. Pra arredondar pra cima (como `ceil()`), usei um truque de matemática inteira:

```
ceil(a/b) = (a + b - 1) / b
```

Assim, 561 palavras / 100 pal/min = `(561 + 99) / 100` = `660 / 100` = **6 min** (561 + 99 = 660, que dividido por 100 dá 6 — sem truncar pra 5).

Tem também o problema de que shortcodes não podem ser aninhados dentro de outros shortcodes no Hugo. Se `meta-data` é um shortcode e `reading-time` também é, você não pode chamar `{{< reading-time >}}` dentro do HTML do `meta-data`. A solução: a lógica vive num **partial**, que pode ser chamado de qualquer lugar.

---

## O que fica

Se você usa Hugo+Hextra, saiba que `.ReadingTime` nativo pode não funcionar no seu contexto. Conte palavras de `.RawContent` se precisar. E teste o número final lendo seus próprios posts porque a velocidade de leitura real do seu público é o único benchmark que importa.

O tempo de leitura é o tipo de feature que parece tão simples que muitos parecem não se importar. Mas quando você pensa sobre, percebe que é uma das poucas coisas que beneficiam leitor e autor ao mesmo tempo: o leitor decide melhor, e o autor ganha um leitor que escolheu estar ali.

---

## Fontes

- [Desenvolvendo Tempo estimado de leitura — Lucas Galrão (Medium)](https://medium.com/@lucas.galrao0/desenvolvendo-algoritmo-para-calcular-tempo-de-leitura-em-uma-p%C3%A1gina-web-4b779c521315)
- [Hugo Page Variables — WordCount & ReadingTime](https://gohugo.io/methods/page/wordcount/)

---

{{< tags >}}
