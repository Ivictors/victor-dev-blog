---
title: "Kafka: o que partições, acks e idempotência realmente garantem"
description: "Como pensar em ordem, durabilidade e duplicatas sem confundir a gravação no Kafka com o processamento da aplicação."
date: 2026-10-29
lang: pt-BR
locale: pt
layout: post-localized
translation-id: garantias-kafka
link: /pt/posts/garantias-entrega-kafka/
alternate: /en/posts/kafka-delivery-guarantees/
tags: kafka, arquitetura
author: victor
---

## O caminho de um evento

Quando estudei Kafka, comecei com uma pergunta simples: onde uma mensagem fica e como ela chega a quem precisa processá-la? O produtor grava registros em um tópico dividido em partições. Cada partição é um log ordenado, com offsets que identificam posições **dentro daquela partição**. O consumidor lê esses registros e acompanha a posição processada.

Isso já impõe um limite importante: Kafka não oferece uma ordem total entre todas as partições de um tópico. Se eventos da mesma entidade precisam manter ordem relativa, a escolha da chave e da partição importa. Uma mesma chave tende a ir para a mesma partição enquanto a estratégia de particionamento e a quantidade de partições permanecerem compatíveis; alterar a quantidade de partições pode mudar esse mapeamento para eventos futuros.

## Paralelismo nos grupos

Dentro de um grupo de consumidores, uma partição é atribuída a um consumidor por vez. Com três partições e quatro consumidores do mesmo grupo lendo apenas esse tópico, um deles fica sem partição. Outro grupo mantém seus próprios offsets e pode ler os mesmos registros para uma finalidade diferente. Isso distribui trabalho sem “duplicar a mensagem” no log a cada grupo.

Quando membros entram, saem ou deixam de responder, as atribuições podem mudar. A aplicação precisa tolerar reprocessamento durante falhas e rebalanceamentos, principalmente se o efeito externo foi concluído antes de o offset correspondente ser confirmado.

## `acks` fala sobre confirmação de gravação

`acks=0` não espera confirmação do broker. `acks=1` espera a gravação no log do líder, sem aguardar todas as réplicas sincronizadas. `acks=all` espera a confirmação das réplicas que estão no conjunto *in-sync* (ISR). Esse último é o nível mais forte de confirmação do produtor, mas a durabilidade real também depende da configuração de replicação, de `min.insync.replicas` e da política de eleição do líder. Nenhuma dessas opções diz se o consumidor concluiu seu trabalho.

Nas configurações atuais do produtor Java documentadas pelo Apache Kafka, `acks=all` é o padrão. Anotações ou exemplos de versões antigas podem mostrar outro padrão; vale conferir a versão usada no projeto.

## Idempotência não cobre todos os efeitos

Com `enable.idempotence=true`, o produtor usa identificadores e números de sequência para evitar que um reenvio da mesma produção grave duplicatas no log. Isso protege o envio no escopo do produtor. Não impede que uma aplicação reiniciada produza novamente o mesmo evento de negócio como uma nova operação, nem torna automaticamente idempotente uma cobrança feita por um consumidor.

Para processamento de Kafka para Kafka, transações podem coordenar registros produzidos e offsets consumidos, desde que os consumidores usem a configuração adequada para ler apenas registros confirmados. Ao chamar um banco, uma API de pagamento ou outro sistema externo, a aplicação ainda precisa de uma estratégia própria, como uma chave de idempotência e uma operação atômica no destino. “Exactly once” depende de onde começa e termina a operação observada.

## O que eu verificaria num projeto

Antes de prometer uma garantia, eu anotaria: chave de partição, número de partições, `acks`, replicação, `min.insync.replicas`, política de retries, momento do commit do offset e comportamento do destino diante de duplicatas. Isso transforma um rótulo como *at least once* em uma descrição testável do fluxo inteiro.

**Fontes:** [design do Apache Kafka](https://kafka.apache.org/35/design/design/) e [configuração do produtor Java](https://kafka.apache.org/42/configuration/producer-configs/). Este artigo parte das minhas notas de Full Cycle sobre tópicos, partições, produtores e consumidores.
