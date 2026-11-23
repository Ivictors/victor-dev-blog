---
title: "Transactional Outbox sem promessa de exactly-once"
description: "Como manter uma alteração no banco e a publicação de um evento conectadas em um microserviço."
date: 2026-11-22
lang: pt-BR
locale: pt
layout: post-localized
translation-id: transactional-outbox
link: /pt/posts/transactional-outbox/
alternate: /en/posts/transactional-outbox/
tags: microsservicos, arquitetura, kafka
author: victor
---

Um serviço geralmente precisa alterar seu banco e publicar um evento. Duas operações independentes criam uma lacuna: o processo pode parar depois de uma concluir e antes de a outra acontecer.

## Coloque os dois registros na mesma transação local

O Transactional Outbox grava a alteração de negócio e a mensagem pendente na mesma transação do banco:

```text
Transação local
  1. Grava o pedido.
  2. Grava PedidoCriado na outbox.
  3. Confirma os dois registros.

Relay
  4. Lê as mensagens pendentes.
  5. Publica no broker.
```

Se a transação sofrer rollback, nenhum registro fica visível. Se houver commit, um relay pode publicar o evento depois. O limite atômico é o banco local; não é uma transação distribuída com o broker.

## O relay pode publicar duas vezes

O relay pode cair depois de publicar e antes de marcar a mensagem como enviada. Ao reiniciar, ele pode publicar o mesmo evento novamente. Portanto, o padrão melhora a entrega do evento, mas não cria processamento exatamente uma vez de ponta a ponta.

Dê a cada evento um `eventId` estável. Os consumidores devem reconhecer um evento já aplicado, de preferência registrando esse ID na mesma transação local da alteração de estado. Também defina limites de retry, regras de ordenação e limpeza dos registros antigos.

Consultar a tabela periodicamente e ler o log de transações do banco com CDC são estratégias comuns. Escolha de acordo com latência, complexidade operacional e recursos do banco utilizado.

O Outbox é valioso porque torna explícito o limite de falha. Sua garantia termina no commit local e precisa ser completada por consumidores idempotentes.
