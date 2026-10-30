---
title: "Um travamento no Docker Desktop e uma correção no cliente do Floci"
description: "Como a validação de conexões ociosas por named pipe travava um teste do Floci no Windows e o que mudou no PR #5400."
date: 2026-10-29
lang: pt-BR
locale: pt
layout: post-localized
translation-id: floci-docker-named-pipe
link: /pt/posts/travamento-docker-named-pipe-floci/
alternate: /en/posts/floci-docker-named-pipe-hang/
tags: java, open-source
author: victor
---

## O problema que apareceu no teste

Ao validar uma contribuição anterior no Floci, encontrei um travamento no Windows com Docker Desktop. O teste `EcsImagePullDockerIntegrationTest` parecia parado no download da imagem `registry:2`. Um *thread dump* mostrou outra coisa: a execução ainda estava no ping ao Docker, antes de iniciar o pull. O cliente HTTP tentava verificar se uma conexão ociosa do pool continuava válida e ficava bloqueado na leitura do *named pipe* do Docker.

O caso foi registrado na [issue #5394](https://github.com/floci-io/floci/issues/5394). Ela descreve como reproduzir o problema com `EcsImagePullDockerIntegrationTest` em Windows, Docker Desktop e `npipe:////./pipe/docker_engine`. A falha dependia do tempo de ociosidade e do reaproveitamento da conexão; por isso, podia não acontecer em toda execução.

## Por que o timeout não resolveu

O `FlociDockerHttpClient` usa um pool de conexões. Antes de reaproveitar uma conexão ociosa por mais de um segundo, o Apache HttpClient faz uma leitura curta para detectar conexões encerradas. Nesse transporte, o `NamedPipeSocket` usado pelo cliente Docker podia bloquear mesmo com o timeout de leitura curto configurado para a validação. Assim, o teste não chegava à operação Docker que parecia ser a causa.

Isso importa além do teste: a validação acontece no cliente HTTP usado para conversar com o Docker. O teste de ECS expôs o comportamento, mas a mudança está no código principal do cliente.

## A mudança no PR

No [PR #5400](https://github.com/floci-io/floci/pull/5400/files), a configuração passou a considerar o esquema de `dockerHost`. Para `npipe`, ela desativa a validação de conexão ociosa. Para `tcp` e `unix`, mantém o limite de um segundo. A mudança é específica ao transporte que apresentou o bloqueio; a evicção de conexões ociosas e os timeouts das requisições não foram alterados pelo PR.

O teste unitário verifica as três escolhas de configuração: `npipe` desativado, `tcp` e `unix` com a validação mantida. Segundo a descrição do PR, também passaram a execução direcionada de `EcsImagePullDockerIntegrationTest` e o Checkstyle dos dois arquivos alterados. A suíte completa não foi executada nessa validação, então esses resultados sustentam a correção no cenário investigado, sem provar ausência de outros problemas.

## O que aprendi

Um timeout no cliente não cobre necessariamente todos os caminhos de I/O do transporte subjacente. Quando uma operação parece travar, identificar em qual chamada ela realmente parou evita corrigir o componente errado. Aqui, o diagnóstico separou o pull da imagem da validação de uma conexão anterior e permitiu limitar a correção ao `npipe`.

**Código e contexto:** [issue #5394](https://github.com/floci-io/floci/issues/5394) · [PR #5400](https://github.com/floci-io/floci/pull/5400).
