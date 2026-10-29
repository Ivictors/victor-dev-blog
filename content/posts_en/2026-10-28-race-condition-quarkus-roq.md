---
title: "How I fixed a race condition in Quarkus Roq"
description: "Why the editor stayed on Please wait after creating or saving a page, and how I fixed the asynchronous sequence."
date: 2026-10-28
lang: en
locale: en
layout: post-localized
translation-id: roq-editor-race-condition
alternate: /pt/posts/como-corrigi-uma-race-condition-no-quarkus-roq/
tags: java, quarkus, open-source, concurrency
author: victor
---

## Context

The Quarkus Roq editor sometimes stayed on **“Please wait”** after creating or saving a page. The file was already on disk, but the interface did not finish updating. Reloading the tab with F5 cleared the symptom. The failure was intermittent: it depended on the order in which an asynchronous write and the browser reconnection completed.


## The problem

In `roq-editor/deployment/src/main/resources/dev-ui/qwc-roq-editor.js`, `createPage` and `savePageContent` call `_writeCall`. The write runs in the background, and `_writeCall` polls its status until it completes. Creating or saving the file also triggers a hot reload, which disconnects and reconnects the browser.

Before the fix, page creation set `_pendingRefreshPages` **after** `_writeCall` finished. The reconnection only called `_refreshPageInfo()` when that flag was already set. If the browser reconnected first, it skipped the refresh. The flag and overlay were then activated, but there was no further reconnection to trigger the refresh that would close the overlay.

## Investigation

The sequence was:

1. The editor starts writing; hot reload begins.
2. The browser reconnects and checks `_pendingRefreshPages`, which is still false.
3. `_writeCall` finishes and sets the refresh flag.
4. The overlay remains open because the reconnection has already happened.

The [issue description](https://github.com/quarkiverse/quarkus-roq/issues/967) notes that `_writeCall` polled `writeStatus()` every 500 ms. That delay helped create a window in which reconnection could happen before the state change. The file write itself had succeeded; the UI events were not coordinated correctly.

## Solution

In the [PR #1286 diff](https://github.com/quarkiverse/quarkus-roq/pull/1286/files), I moved the `_pendingRefreshPages` and `_writeInProgress` state changes **before** starting `createPage` or `savePageContent`. While a write is in progress, reconnection defers `_refreshPageInfo()`. When the operation ends, `finally` clears `_writeInProgress` and, if a refresh is pending and the connection is active, starts the refresh.

I also clear `_pendingRefreshPages` if the write or refresh fails. This prevents the editor from retaining a pending refresh with no operation left to complete it. The overlay lifecycle now follows the refresh result instead of depending on one particular ordering of the write and reconnection.

## What I learned

An asynchronous operation can finish successfully while leaving the interface in an incorrect state. Here, the critical detail was when the intention to refresh became visible to the reconnection handler. Setting that state before the write, delaying the refresh while the write runs, and clearing state on failure made the sequence explicit.

## Source

The full reproduction report is in [issue #967](https://github.com/quarkiverse/quarkus-roq/issues/967); the integrated change is in [PR #1286](https://github.com/quarkiverse/quarkus-roq/pull/1286).
