---
title: "Sagas: choreography, orchestration, and compensation"
description: "How microservices coordinate a multi-step business operation when one transaction cannot span every service."
date: 2026-11-22
lang: en
locale: en
layout: post-localized
translation-id: sagas-choreography-orchestration
link: /en/posts/sagas-choreography-orchestration/
alternate: /pt/posts/sagas-coreografia-orquestracao/
tags: microservices, architecture, kafka
author: victor
---

An order may need an order service, inventory, and payments to complete separate local transactions. A single database transaction cannot safely cover all of them once each service owns its data.

## Choreography reacts to events

Each service publishes what happened and other services decide whether to react:

```text
Orders -> OrderCreated -> Inventory
Inventory -> StockReserved -> Payments
Payments -> PaymentApproved -> Orders
```

There is no central coordinator. Choreography can be simple for short flows, but the complete process becomes distributed across event handlers. As steps and exception paths grow, it becomes harder to answer who is responsible for the next action.

## Orchestration follows an explicit plan

An orchestrator tracks the saga state and sends commands to participants. It can make the flow easier to inspect and change, but it must persist state, handle timeouts, and avoid becoming the owner of business rules that belong to the services.

## Compensation is a new business action

If stock was reserved and payment fails, the system may need to release the reservation. That is a compensating action, not time travel: sending an email or shipping a package may not be reversible. Each service defines what compensation means for its own state.

Choose choreography when the flow and ownership remain easy to understand. Choose orchestration when an explicit process, visibility, and failure handling are more valuable than a fully decentralized flow. In both cases, define idempotency, timeouts, retries, and observable state before promising consistency.
