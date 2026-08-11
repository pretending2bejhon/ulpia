# Consolidation

Consolidation is the pass that turns a day of scattered capture into typed memory. It
runs once, alone, on a schedule — nightly is the natural cadence — and it is **the only
thing permitted to write the durable layers of memory.**

This document is the concept, not an implementation. There is no code in this repository
by design: the loop is a handful of decisions, and every one of them is easier to get
right in your own stack than to adopt from someone else's.

Prerequisites: [memory-model.md](memory-model.md) for what the four types are,
[conventions.md](conventions.md) for the keys and names referenced below.

---

## Why one writer, and why not a live session

**Semantic and procedural memory are consolidation-only. Every other path is open to a
live session.** That asymmetry is not caution, it is arithmetic.

Those two layers are organised by *subject*, so two sessions working on the same customer
plausibly want to edit the same file. The dated layers are not: a session log and a
decision record are bound to a moment that already happened, and two sessions do not
write the same moment.

Concurrent writes to one file do not raise an error. They produce **fewer files than
success messages** — writes are lost silently and reported as recorded, which is worse
than a crash, because a crash gets investigated. One writer, one moment, one file removes
the failure mode instead of managing it.

So a session that discovers something durable does not promote it. It writes to its own
working file — one per session, no contention possible — and consolidation promotes it
later, with nothing else running.

---

## The drain path

Consolidation reads two inputs and writes into four homes.

**Inputs**

- `10-memory/inbox.md` — append-only capture. One line per thought, no structure, no
  front-matter, no routing decision. This file exists so that *"I don't know where this
  goes"* has a legal answer that is not a guess.
- `10-memory/working/` — one file per session, holding what that session found.

**The decision, per claim**

Apply the placement procedure and stop at the first match. The question is always **how
is this bound**, never what it is about:

| Bound by | Home |
|---|---|
| a date — it happened | `episodic/<YYYY-MM>/` |
| nothing — true right now | `semantic/<area>/` |
| a repeatable procedure | `procedural/<area>/` |
| a future date | `prospective/<YYYY-MM>/` |
| nothing you can answer honestly | **leave it in the inbox** |

That last row is load-bearing. An unsorted line costs one more pass; a confident wrong
placement costs the claim outright, because nobody searches the folder it went to. The
unsorted line is visible and irritating, which is exactly why it gets fixed. A misfiled
claim is invisible and calm.

**Promotion, not overwriting.** When a new claim replaces an old one, the old note keeps
its text and gets `superseded_by:`; the new one carries `valid_from:`. Never write a
correction over an old body. A patched note can no longer answer *what did we believe
three months ago, and when did that change* — and that question is most of the reason to
write claims down at all.

**Working files are not drained.** They stay as the session record. Only the inbox is
cleared, and only the lines actually promoted.

---

## The machine-only keys

Six keys belong to consolidation and to nothing else:

```yaml
updated:            # any edit
retrieval_count:    # incremented when the note is read
last_retrieved:     # stamped when the note is read
last_consolidated:  # stamped by this pass
belief:             # confidence, maintained across passes
belief_reason:      # why it moved
```

Together they are the **decay signal** — how the vault knows which claims are load-bearing,
which are never read, and which have been quietly contradicted by something newer.

**An agent that hand-writes one of these corrupts the signal, and nothing errors.** It is
the same class of act as editing a log file to say the job succeeded: the record now
disagrees with reality, and the only thing that could have caught it was the record.

Two disciplines make the signal worth having:

- **`reviewed` is never older than `updated`.** `updated` is automatic; `reviewed` is
  strictly manual and means a human read the claim and confirmed it. Auto-stamp both and
  every note looks freshly verified — you have built a decay signal that always reads
  green. This is the first lint check worth wiring.
- **`belief` moves on evidence, not on age.** A claim reinforced by a new session should
  rise; a claim contradicted by one should fall and say why in `belief_reason`. A claim
  nobody has touched is not thereby false, it is merely unexamined — and that is what
  `review_interval_days` is for.

---

## Executing the verify lines

Notes that carry a number governing a decision are tagged `kind/model` and hold a
`verify:` — an executable check. Consolidation is the natural place to run them.

**Run each one and record what happened.** `pass | fail | error | timeout | blocked` are
all different, and collapsing them loses the plot. The rule that makes the whole mechanism
worth having:

> **Presence is never mistaken for a pass.**

A note that *has* a `verify:` has proved nothing. A note whose `verify:` ran and returned
zero has. Any report that lists gated notes without listing outcomes is a report that will
one day be green while everything underneath it is wrong.

Three details that decide whether this works:

- **Isolate and time-box.** Each check runs in its own child process under a timeout, so
  one hung check cannot take the pass down with it.
- **Only from front-matter.** A `verify:` line appearing in a note *body* — including
  inside a fenced code block, including in an example in your own documentation — is
  ignored. Note bodies carry pasted, forwarded and scraped text, and untrusted input is
  never instruction. A scanner that extracts `verify:` from anywhere in the file will
  eventually execute something an attacker mailed you.
- **Report the ungated too.** The same pass should list every `kind/model` note *missing*
  `load_bearing:` or `verify:`, naming the absent key. Otherwise a model authored without
  a gate is silence, and silence reads like success.

---

## Regenerating the indexes

Every folder has an index; every index goes stale. Entries point at files that moved,
counts drift, and **nothing errors** — an index is prose, and prose does not fail.

So the last step of the pass rebuilds every index **from the filesystem**, between
explicit start and end markers, leaving hand-written prose outside them untouched. The
part that goes stale is the part a script rewrites. This is the only durable fix for index
rot; a convention to keep them updated is not a fix, it is a hope.

While the tree is already being walked, this is the cheapest moment to report:

- wikilinks that resolve to nothing, and index entries pointing at absent files
- duplicate basenames — they make every existing link to the original silently ambiguous
- notes past the size threshold, and drafts untouched past their age threshold
- `reviewed` older than `updated`
- anything that looks like a credential

**Report; do not auto-fix.** An auto-fixer that is wrong once teaches everyone to stop
reading the report, and after that the report is decoration.

---

## What a good pass looks like

A pass that finishes is not a pass that worked. Check for these, in this order:

1. The inbox is smaller, and every line that left it landed somewhere findable.
2. No file was written by two writers.
3. Every `verify:` has an outcome next to it — not a checkmark for existing.
4. Every index was regenerated, including the ones that did not change.
5. The failures are listed. **A run with nothing to report is a run to be suspicious of**,
   because a real vault always has something drifting.

Nothing in the list above requires a large system. It requires that each step reports what
it actually did, rather than that it ran.
