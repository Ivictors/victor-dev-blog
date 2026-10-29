---
title: "Como corrigi uma race condition no Quarkus Roq"
lang: pt-BR
locale: pt
layout: post-localized
translation-id: roq-editor-race-condition
alternate: /en/posts/how-i-fixed-a-race-condition-in-quarkus-roq/
aliases: [/posts/como-corrigi-uma-race-condition-no-quarkus-roq/]
description: "Por que o editor ficava preso em Please wait após criar ou salvar uma página, e como corrigi a sequência assíncrona."
date: 2026-10-28
tags: java, quarkus, open-source, concorrencia
author: victor
---

## Contexto

O editor do Quarkus Roq às vezes ficava preso na mensagem **“Please wait”** depois de criar ou salvar uma página.
O arquivo já existia no disco, mas a interface não terminava de atualizar. Recarregar a aba com F5 resolvia o sintoma.
A falha era intermitente: dependia da ordem em que uma escrita assíncrona e a reconexão do navegador terminavam.


## O problema

No arquivo `roq-editor/deployment/src/main/resources/dev-ui/qwc-roq-editor.js`, as operações `createPage` e `savePageContent` chamam `_writeCall`. A gravação acontece em segundo plano, e `_writeCall` consulta periodicamente o estado até receber a conclusão. Criar ou salvar o arquivo também provoca um hot reload, que desconecta e reconecta o navegador.

Antes da correção, a criação só marcava `_pendingRefreshPages` **depois** que `_writeCall` terminava. A reconexão, por sua vez, só chamava `_refreshPageInfo()` se essa marca já estivesse ativa. Quando a reconexão chegava primeiro, ela passava sem atualizar a página. Depois, a marca era ativada e o overlay aparecia, mas não restava outra reconexão para disparar o refresh que o fecharia.

## Investigação

Em termos de sequência:

1. O editor inicia a escrita; o hot reload começa.
2. O navegador reconecta e verifica `_pendingRefreshPages`, ainda desativado.
3. `_writeCall` termina e ativa a marca de atualização.
4. O overlay continua aberto porque o evento de reconexão já passou.

A [descrição da issue](https://github.com/quarkiverse/quarkus-roq/issues/967) registra que `_writeCall` consultava `writeStatus()` a cada 500 ms. Essa espera ajudava a criar a janela em que a reconexão podia ocorrer antes da mudança de estado.
Não era uma falha de gravação do arquivo; era uma falha na coordenação dos eventos da interface.

## Solução

No [diff do PR #1286](https://github.com/quarkiverse/quarkus-roq/pull/1286/files), passei a marcar `_pendingRefreshPages` e `_writeInProgress` **antes** de iniciar `createPage` ou `savePageContent`. Enquanto a escrita está em andamento, a reconexão adia `_refreshPageInfo()`. Quando a operação termina, o bloco `finally` limpa `_writeInProgress` e, se há atualização pendente e a conexão está ativa, inicia o refresh.

Também limpei `_pendingRefreshPages` quando a escrita ou o próprio refresh falha. Isso evita deixar o editor com uma atualização pendente sem uma operação capaz de concluí-la. O código mantém o ciclo do overlay ligado ao resultado da atualização, em vez de depender de uma única ordem entre a escrita e a reconexão.

## Lições aprendidas

Uma operação assíncrona pode terminar corretamente e ainda assim deixar a interface em um estado incorreto. Neste caso, o ponto crítico era o momento em que a intenção de atualizar a página ficava visível para o evento de reconexão. Marcar essa intenção antes da escrita, adiar o refresh enquanto ela acontece e limpar o estado em caso de erro tornou a sequência explícita.

## Código-fonte

O relato completo da reprodução está na [issue #967](https://github.com/quarkiverse/quarkus-roq/issues/967); a alteração integrada está no [PR #1286](https://github.com/quarkiverse/quarkus-roq/pull/1286).
