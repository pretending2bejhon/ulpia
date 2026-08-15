---
title: Consolidation routine
type: sop
domain: clay
status: active
created: 2026-05-01
reviewed: 2026-05-16
review_interval_days: 90
owner: Mara Voss
description: The nightly pass that drains inbox and working notes into typed memory, stamps the machine-only keys, and regenerates the indexes.
tags:
  - type/sop
  - domain/clay
  - status/active
aliases:
  - Consolidation
---

# Consolidation routine

The consolidation routine is the only thing that writes `semantic/` and `procedural/`. A
live session never does, because those two layers are organised by subject rather than by
date, which makes them the only places where two sessions would plausibly edit the same
file. Concurrent writes to one file do not error, they just go missing.

## Steps

1. Read every line of `10-memory/inbox.md` and every file in `10-memory/working/`.
2. For each claim, pick the type by how it is bound: a date → `episodic/`, undated and true
   now → `semantic/`, a repeatable procedure → `procedural/`, a future date →
   `prospective/`. Anything that resists the question stays in the inbox.
3. Write the promotion. A claim that replaces an existing one gets `superseded_by:` on the
   old note and `valid_from:` on the new one, never a correction typed over the old body.
4. Stamp the machine-only keys and nothing else: `updated`, `retrieval_count`,
   `last_retrieved`, `last_consolidated`, `belief`, `belief_reason`.
5. Leave `reviewed` alone. It is manual, it means a human confirmed the claim, and it must
   never be older than `updated`.
6. Regenerate the four type indexes and [[_memory-index]] from the filesystem.
7. Clear the drained inbox lines. Working files stay as the session record.

## What breaks when you skip a step

Skipping step 2's honesty test is how a claim gets filed confidently in the wrong folder,
where nobody will ever look for it: an unsorted inbox line is visible and annoying, which
is why it gets fixed.
Writing a correction into an old body instead of step 3 loses the answer to "what did we
believe in March".
Hand-setting a key from step 4, or auto-stamping `reviewed` in step 5, makes every note
look freshly verified and the decay signal worthless.
Skipping step 6 is index rot: entries pointing at files that moved, and no error anywhere.
