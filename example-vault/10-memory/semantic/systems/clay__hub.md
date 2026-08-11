---
title: Clay
type: hub
domain: clay
status: active
created: 2026-05-01
reviewed: 2026-05-16
description: What Clay is, where its parts live, and the query that reassembles them - the agent that maintains this vault.
tags:
  - type/hub
  - domain/clay
  - status/active
aliases:
  - Clay
---

# Clay

Clay is the agent that maintains this vault: it drafts invoices, keeps the orders board
current, and runs the nightly consolidation pass.

## Clay has no folder

Clay is a `domain:` value, not a directory, because its parts have different lifecycles. A
procedure can go stale, a decision is bound to a date, and a rendered board has no truth
value at all — filing them together would put each one in two plausible homes.

| Part | Where it lives |
|---|---|
| How it works | [[consolidation-routine__sop]] |
| The cache it rewrites every session | `00-core/hot.md` |
| The routing table it reads | [[_memory-index]] |
| What it renders for Mara | `40-artifacts/boards/orders-board__plan.md` |
| Its code | none yet — the studio is one person and Clay runs from the boot file |

## Reassembling it

```bash
rg 'domain/clay' 10-memory/          # every claim about Clay, all four memory types
rg -l '^type: sop$' 10-memory/procedural/vault/
```

This hub is the human entry point. The `domain/clay` tag is the agent's, and it is the one
that survives a reorganisation — the folders can move, the query cannot break.
