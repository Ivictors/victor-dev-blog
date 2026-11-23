---
title: "Anti-Corruption Layer: protect the domain at an integration boundary"
description: "How to translate a legacy model without letting external concepts spread through a microservice."
date: 2026-11-22
lang: en
locale: en
layout: post-localized
translation-id: anti-corruption-layer
link: /en/posts/anti-corruption-layer/
alternate: /pt/posts/camada-anticorrupcao/
tags: microservices, architecture, ddd
author: victor
---

An integration is risky when two systems use similar words with different meanings. If a new Orders service starts using the legacy billing system's codes everywhere, the legacy model becomes part of the new domain.

## Translate at the boundary

An Anti-Corruption Layer (ACL) translates names, structures, protocols, and errors between models:

```text
Orders domain -> ACL -> Legacy billing
     PAID      <-      code B7
```

The service receives its own concept, such as `PAID`, instead of spreading `B7` through application and domain code. The ACL can be a module inside the consuming service or, when there is a clear operational reason, a separate component.

## Translation is about meaning

Matching field names is not enough. “Active customer” in sales may not mean the same thing as “active customer” in billing. The service that owns the business rule should keep that rule; the ACL should handle the difference at the system boundary.

An ACL is not an API Gateway. A gateway routes or applies edge policies. The ACL protects a domain model from an external vocabulary and contract.

Use the pattern when integrating a legacy system, supplier, or another bounded context with different rules. If both models already have the same meaning, a translation layer may add complexity without a benefit.
