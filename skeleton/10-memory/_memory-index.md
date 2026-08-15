---
title: Memory Index
type: hub
domain: <AGENT-NAME>
status: active
created: <YYYY-MM-DD>
description: Routing table for all of memory - the four types, what each one answers, and where a session may write.
tags:
  - type/hub
  - status/active
aliases:
  - Memory Index
---

# Memory index

`10-memory/` holds **claims**: sentences that can be true or false and can go stale.
Everything else in the vault holds artifacts.
If what you have is a thing rather than a sentence, it does not belong here.

Pick the type by **how the claim is bound**, not by what it is about.

| Type | Answers | Bound by | n |
|---|---|---|---|
| [[_episodic-index\|episodic/]] | what happened, and what was decided | **a date** | 0 |
| [[_semantic-index\|semantic/]] | what is true right now | nothing, undated | 0 |
| [[_procedural-index\|procedural/]] | how to do it, and what breaks when you don't | a repeatable procedure | 0 |
| [[_prospective-index\|prospective/]] | what is intended | a future date | 0 |
| `inbox.md` | not sorted yet | n/a | append-only |
| `working/` | this session's scratch | one file per session | n/a |

Don't know yet → `inbox.md`. **Flag rather than guess**: an honest unsorted line costs
a moment of consolidation; a confident wrong placement costs a lost claim.

## Where a session may write

| Path | Policy |
|---|---|
| `working/` | append freely: **one file per session, never a shared one** |
| `inbox.md` | append freely |
| `episodic/` | append freely |
| `prospective/` | append freely: a plan is authored, not derived |
| `semantic/` | **consolidation only** |
| `procedural/` | **consolidation only** |

Semantic and procedural memory change through consolidation, never mid-session.
A session that discovers a durable fact writes it to `working/` and lets consolidation
promote it. Six processes writing one shared file produce fewer files than success
messages. The lost writes are reported as recorded.

## Cutting across the types

A system is a `domain:` value, never a folder. Its parts scatter by kind and reassemble
by query.

```bash
rg 'domain/<slug>' 10-memory/                # every claim about one domain, all four types
rg --files -g '*__decision.md' 10-memory/    # every decision ever, one call
rg '^- \[decision\]' 10-memory/              # typed observation lines, all of memory
```

Naming, front-matter and growth rules: `00-core/meta/conventions.md`.
