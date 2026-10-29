---
title: "Como corrigi uma race condition no Quarkus Roq"
description: "Um estudo de caso sobre operações assíncronas no editor do Quarkus Roq."
date: 2026-10-28
tags: java, quarkus, open-source, concorrencia
author: victor
---

# Como corrigi uma race condition no Quarkus Roq

## Contexto

Durante uma contribuição ao Quarkus Roq, trabalhei
em uma correção relacionada às operações assíncronas
de criação e salvamento no editor.

## O problema

O fluxo de atualização da interface podia ocorrer
antes da finalização de determinadas operações
assíncronas.

## Investigação

Nesta seção, vou explicar:
- Como o problema foi identificado.
- Quais arquivos e métodos estavam envolvidos.
- Como investiguei a sequência de execução.
- Quais alternativas considerei.

## Solução

Descreverei as alterações implementadas e
como elas modificaram o comportamento do editor.

## Lições aprendidas

Concorrência não é um problema exclusivo do backend.
Operações assíncronas também exigem cuidados com
estado e sincronização nas interfaces.

## Código-fonte

[Pull Request #1286](https://github.com/quarkiverse/quarkus-roq/pull/1286)