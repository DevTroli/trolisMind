# 🧠 Troli's Mind

> **Backup de conhecimentos do meu cérebro**

Um blog pessoal que funciona como um "Grail Diary" tecnológico, combinando deep-dives técnicos com experiências pessoais, servindo como ferramenta de desenvolvimento de comunicação e portfolio vivo para a comunidade tech.

## 🛠️ Stack Técnico

- **Framework:** [Hugo](https://gohugo.io/) (gerador de sites estáticos)
- **Tema:** [Hextra](https://imfing.github.io/hextra/) (customizado)
- **Linguagem:** Go Templates + Markdown
- **Deploy:** Estático (compatível com Vercel, Netlify, GitHub Pages)
- **Arquivos:** Markdown puro para posts + front matter YAML

***

## 📊 Status do Projeto

**Versão atual:** MVP v1.0  
**Status:** ✅ Funcional e pronto para uso  
**Próximos passos:** Deploy e criação de conteúdo  

### Progresso futuro:
- [ ] Sistema de busca
- [ ] RSS feed
- [ ] Analytics
- [ ] Comentários (talvez)
- [ ] Roadmaps & Milestones


## 📁 Estrutura do Projeto

```
trolismind/
├── archetypes/
│   ├── default.md              # Modelo padrão
│   ├── post.md                 # Modelo para posts
│   ├── milestone.md            # Modelo para milestones
│   └── roadmap.md              # Modelo para roadmaps
├── assets/                    # CSS, JS e arquivos processados por Hugo Pipes
│   ├── css/
│   └── js/
├── content/
│   ├── posts/
│   ├── milestones/
│   ├── roadmaps/
│   ├── sobre/
│   │   └── index.md          # Página sobre (/sobre/)
│   └── principios/
│       └── index.md          # Princípios do blog (/principios/)
├── data/                       # Dados em yaml, json, toml para uso dinâmico
├── i18n/                       # Traduções
├── layouts/                    # Templates HTML personalizados
│   ├── posts/
│   ├── milestones/
│   ├── roadmaps/
│   └── sobre/
├── static/                     # Arquivos estáticos diretos (images, fontes, etc.)
├── hugo.toml                   # Configuração geral do Hugo
├── docker-compose.yml          # Se usar containers para dev
├── .github/workflows/          # Pipeline CI/CD
└── README.md
```

***

## 🎨 Taxonomia dos Post

O blog categoriza conteúdo em tipos editoriais específicos:

- **🔥 RANTs** - Desabafos construtivos temperados com rigor técnico
- **🏗️ DeepDives** - Decisões técnicas complexas com contexto humano
- **🎯 CareerNotes** - Lições de carreira aprendidas na prática
- **💾 Snipptes** - Snippets testados em produção com contexto
- **👥 Design** - Como decisões técnicas impactam pessoas
- **📚 Library** - Recursos curados que impactam na prática
- **🌀 Off-topic** - Conteúdo tangente mas relevante

## 🚀 Como Rodar Localmente

### Pré-requisitos
- [Hugo](https://gohugo.io/installation/) instalado
- Git

### Comandos
```bash
# Clonar o projeto
git clone https://github.com/DevTroli/trolisMind
cd trolisMind

# Rodar servidor de desenvolvimento
hugo server -D

# Acessar no navegador
# http://localhost:1313
```

### Criar novo post
```bash
# Usando archetype personalizado
hugo new posts/:ano/:mes/:slug-post/index.md

# Ou manualmente criar arquivo em:
# content/posts/2025/10/meu-novo-post/index.md
```

***

## 🔗 URLs e Navegação

- **Home:** `/`
- **Todos os posts:** `/posts/`
- **Post individual:** `/posts/2025/10/titulo-do-post/`
- **Posts por tipo:** `/posttypes/rant/`, `/posttypes/architecture/`, etc.
- **Página sobre:** `/sobre/`
- **Princípios:** `/principios/`

### Configuração de permalinks:
```toml
[permalinks]
  posts = "/posts/:year/:month/:slug/"
```

### Shortcodes disponíveis:

**Posts recentes na home:**
```markdown
{{< recent-posts count="5" >}}
```

**Lista completa de posts:**
```markdown
{{< all-posts >}}
```

**Embed YouTube:**
```markdown
{{< youtube id="dQw4w9WgXcQ" >}}
```

***

## ⚡ Features Implementadas

### ✅ Core Features
- [x] Sistema de posts com tipos editoriais
- [x] Home page dinâmica com posts recentes
- [x] Página de arquivo completo de posts
- [x] Filtros por tipo de post
- [x] Shortcodes para automatização
- [x] Páginas institucionais (Sobre, Princípios)
- [x] URLs semânticas e SEO-friendly

### ✅ UX Features
- [x] Fallback elegante para páginas sem posts
- [x] Embed responsivo de YouTube
- [x] Navegação por tipos de conteúdo
- [x] Data e tipo organizados semanticamente
- [x] Tags funcionais
- [x] Design minimalista focado em conteúdo

### ✅ DX Features
- [x] Archetypes para acelerar criação de posts
- [x] Shortcodes reutilizáveis
- [x] Estrutura de pastas organizada
- [x] Hot reload em desenvolvimento
- [x] Markdown puro para posts

***

## 🎯 Filosofia de Desenvolvimento

- **Conteúdo > Tecnologia** - A tecnologia serve o conteúdo, não o contrário
- **Markdown-first** - Posts em markdown puro, sem complexidade desnecessária
- **Automação inteligente** - Shortcodes para tarefas repetitivas, manual para controle
- **Semântica clara** - URLs, tipos e estruturas que fazem sentido
- **Performance** - Site estático, rápido e leve

***

## 🤝 Como Contribuir

Este é um projeto pessoal, mas feedbacks são bem-vindos:

1. **Issues** - Para bugs ou sugestões
2. **Pull Requests** - Para melhorias técnicas
3. **Discussões** - Para ideias e feedback geral

***

## 🔗 Contatos
- **Autor:** [@DevTroli](https://github.com/DevTroli)
- **LinkedIn:** [Pablo Troli](https://www.linkedin.com/in/pablotroli/)

---

**Construído com ❤️ e muito ☕**
