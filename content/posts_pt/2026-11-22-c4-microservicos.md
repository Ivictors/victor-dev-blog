---
title: "Diagramas C4 para explicar microsserviços"
description: "Como escolher o nível certo de detalhe arquitetural para diferentes públicos e decisões."
date: 2026-11-22
lang: pt-BR
locale: pt
layout: post-localized
translation-id: c4-microservices
link: /pt/posts/c4-microservicos/
alternate: /en/posts/c4-microservices/
tags: microsservicos, arquitetura, c4
author: victor
---

Um diagrama de arquitetura pode estar tecnicamente correto e ainda falhar em explicar uma decisão. O modelo C4 ajuda usando quatro níveis de aproximação: Contexto, Contêineres, Componentes e Código.

## Contexto

Mostre pessoas, sistema e sistemas externos que interagem com ele. Essa visão deve ser compreensível para alguém que não precisa conhecer a tecnologia usada.

## Contêineres

Mostre aplicações executáveis e armazenamentos que formam o sistema, suas responsabilidades e como se comunicam. Em C4, “contêiner” não significa Docker. Uma aplicação web, uma API, um banco e um microsserviço podem aparecer nesse nível.

## Componentes

Aproxime-se de um contêiner, como Pedidos, e mostre seus blocos lógicos relevantes: API, serviço de aplicação e repositório. Um componente não é automaticamente outro serviço implantado de forma independente.

## Código

Mostre classes, funções ou detalhes de implementação quando eles responderem a uma pergunta concreta. Esse nível muda mais rápido e não deve ser desenhado apenas para preencher o diagrama.

Comece pela pergunta e escolha o menor nível de zoom útil. Nomeie responsabilidades e setas, mantenha os diagramas relacionados coerentes e atualize-os quando a arquitetura mudar. C4 é uma ferramenta de comunicação, não uma exigência de desenhar todos os níveis para todos os sistemas.

**Fonte:** [Diagramas do modelo C4](https://c4model.com/diagrams).

Todo diagrama deve declarar seu tipo e escopo, nomear responsabilidades e rotular relações com direção e protocolo quando necessário. Diagramas de contexto e contêineres costumam durar mais; componentes e código mudam mais rápido. Use apenas os níveis que respondem a uma pergunta real.
