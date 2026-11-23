---
title: "API Composition para leituras distribuídas"
description: "Como montar uma visão usando APIs de serviços sem fazer join nas tabelas privadas de outro serviço."
date: 2026-11-22
lang: pt-BR
locale: pt
layout: post-localized
translation-id: api-composition
link: /pt/posts/composicao-de-api/
alternate: /en/posts/api-composition/
tags: microsservicos, arquitetura, api
author: victor
---

Uma tela de detalhes pode precisar de dados do pedido, em Pedidos, e do cliente, em Clientes. Em uma arquitetura de microsserviços, a tela não deve fazer join direto nas tabelas privadas dos dois serviços.

## Faça a composição na fronteira da API

Um componente de API Composition chama as APIs donas dos dados e monta uma resposta para o cliente:

```text
Cliente -> GET /resumo-pedido/123 -> Composer
                                  |- API de Pedidos
                                  '- API de Clientes
Cliente <- status do pedido e nome do cliente
```

O composer pode ficar em um API Gateway, em um serviço específico da tela ou em outro componente com responsabilidade clara. Cada serviço continua dono dos seus dados e significados.

## Composição é para leitura

O composer combina respostas. Ele não transforma atualizações em vários serviços em uma transação de banco e não substitui uma saga para coordenar escritas.

Chamadas paralelas podem reduzir latência, mas cada dependência precisa de timeout e política de falha. Decida se a tela pode mostrar dados parciais, usar cache ou retornar erro quando um serviço estiver indisponível. Defina também a autorização de cada chamada; composição não deve contornar o serviço dono do dado.

API Composition é um modelo de leitura prático para dados distribuídos. O custo é a latência explícita da rede e o tratamento de falhas parciais na fronteira.
