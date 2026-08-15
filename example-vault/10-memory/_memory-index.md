---
title: Memory Index
type: hub
domain: clay
status: active
created: 2026-05-01
description: Routing table for all of Mara's memory - the four types, what each answers, and where a session may write.
tags:
  - type/hub
  - domain/clay
  - status/active
aliases:
  - Memory Index
---

# Memory index

`10-memory/` holds **claims**: sentences that can be true or false and can go stale.
Everything else in this vault holds artifacts.
Pick the type by **how the claim is bound**, not by what it is about.

| Type | Answers | Bound by | n |
|---|---|---|---|
| [[_episodic-index\|episodic/]] | what happened, and what was decided | **a date** | 3 |
| [[_semantic-index\|semantic/]] | what is true right now | nothing, undated | 6 |
| [[_procedural-index\|procedural/]] | how to do it, and what breaks when you don't | a repeatable procedure | 2 |
| [[_prospective-index\|prospective/]] | what is intended | a future date | 1 |
| `inbox.md` | not sorted yet | n/a | append-only |
| `working/` | this session's scratch | one file per session | 1 |

Don't know yet → `inbox.md`. **Flag rather than guess**: an honest unsorted line costs a
moment of the next consolidation pass; a confident wrong placement costs the claim.

## Where a session may write

| Path | Policy |
|---|---|
| `working/` | append freely: **one file per session, never a shared one** |
| `inbox.md` | append freely |
| `episodic/` | append freely |
| `prospective/` | append freely: a plan is authored, not derived |
| `semantic/` | **consolidation only** |
| `procedural/` | **consolidation only** |

Semantic and procedural memory change through consolidation, never mid-session
([[consolidation-routine__sop]]).

## Cutting across the types

```bash
rg 'domain/clay' 10-memory/                  # every claim about the agent itself
rg --files -g '*__decision.md' 10-memory/    # every decision ever, one call
rg '^- \[decision\]' 10-memory/              # typed observation lines, all of memory
```

Domains in live use: `clay` · `voss-ceramics` · `studio` · `cafe-lumen`.
