---
title: "Observabilidade em microserviços: acompanhe a requisição"
description: "Como métricas, logs e traces trabalham juntos quando uma operação de negócio atravessa vários serviços."
date: 2026-11-22
lang: pt-BR
locale: pt
layout: post-localized
translation-id: microservices-observability
link: /pt/posts/observabilidade-microservicos/
alternate: /en/posts/microservices-observability/
tags: microsservicos, arquitetura, observabilidade
author: victor
---

Em uma compra distribuída, um erro na API pode ter começado em Pedidos, Estoque ou Pagamentos. Uma única mensagem de erro não informa qual dependência ficou lenta ou qual decisão falhou.

## Três sinais respondem perguntas diferentes

- **Métricas** mostram comportamento agregado ao longo do tempo, como taxa de erros e latência.
- **Logs** registram eventos específicos, como uma cobrança recusada.
- **Traces** conectam os spans de uma requisição entre serviços.

O OpenTelemetry oferece APIs, SDKs e coletores para instrumentar e exportar telemetria. Ele não é o banco nem a interface em que a equipe consulta todos os sinais.

## Transporte o contexto entre fronteiras

O contexto da trace precisa acompanhar chamadas HTTP e mensagens. Um `traceId` estável conecta spans e ajuda a localizar logs relacionados. Registre serviço, operação, resultado, duração e os identificadores necessários para investigar.

Não coloque senhas, tokens ou dados pessoais desnecessários em logs e traces. Evite usar IDs de alta cardinalidade, como cada ID de pedido, como dimensões de métricas; isso pode criar muitas séries e encarecer a coleta.

## Comece por uma pergunta operacional

Instrumentação é útil quando responde a uma pergunta: “qual etapa aumentou a latência do checkout?” ou “as falhas de pagamento estão isoladas em um provedor?”. Defina sinal, amostragem, retenção e limite de alerta em torno dessa pergunta.

Observabilidade não impede falhas. Ela reduz o caminho entre um sintoma e uma explicação baseada em evidências, algo essencial quando uma operação atravessa serviços implantados de forma independente.
