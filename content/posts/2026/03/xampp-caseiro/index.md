---
title: "Fiz o meu proprio Xampp (DIY)"
description: "Tentei montar meu próprio ambiente de desenvolvimento — Nginx, PostgreSQL e PHP-FPM no Arch Linux."
date: 2026-03-26T11:36:32-03:00
author: "Troli"
posttype: ["snippets"]
milestone: []
roadmap: []
tags: ["dev-web", "DIY", "linux"]
draft: false
---

{{< meta-data >}}

Normalmente o XAMPP e usado no Windows. Mas minha máquina roda Arch Linux. Tudo no Linux ainda mais no arch nao e tao simples quanto baixar e usar um .exe do xampp no windows

Além de que eu não queria simplesmente instalar o XAMPP e seguir em frente. Queria entender o que ele faz por baixo: um servidor web, um banco de dados, uma linguagem de script — e como esses três conversam.

![Arch Linux](https://servidor-estaticos-ashy.vercel.app/v2/trolismind/post7_arch.png)


A motivação não era replicar. Era compreender.

O plano tomou forma rápido: trocar o Apache pelo Nginx, o MySQL pelo PostgreSQL, e gerenciar tudo com as ferramentas que já uso no cotidiano. Um XAMPP caseiro, mas com minhas próprias configurações.

---

## Primeiro obstáculo

PostgreSQL já estava instalado. Mas o comando `psql` retornava isso:

```
FATAL: a conexão com o servidor no soquete "/run/postgresql/.s.PGSQL.5432" falhou
```

A investigação começou pelo `systemctl`:
```bash
systemctl status postgresql.service
```

O serviço falhava porque o cluster de dados nunca havia sido inicializado. O Arch Linux, diferente de um pacote como o XAMPP, entrega só as ferramentas — a responsabilidade de criar a fundação é sua.

A solução:
```bash
sudo -iu postgres
initdb
```

Banco de dados funcionando. Primeira lição aprendida: não é porque está instalado que está configurado.

---

## Nginx 🤝 PHP-FPM

Com o banco no ar, o próximo passo era o servidor web. Nginx instalado, `systemctl enable --now`, e a tela de "Welcome to nginx!" confirmou o funcionamento.

![welcome Nginx](https://servidor-estaticos-ashy.vercel.app/v2/trolismind/post7_welcomeNgix.jpeg)

Mas servir arquivos estáticos é trivial. A meta era fazer o Nginx conversar com o PHP. A solução é a dobradinha Nginx + PHP-FPM: o Nginx recebe a requisição e passa pro PHP-FPM executar.

O problema apareceu na configuração. O arquivo `/etc/nginx/nginx.conf` tem uma estrutura hierárquica rígida — colocar um bloco `location` no lugar errado gera erros como:

```
location directive is not allowed here
```

A solução foi adotar uma prática profissional desde o início: isolar a configuração do site em arquivo próprio dentro de `/etc/nginx/conf.d/`. Separação de responsabilidades, mesmo em ambiente local.

---

## ERROR 404 & 502

Com a configuração no lugar, o browser respondeu. Não com a página esperada — com um 404.

O servidor funcionava. Ele simplesmente não encontrava o arquivo. O motivo: o Nginx roda como usuário `http` e não tinha permissão pra acessar o diretório pessoal `/home/troli`. Um conceito fundamental do Linux que havia passado batido.

A correção foi um ajuste sutil:

```bash
chmod 711 /home/troli
```

Isso permite ao servidor "atravessar" o diretório sem poder ler seu conteúdo.

O 404 virou 502. Progresso, mas ainda erro. Agora o Nginx encontrava o arquivo mas falhava ao entregá-lo ao PHP-FPM — desalinhamento entre a configuração de `root` e o `fastcgi_param`. Simplificar a configuração e apontar a raiz diretamente pra pasta do `index.php` resolveu.

![phpInfo()](https://servidor-estaticos-ashy.vercel.app/v2/trolismind/post7_phpinfo.png)

A tela branca foi substituída pelo `phpinfo()`.

---

## A conexão final

O último passo foi um script de teste conectando os três componentes:

```php
try {
    $pdo = new PDO('pgsql:host=localhost;dbname=postgres', 'postgres', '');
    echo "Conexão com o PostgreSQL bem-sucedida!";
} catch (PDOException $e) {
    echo "Erro: " . $e->getMessage();
}
```

![pqsl](https://servidor-estaticos-ashy.vercel.app/v2/trolismind/post7_psql.png)

Funcionou! 

---

## O que ficou de aprendizado

Minha vontade de replicar o ambiente da Fatec se tornou sobre administração de Linux. Cada erro foi uma camada a mais de entendimento de permissões, hierarquia de configuração, inicialização de serviços.

Hoje, meu “XAMPP caseiro” não é só mais leve ou mais rápido, mas que tudo pra mim ele super compreensível.

Se algo quebra, eu sei onde olhar.
Se preciso mudar algo, eu sei por quê e como.

Nao se para os outros mais para mim isso vale mais do que qualquer programa pronto.

---

{{< tags >}}
