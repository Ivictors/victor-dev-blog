---
title: "API Composition for distributed reads"
description: "How to assemble a view from service APIs without joining another service's private tables."
date: 2026-11-22
lang: en
locale: en
layout: post-localized
translation-id: api-composition
link: /en/posts/api-composition/
alternate: /pt/posts/composicao-de-api/
tags: microservices, architecture, api
author: victor
---

A details screen may need order data from Orders and customer data from Customers. In a microservice architecture, the screen should not join both services' private database tables.

## Compose the read at an API boundary

An API Composition component calls the APIs that own the data and builds a response for the client:

```text
Client -> GET /order-summary/123 -> Composer
                                  |- Orders API
                                  '- Customers API
Client <- order status and customer name
```

The composer can live in an API Gateway, a screen-specific service, or another component that has a clear responsibility. Each service remains the owner of its data and meaning.

## Composition is for reads

The composer combines responses. It does not turn updates in several services into one database transaction and does not replace a saga for coordinating writes.

Parallel calls can reduce latency, but every dependency needs a timeout and a failure policy. Decide whether the screen can show partial data, use a cache, or return an error when one service is unavailable. Also define authorization for each downstream request; composition must not bypass the service that owns the data.

API Composition is a practical read model for distributed data. Its cost is explicit network latency and the need to handle partial failures at the boundary.

Limit fan-out when a request returns a list, or a single page can create an N+1 request pattern. Cache only when freshness and invalidation are explicit. Authorization still belongs to each service that owns the resource.

**Source:** [Microservices.io — API Composition](https://microservices.io/patterns/data/api-composition.html).
