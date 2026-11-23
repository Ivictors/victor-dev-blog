---
title: "Transactional Outbox without an exactly-once promise"
description: "How to keep a database change and an event publication reliably connected in a microservice."
date: 2026-11-22
lang: en
locale: en
layout: post-localized
translation-id: transactional-outbox
link: /en/posts/transactional-outbox/
alternate: /pt/posts/transactional-outbox/
tags: microservices, architecture, kafka
author: victor
---

A service often needs to update its database and publish an event. Two independent operations create a gap: the process can stop after one succeeds and before the other does.

## Put both records in one local transaction

Transactional Outbox stores the business change and a pending message in the same database transaction:

```text
Local transaction
  1. Save the order.
  2. Save OrderCreated in the outbox.
  3. Commit both records.

Relay
  4. Read pending messages.
  5. Publish them to the broker.
```

If the transaction rolls back, neither record is visible. If it commits, a relay can publish the pending event later. The atomic boundary is the local database; it is not a distributed transaction with the broker.

## The relay can publish twice

The relay may crash after publishing and before marking the message as sent. On restart it can publish the same event again. The pattern therefore improves delivery of the event, but does not create exactly-once processing from end to end.

Give each event a stable `eventId`. Consumers should recognize an event they have already applied, ideally recording that ID in the same local transaction as their own state change. Also define retry limits, ordering rules, and cleanup for old outbox records.

Polling the table and reading the database transaction log through CDC are common relay strategies. Choose according to latency, operational complexity, and the database capabilities you actually run.

The Outbox pattern is valuable because it makes the failure boundary explicit. Its guarantee ends at the local commit and must be completed by idempotent consumers.

For events from the same aggregate, the relay must also preserve the order required by the domain. A stable key and a concurrency strategy should prevent `OrderCancelled` from being published before `OrderCreated`.

**Source:** [Microservices.io — Transactional outbox](https://microservices.io/patterns/data/transactional-outbox.html).
