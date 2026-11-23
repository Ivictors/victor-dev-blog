---
title: "Observability in microservices: follow the request"
description: "How metrics, logs, and traces work together when one business operation crosses several services."
date: 2026-11-22
lang: en
locale: en
layout: post-localized
translation-id: microservices-observability
link: /en/posts/microservices-observability/
alternate: /pt/posts/observabilidade-microservicos/
tags: microservices, architecture, observability
author: victor
---

In a distributed purchase, an error at the API may have started in Orders, Inventory, or Payments. A single error message does not tell us which dependency was slow or which decision failed.

## Three signals answer different questions

- **Metrics** show aggregated behavior over time, such as error rate and latency.
- **Logs** record specific events, such as a payment rejection.
- **Traces** connect the spans of one request across services.

OpenTelemetry provides APIs, SDKs, and collectors for instrumenting and exporting telemetry. It is not the database or user interface where the team queries every signal.

## Carry context across boundaries

The trace context must follow HTTP calls and messages. A stable `traceId` links spans and helps find related logs. Record the service, operation, result, duration, and correlation identifiers needed for an investigation.

Do not put passwords, tokens, or unnecessary personal data into logs and traces. Avoid using high-cardinality IDs such as every order ID as metric dimensions; that can create expensive numbers of time series.

## Start with an operational question

Instrumentation is useful when it supports a question: “Which step increased checkout latency?” or “Are payment failures isolated to one provider?” Define the signal, sampling, retention, and alert threshold around that question.

Observability does not prevent failures. It shortens the path from a symptom to an evidence-based explanation, which is essential when a business operation crosses independently deployed services.

Context propagation connects the span created by the caller to the span created by the downstream service. For HTTP, W3C Trace Context is commonly used; for messages, the context belongs in message metadata. Do not propagate credentials or sensitive baggage to external systems.

**Sources:** [OpenTelemetry signals](https://opentelemetry.io/docs/concepts/signals/) and [context propagation](https://opentelemetry.io/docs/concepts/context-propagation/).
