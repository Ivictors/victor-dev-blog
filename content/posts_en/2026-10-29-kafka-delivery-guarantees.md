---
title: "Kafka: what partitions, acks, and idempotence actually guarantee"
description: "How to reason about ordering, durability, and duplicates without confusing writes to Kafka with application processing."
date: 2026-10-29
lang: en
locale: en
layout: post-localized
translation-id: garantias-kafka
link: /en/posts/kafka-delivery-guarantees/
alternate: /pt/posts/garantias-entrega-kafka/
tags: kafka, architecture
author: victor
---

## An event's path

When I started studying Kafka, I asked a simple question: where does a message live, and how does it reach the code that needs to process it? A producer writes records to a topic split into partitions. Each partition is an ordered log, with offsets identifying positions **within that partition**. A consumer reads the records and tracks its processed position.

That gives us an important limit: Kafka does not provide a total order across every partition of a topic. If events for one entity must stay in relative order, the key and partition choice matter. The same key generally maps to the same partition while the partitioning strategy and partition count remain compatible; changing the partition count can change the mapping for future events.

## Parallelism in consumer groups

Within a consumer group, one partition is assigned to one consumer at a time. If four consumers in the same group read only a topic with three partitions, one has no partition to read. A different group keeps its own offsets and can read the same records for another purpose. Kafka does not write a new copy of each record for each group.

Assignments can change as members join, leave, or stop responding. Applications need to tolerate reprocessing after failures and rebalances, particularly when an external effect completes before its offset is committed.

## `acks` confirms the write, not the processing

`acks=0` waits for no broker acknowledgment. `acks=1` waits for the leader to append to its local log, without waiting for all synchronized replicas. `acks=all` waits for acknowledgment from the replicas in the in-sync set (ISR). This is the strongest producer acknowledgment setting, but actual durability also depends on replication, `min.insync.replicas`, and leader election policy. None of these settings says whether a consumer completed its work.

In current Apache Kafka documentation for the Java producer, `acks=all` is the default. Older notes or examples may show a different default, so check the version used by your project.

## Idempotence does not cover every side effect

With `enable.idempotence=true`, the producer uses IDs and sequence numbers to stop a retry of the same production from appending duplicate records. That protects the producer's send path. It does not prevent a restarted application from producing the same business event as a new operation, and it does not make a consumer's payment request idempotent.

For Kafka-to-Kafka processing, transactions can coordinate produced records with consumed offsets, provided downstream consumers are configured to read only committed records. If processing writes to a database, payment API, or another external system, the application still needs its own strategy, such as an idempotency key and an atomic operation at the destination. “Exactly once” depends on the boundaries of the operation being observed.

## What I would check in a project

Before promising a guarantee, I would record the partition key, partition count, `acks`, replication, `min.insync.replicas`, retry policy, offset commit point, and how the destination handles duplicates. That turns a label such as *at least once* into a testable description of the whole flow.

**Sources:** [Apache Kafka design](https://kafka.apache.org/35/design/design/) and [Java producer configuration](https://kafka.apache.org/42/configuration/producer-configs/). This article grew out of my Full Cycle notes on topics, partitions, producers, and consumers.
