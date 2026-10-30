---
title: "A Docker Desktop hang and a fix in Floci's HTTP client"
description: "How stale-connection validation over a named pipe stalled a Floci test on Windows, and what changed in PR #5400."
date: 2026-10-29
lang: en
locale: en
layout: post-localized
translation-id: floci-docker-named-pipe
link: /en/posts/floci-docker-named-pipe-hang/
alternate: /pt/posts/travamento-docker-named-pipe-floci/
tags: java, open-source
author: victor
---

## The failure I saw

While validating an earlier Floci contribution, I hit a hang on Windows with Docker Desktop. `EcsImagePullDockerIntegrationTest` appeared to be stuck pulling the `registry:2` image. A thread dump showed a different point of failure: execution was still in a Docker ping, before the image pull began. The HTTP client was checking whether an idle pooled connection was still usable and became blocked reading Docker's named pipe.

I documented the reproduction in [issue #5394](https://github.com/floci-io/floci/issues/5394). It involves `EcsImagePullDockerIntegrationTest`, Windows, Docker Desktop, and the `npipe:////./pipe/docker_engine` endpoint. Pool reuse and timing matter, so a run may not reproduce the hang every time.

## Why the timeout was insufficient

`FlociDockerHttpClient` uses a connection pool. Before reusing a connection idle for more than a second, Apache HttpClient performs a short read to detect a stale connection. With this transport, the Docker client's `NamedPipeSocket` could block despite the short read timeout used by that check. The test therefore never reached the Docker operation that first looked suspicious.

The test exposed behavior in the HTTP client used for Docker communication. The change is in the main client code, rather than in the ECS test alone.

## What the PR changed

In [PR #5400](https://github.com/floci-io/floci/pull/5400/files), connection configuration became dependent on the `dockerHost` scheme. Stale-connection validation is disabled for `npipe` and retains its one-second threshold for `tcp` and `unix`. The PR did not change idle-connection eviction or request timeouts.

The unit test checks all three configuration choices. The PR description also reports a passing focused run of `EcsImagePullDockerIntegrationTest` and Checkstyle for the two changed Java files. The full suite was not run as part of that validation. Those results support the investigated case, but do not establish that every Docker scenario is covered.

## What I took away

A timeout configured in an HTTP client does not necessarily bound every I/O path in its underlying transport. When an operation appears to hang, finding the actual blocked call helps avoid fixing the wrong component. Here, a thread dump separated image pulling from validation of an earlier connection and kept the change specific to `npipe`.

**Code and context:** [issue #5394](https://github.com/floci-io/floci/issues/5394) · [PR #5400](https://github.com/floci-io/floci/pull/5400).
