---
title: Café Lumen places a first wholesale order
type: log
domain: cafe-lumen
status: active
created: 2026-05-02
description: The call in which Café Lumen placed a first wholesale order of 60 pieces, and the terms agreed on it.
source: "95-data/raw/voss-ceramics__2026-05-02_cafe-lumen-inquiry__email.txt"
source_type: external
tags:
  - type/log
  - domain/cafe-lumen
  - status/active
aliases:
  - Café Lumen first order
---

# Café Lumen places a first wholesale order

Café Lumen wrote in on 2026-05-02 after seeing the Saturday class table at the spring
market. Mara called back the same afternoon and the order was agreed on that call.
The written enquiry it started from is kept raw and unedited at
`95-data/raw/voss-ceramics__2026-05-02_cafe-lumen-inquiry__email.txt`.

## What was agreed

Café Lumen ordered 60 pieces: 36 cortado cups, 24 saucers, unglazed rims, delivery by
2026-05-29.
Payment is on delivery, not on order, and they asked to be invoiced monthly if this
becomes a standing arrangement.

- [decision] 60 pieces at the wholesale tier, delivery promised 2026-05-29
- [decision] Unglazed rims on every piece: their staff stack cups wet
- [constraint] Payment on delivery, so the clay and glaze are financed by the studio first
- [constraint] 60 pieces is four glaze loads, and the kiln does four firings in a good week
- [risk] One kiln, and the delivery date has no slack in it if a firing goes wrong

## What this changed

The last constraint above is what forced the capacity question. It is worked out, with its
falls-over line, in [[kiln-capacity-model__fact]], and the standing facts about this
customer live in [[cafe-lumen__fact]].
