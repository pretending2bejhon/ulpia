---
title: Orders board
type: plan
domain: voss-ceramics
status: active
created: 2026-05-02
description: Live board of open orders, what stage each one is at, and what it is waiting on.
tags:
  - type/plan
  - domain/voss-ceramics
  - status/active
aliases:
  - Orders board
---

# Orders board

This board is an **artifact**, not memory. It carries typed front-matter because Clay
updates it and needs a handle on it — and front-matter still does not make it a claim. What
decides is who maintains it and whether it can be false: this board is rendered from the
orders, so it cannot be wrong about them, it can only be out of date. The claims behind it
live in `10-memory/semantic/clients/`.

## Open

| Customer | Pieces | Stage | Waiting on | Promised |
|---|---|---|---|---|
| Café Lumen | 60 | glaze firing | kiln element, Tomas not yet back | 2026-05-29 |
| Saturday class | 14 | bisque | nothing | 2026-05-23 |
| Market restock | 30 | thrown, drying | kiln time | none |

## Blocked

| Customer | Pieces | Blocked on |
|---|---|---|
| Hearthline test tiles | 6 | glaze delivery, expected 2026-05-21 |

## Done this month

| Customer | Pieces | Delivered |
|---|---|---|
| Spring market table | 42 | 2026-05-09 |
