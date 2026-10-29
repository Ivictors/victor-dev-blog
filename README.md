# Victor Dev Blog

Este repositório contém o código e os artigos do meu blog técnico, construído com [Quarkus Roq](https://iamroq.dev). Sou Victor Marques, desenvolvedor backend Java e colaborador open source. Uso este espaço para documentar problemas que investiguei, as decisões por trás das correções e o que aprendi ao contribuir com projetos de software.

## Sobre o blog

Os artigos partem de casos verificáveis: issues, pull requests e código-fonte. O primeiro texto publicado explica uma race condition no editor do Quarkus Roq e a correção enviada no [PR #1286](https://github.com/quarkiverse/quarkus-roq/pull/1286). Os temas do blog incluem Java, Quarkus, concorrência, engenharia de software e desenvolvimento open source.

## Recursos do projeto

- **Português e inglês:** páginas e artigos completos em `/pt/` e `/en/`, com seletor de idioma e traduções da interface em `data/i18n/`.
- **Navegação por assuntos:** páginas de tags para cada idioma, geradas pelo plugin de tagging do Roq.
- **Descoberta do conteúdo:** feeds RSS por idioma, sitemap XML, mapas HTML, URLs canônicas e links `hreflang`.
- **Links antigos preservados:** o plugin de aliases redireciona as URLs anteriores para o conteúdo em português.
- **Publicação por GitHub Actions:** mudanças mescladas em `main` acionam a geração e o deploy para GitHub Pages.

## Desenvolvimento local

Instale Java 21 e o [Roq CLI](https://iamroq.dev/docs/getting-started/). Na raiz do repositório, execute:

```powershell
roq start     # prévia com atualização automática em http://localhost:8080
roq generate  # gera o site em target/roq
roq serve     # serve a versão gerada em http://localhost:8181
```

Execute `roq serve` após uma geração bem-sucedida. Se `roq generate` falhar, procure a primeira mensagem de erro antes de `BUILD FAILURE`. Um aviso isolado sobre `beans.xml` do tema não impede a geração.

## Criando um artigo

1. Crie uma versão em português em `content/posts_pt/` e outra em inglês em `content/posts_en/`.
2. Em cada frontmatter, defina `title`, `description`, `date`, `tags`, `author: victor`, `layout: post-localized`, `locale` e `lang`. Use o mesmo `translation-id` nas duas versões e faça `alternate` apontar para a URL da tradução correspondente. Se precisar manter uma URL fixa, defina `link`.
3. Ao criar tags, atualize `tagAlternates` em `data/i18n/pt.yaml` e `data/i18n/en.yaml` para que o seletor abra a página do mesmo assunto no outro idioma.
4. Revise as afirmações e os links, execute `roq generate`, confira as duas versões com `roq serve` e preencha o [checklist de publicação](.github/PULL_REQUEST_TEMPLATE.md) no pull request.

O [artigo sobre race conditions em português](content/posts_pt/2026-10-28-race-condition-quarkus-roq/contribution-race-condition-quarkus-roq.md) e sua [tradução inglesa](content/posts_en/2026-10-28-race-condition-quarkus-roq.md) são exemplos de frontmatter e estrutura.

## Publicação

O [workflow de deploy](.github/workflows/deploy.yml) roda após um push em `main`, diariamente para conteúdo agendado e também manualmente. Em **Settings → Pages → Build and deployment**, a fonte deve ser **GitHub Actions**. Depois de uma execução bem-sucedida, o endereço do blog é [ivictors.github.io/victor-dev-blog](https://ivictors.github.io/victor-dev-blog/).

O computador local não precisa ficar ligado: o GitHub Pages serve os arquivos estáticos produzidos pelo workflow. Confira o resultado em **Actions** e abra `/pt/`, `/en/` e o artigo antes de compartilhar os links.

## Estrutura

| Caminho | Conteúdo |
|---|---|
| `content/pt/` e `content/en/` | Páginas de cada idioma |
| `content/posts_pt/` e `content/posts_en/` | Artigos completos |
| `data/i18n/` | Textos da interface, menus e correspondência entre tags |
| `templates/` | Layouts e componentes adaptados para os dois idiomas |
| `web/` e `public/` | Estilos e imagens |

`data/menu.yml` contém somente uma lista vazia porque o tema padrão ainda exige esse bean durante o build; os menus visíveis vêm de `data/i18n/`.

## Contato

- [GitHub](https://github.com/Ivictors)
- [LinkedIn](https://www.linkedin.com/in/vi-marques/)
