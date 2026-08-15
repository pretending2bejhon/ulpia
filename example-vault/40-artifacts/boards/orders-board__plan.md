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
updates it and needs a handle on it. Front-matter still does not make it a claim. It
is **derived state**: the claims it is rendered from live in `10-memory/semantic/clients/`
and the episodic record, and when the board disagrees with them, the claims win and the
board gets regenerated. Staleness is its failure mode, and the fix is always re-rendering,
never editing the board into truth.

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
