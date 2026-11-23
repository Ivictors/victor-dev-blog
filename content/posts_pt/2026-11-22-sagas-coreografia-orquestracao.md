---
title: "Sagas: coreografia, orquestração e compensação"
description: "Como microsserviços coordenam uma operação de negócio em várias etapas quando uma transação não pode atravessar todos os serviços."
date: 2026-11-22
lang: pt-BR
locale: pt
layout: post-localized
translation-id: sagas-choreography-orchestration
link: /pt/posts/sagas-coreografia-orquestracao/
alternate: /en/posts/sagas-choreography-orchestration/
tags: microsservicos, arquitetura, kafka
author: victor
---

Um pedido pode precisar que pedidos, estoque e pagamentos concluam transações locais separadas. Quando cada serviço controla seus dados, uma única transação de banco não cobre todos eles com segurança.

## A coreografia reage a eventos

Cada serviço publica o que aconteceu e os demais decidem se devem reagir:

```text
Pedidos -> PedidoCriado -> Estoque
Estoque -> EstoqueReservado -> Pagamentos
Pagamentos -> PagamentoAprovado -> Pedidos
```

Não existe um coordenador central. A coreografia pode ser simples em fluxos curtos, mas o processo completo fica distribuído entre handlers de eventos. Conforme crescem as etapas e exceções, fica mais difícil responder quem é responsável pela próxima ação.

## A orquestração segue um plano explícito

Um orquestrador acompanha o estado da saga e envia comandos aos participantes. Isso pode facilitar a inspeção e a mudança do fluxo, mas o orquestrador precisa persistir estado, tratar timeouts e evitar assumir regras que pertencem aos serviços.

## Compensação é uma nova ação de negócio

Se o estoque foi reservado e o pagamento falhou, o sistema pode precisar liberar a reserva. Isso é uma ação de compensação, não uma viagem no tempo: enviar um e-mail ou despachar um pacote pode não ser reversível. Cada serviço define o que a compensação significa para seu estado.

Use coreografia quando o fluxo e as responsabilidades continuarem fáceis de entender. Use orquestração quando um processo explícito, visibilidade e tratamento de falhas forem mais valiosos que um fluxo totalmente descentralizado. Nos dois casos, defina idempotência, timeouts, retries e estado observável antes de prometer consistência.
