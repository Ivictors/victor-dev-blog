---
title: "Microservice boundaries and the Strangler Fig migration"
description: "How business capabilities guide service boundaries and how to migrate a monolith incrementally."
date: 2026-11-22
lang: en
locale: en
layout: post-localized
translation-id: microservices-boundaries-strangler
link: /en/posts/microservices-boundaries-strangler/
alternate: /pt/posts/limites-microservicos-strangler/
tags: microservices, architecture
author: victor
---

Splitting a monolith is not mainly a matter of creating more deployable units. The difficult decision is choosing boundaries that keep business rules together and reduce coordinated changes.

## Start with business capabilities

Use capabilities such as orders, inventory, payments, and delivery as candidates. A service should own the rules and data that change for the same business reasons. This is a starting hypothesis, not an automatic mapping from every department or database table to a service.

Good boundaries reduce calls that must change together. They also make ownership clearer: the team responsible for orders can evolve the order lifecycle without exposing its tables to every other team.

## Migrate with Strangler Fig

The Strangler Fig pattern routes each capability to either the legacy system or a new service while both systems coexist:

```text
Client -> router -> new Catalog service
                -> legacy Orders module
                -> legacy Payments module
```

Choose one capability with a clear boundary and useful feedback. Route only that capability to the new implementation, compare behavior, and migrate its data and integrations deliberately. Repeat after the first slice is stable.

## Watch the shared-data trap

If the monolith and the new service write the same tables without an explicit ownership rule, the migration creates two sources of truth. Prefer a transition contract, a single owner for each change, and events or APIs for other consumers.

The new service may need an anti-corruption layer to translate legacy terms into its own domain. This prevents the old model from spreading into the new code while the systems coexist.

Microservices are useful when their boundaries improve independent change. The Strangler Fig approach makes that decision reversible in small steps, but it does not remove the cost of distributed operations.
