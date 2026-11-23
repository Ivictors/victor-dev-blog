---
title: "Secret Manager and least privilege in microservices"
description: "How services can access credentials without spreading secrets through source code, images, or logs."
date: 2026-11-22
lang: en
locale: en
layout: post-localized
translation-id: secret-manager-microservices
link: /en/posts/secret-manager-microservices/
alternate: /pt/posts/secret-manager-microservicos/
tags: microservices, security, aws
author: victor
---

Database passwords, API tokens, and private keys need restricted access and rotation. Putting them in source code or container images makes it harder to control who can use them and how quickly they can be replaced.

## Use a specialized secret store

A Secret Manager stores protected values, checks the workload identity, and returns only the secrets that identity is allowed to read. HashiCorp Vault and AWS Secrets Manager are examples. The service should authenticate through a trusted workload identity rather than a fixed password embedded in its code.

```text
Payments service -> workload identity -> Secret Manager
Payments service <- authorized secret
Payments service -> payment provider
```

Grant each service only the secrets it needs. Avoid logging values, copying them into images, or returning them in error responses. Rotation also requires the application to reload or renew credentials safely.

## Plan outages and auditing

Define what happens when the Secret Manager is unavailable. A short-lived cached value may be acceptable for a bounded period, while a new startup may need to fail closed. Record access for investigation and revoke compromised credentials.

A Secret Manager is not a custom microservice for passwords. Building one adds difficult responsibilities around storage, authentication, availability, rotation, and audit. Integrate a specialized service and keep application data protection separate from secret storage.

The identity check happens before the secret is returned. Prefer workload identities, least-privilege policies, and temporary credentials when the platform supports them. Rotation also requires the application to reload connections safely during the transition.

**Source:** [AWS Secrets Manager best practices](https://docs.aws.amazon.com/secretsmanager/latest/userguide/best-practices.html).
