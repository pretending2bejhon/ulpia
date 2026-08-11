---
title: Kiln capacity model
type: fact
domain: studio
status: active
created: 2026-05-04
reviewed: 2026-05-04
review_interval_days: 30
owner: Mara Voss
description: How many finished pieces one kiln produces in a week, and the single assumption the whole number rests on.
source: "[[2026-05-02_cafe-lumen-first-order__log]]"
source_type: internal
load_bearing: "The old kiln completes four firings in a normal week."
load_bearing_status: UNVERIFIED
verify: 'rg -q "four firings a week" 10-memory/procedural/studio/bisque-firing-schedule__sop.md'
tags:
  - type/fact
  - domain/studio
  - status/active
  - kind/model
aliases:
  - Kiln capacity
---

# Kiln capacity model

**Falls over if:** the old kiln does not complete four firings in a normal week —
`UNVERIFIED`.

## The number

One kiln produces roughly **40 finished pieces a week**.

Every piece is fired twice. A bisque load holds about 40 pieces because they may touch; a
glaze load holds about 15 because they may not. So 40 finished pieces cost one bisque
firing plus not quite three glaze firings — call it four firings, which is what the kiln
does in a week that goes well.

Café Lumen's 60-piece order is therefore a week and a half of kiln time, and two standing
customers of that size would not fit alongside the Saturday class and the market table.
That is the arithmetic behind [[2026-05-10_second-kiln__decision]].

## Why the assumption is named at the top and not the bottom

Four firings a week is the only input here that is not measured. Everything else follows
from load geometry, which does not change. If the real figure is three, the ceiling is 30
pieces a week and the delivery date on the current order has no slack at all — and a
reader who met that caveat in a closing paragraph would already have quoted a lead time.

## What the verify line actually checks

The `verify:` in the front-matter asserts only that the firing schedule this model reads
from still claims four firings a week. **It cannot see the kiln.** A check that can only
confirm a document is the weakest kind there is; when a firing log exists, point it there
instead and let the claim be falsified by the studio rather than by its own paperwork.
Until then `load_bearing_status` stays `UNVERIFIED`, which is honest and blocks nothing.
