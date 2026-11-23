---
title: "C4 diagrams for explaining microservices"
description: "How to choose the right level of architectural detail for different audiences and decisions."
date: 2026-11-22
lang: en
locale: en
layout: post-localized
translation-id: c4-microservices
link: /en/posts/c4-microservices/
alternate: /pt/posts/c4-microservicos/
tags: microservices, architecture, c4
author: victor
---

An architecture diagram can be technically correct and still fail to explain a decision. The C4 model helps by using four levels of zoom: Context, Containers, Components, and Code.

## Context

Show the people, the system, and external systems that interact with it. This view should make sense to someone who does not need to know the implementation technology.

## Containers

Show the executable applications and data stores that form the system, their responsibilities, and how they communicate. In C4, “container” does not mean Docker. A web application, an API, a database, and a microservice can all appear at this level.

## Components

Zoom into one container, such as Orders, and show its relevant logical building blocks: API, application service, and repository. A component is not automatically another independently deployed service.

## Code

Show classes, functions, or other implementation details when they answer a concrete question. This level changes faster and should not be drawn just to fill a diagram.

Start with the question and choose the smallest useful zoom. Label responsibilities and arrows, keep related diagrams consistent, and update them when the architecture changes. C4 is a communication tool, not a requirement to draw every level for every system.

**Source:** [C4 model diagrams](https://c4model.com/diagrams).

Every diagram should state its type and scope, name responsibilities, and label relationships with their direction and protocol when relevant. Context and container diagrams often last longer; component and code diagrams change faster. Use only the levels that answer a real question.
