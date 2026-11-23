---
title: "Limites de microserviços e migração com Strangler Fig"
description: "Como capacidades de negócio orientam limites de serviços e como migrar um monólito de forma incremental."
date: 2026-11-22
lang: pt-BR
locale: pt
layout: post-localized
translation-id: microservices-boundaries-strangler
link: /pt/posts/limites-microservicos-strangler/
alternate: /en/posts/microservices-boundaries-strangler/
tags: microsservicos, arquitetura
author: victor
---

Dividir um monólito não consiste principalmente em criar mais unidades implantáveis. A decisão difícil é escolher limites que mantenham as regras de negócio juntas e reduzam mudanças coordenadas.

## Comece pelas capacidades de negócio

Use capacidades como pedidos, estoque, pagamentos e entregas como candidatas. Um serviço deve controlar as regras e os dados que mudam pelos mesmos motivos. Isso é uma hipótese inicial, não um mapeamento automático de cada área ou tabela para um serviço.

Bons limites reduzem chamadas que precisam mudar juntas. Também deixam a responsabilidade mais clara: a equipe de pedidos pode evoluir o ciclo de vida do pedido sem expor suas tabelas a todas as outras equipes.

## Migre com Strangler Fig

O padrão Strangler Fig encaminha cada capacidade para o sistema legado ou para um serviço novo enquanto os dois coexistem:

```text
Cliente -> roteador -> serviço novo de Catálogo
                   -> módulo legado de Pedidos
                   -> módulo legado de Pagamentos
```

Escolha uma capacidade com limite compreensível e retorno útil. Encaminhe apenas essa capacidade para a nova implementação, compare o comportamento e migre dados e integrações com cuidado. Repita quando a primeira parte estiver estável.

## Cuidado com dados compartilhados

Se o monólito e o serviço novo escrevem nas mesmas tabelas sem uma regra de propriedade, a migração cria duas fontes de verdade. Prefira um contrato de transição, um único dono para cada alteração e eventos ou APIs para os demais consumidores.

Uma camada anticorrupção pode traduzir termos do legado para o domínio do novo serviço. Isso impede que o modelo antigo se espalhe pelo código novo durante a convivência dos sistemas.

Microsserviços fazem sentido quando seus limites melhoram a evolução independente. O Strangler Fig torna a decisão reversível em etapas pequenas, mas não elimina o custo da operação distribuída.

## Como saber se o limite funciona

Observe se há menos deploys coordenados, propriedade clara dos dados e contratos que evoluem de forma independente. Uma cadeia de chamadas síncronas por quase todos os serviços ou acessos frequentes às tabelas de outro serviço indicam que o limite precisa ser revisado.

**Fontes:** [Martin Fowler — Microservices](https://martinfowler.com/articles/microservices.html) e [Strangler Fig Application](https://martinfowler.com/bliki/StranglerFigApplication.html).
