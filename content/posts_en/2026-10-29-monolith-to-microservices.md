---
title: "From monolith to microservices: start with boundaries"
description: "A practical look at domain, data, and operational decisions before extracting a service."
date: 2026-10-29
lang: en
locale: en
layout: post-localized
translation-id: migracao-monolito-microsservicos
link: /en/posts/monolith-to-microservices-boundaries/
alternate: /pt/posts/migracao-monolito-microsservicos/
tags: architecture, microservices
author: victor
---

## First, what problem are we solving?

My architecture notes say “separate contexts,” “avoid nanoservices,” and “start at the edges.” Those reminders point to a larger decision: extracting services makes sense when separation serves a concrete need, such as independent release cycles, clearer ownership, or different scaling requirements.

A monolith can have well-defined modules, tests, and reliable deployments. A single process is not automatically a problem. Microservices add network calls, distributed observability, deployment automation, and consistency decisions. If those needs have not appeared, improving boundaries inside the monolith may be the more useful step.

## Find a business boundary

Before splitting code, identify which rules and data change together. In DDD, a *bounded context* marks where a particular domain model applies. It helps discuss boundaries; it does not require one microservice per context.

Consider a store with catalog, orders, and payments. If the catalog can evolve without coordinating every change with payments, there may be a useful boundary. But if the new service still queries the monolith's internal tables on every request, we have moved coupling onto the network and created a distributed monolith.

## Extract gradually

The **Strangler Fig** pattern describes modernizing a system piece by piece while old and new code coexist. Pick a capability with a recognizable boundary, decide how calls will be routed, and preserve a way to roll back. Starting at an edge with fewer dependencies reduces the number of decisions made at once.

I would ask:

1. What outcome do we expect from the extraction, and how will we observe it?
2. Who will own this capability's rules and data?
3. Which synchronous calls remain, and what happens if they fail?
4. How will reads and writes move without creating two sources of truth?
5. How will we test, deploy, and monitor both sides during the transition?

## Treat data as part of the architecture

Separate data ownership can improve autonomy, but data migration is often the hard part. A transitional solution may need synchronization or read projections. Duplicate data is not necessarily an error; the risk is failing to name the source of truth and the acceptable propagation delay. Events can carry changes, but they also bring reprocessing, failure handling, and eventual consistency into the design.

Replacing a local transaction with several remote calls does not preserve its behavior automatically. Decide what happens when one step fails: safe retry, compensation, manual intervention, or rejection. Tests should cover those paths as well as successful execution.

## My practical rule

I would start with a clear domain boundary, a verifiable reason to separate it, and a plan for data and operations. If I cannot explain who owns each rule, how changes will be deployed, and what happens after a partial failure, the extraction is not ready yet. Making those decisions explicit improves the architecture, whether it remains a modular monolith or becomes separate services.

**Further reading:** [Martin Fowler's Strangler Fig](https://martinfowler.com/bliki/StranglerFigApplication.html). This article grew out of my Full Cycle notes on architecture fundamentals, DDD, and migration from monoliths to microservices.
