---
title: "Specification before AI-generated code"
description: "How explicit requirements, invariants, and tests keep AI-assisted backend work reviewable."
date: 2026-11-17
lang: en
locale: en
layout: post-localized
translation-id: specification-before-ai-code
link: /en/posts/specification-before-ai-code/
alternate: /pt/posts/especificacao-antes-codigo-ia/
tags: ai, software-engineering, architecture
author: victor
---

Asking an AI assistant to implement a feature is easy. Reviewing whether the result is correct is harder when the expected behavior was never written down.

The useful boundary is a small specification: the problem, the actors, the invariants, the failure cases, and the evidence that will prove the change works. It does not need to be a large document. It needs to make the decision visible.

## Start with behavior

Before generating code, describe what must happen from the user's or system's point of view. For a payment command, that might include:

- an authenticated customer can submit an order;
- the same idempotency key does not charge twice;
- an invalid state returns a defined error;
- a successful charge produces one domain event.

These statements give the implementation a target and give the review a way to detect an apparently plausible but incorrect solution.

## Record invariants and non-goals

An invariant is a rule that must remain true. Examples include “an order cannot be paid twice” and “a user cannot read another tenant's invoice.” A non-goal is equally useful: it prevents the implementation from silently expanding into a redesign.

The model can propose an approach, but it should not invent a new business rule because the prompt was incomplete. Unresolved decisions should remain visible for the developer to approve.

## Use tests as evidence

Tests should represent the behavior described in the specification. A failing test can reveal a missing requirement; a passing test provides evidence for one scenario. Neither replaces a review of authorization, persistence, retries, or operational impact.

A practical loop is:

1. write the acceptance scenario;
2. confirm that it fails for the expected reason;
3. ask for the smallest implementation;
4. run the relevant checks;
5. review the diff against the original invariants.

This keeps AI assistance inside a controlled engineering process.

## Ask the model for a plan before code

For a complex change, ask the model to identify files, dependencies, risks, and validation steps first. Review that plan before implementation. For a small correction, a short request and a focused test may be enough.

The objective is not to produce a document for every edit. It is to keep the reasoning proportional to the risk and make important decisions explicit.

AI-assisted development works best when context, requirements, controlled autonomy, and objective validation are combined. The model accelerates implementation; the specification keeps the result accountable.
