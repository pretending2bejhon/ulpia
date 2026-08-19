# Ulpia

**A file-system memory architecture for AI agents.** Typed markdown, a placement
algorithm an agent can execute without judgment, and a nightly consolidation loop.
In daily production since June 2026. No vector database.

*Straight to setup: [Quickstart](#quickstart).*

Most agent-memory projects give you a store and leave the hard part to you: **deciding
where a thing goes.** Ulpia's contribution is the two rules that make that decision
mechanical: a hard split between *claims* and *artifacts*, and a placement procedure
you run top to bottom and stop at the first match. Two people filing the same file land
in the same folder. So do two agents.

Named after the *Bibliotheca Ulpia*, Trajan's library in Rome: Greek and Latin halls
flanking his column, and the only Roman library confirmed to have survived to the fall
of the empire that built it. Plain files outlast the systems stacked on top of them.

---

## What this is and is not

Three things ship here: the **specification** ([docs/](docs/)), a copyable empty
**skeleton**, and a complete worked **example vault**. No code ships. That is a
decision, not a gap. The method describes an operating loop (nightly consolidation,
executed `verify:` lines, lint); this repository specifies those contracts precisely
enough to implement, and [docs/consolidation.md](docs/consolidation.md) states the
reasoning. You bring the runner: any agent that reads and writes files. The author runs
the loop with Claude Code against a production vault; reference scripts are planned for
v1.1. Until then, treat this as a specification and a template you instantiate, not a
package you install. The trust model is in [SECURITY.md](SECURITY.md): what is code,
what is data, and what never crosses between them.

---

## Why files

**Because a literal search over a purposefully-shaped tree is a serious retrieval
strategy, not a fallback.** A 2026 study of agentic search, *Is Grep All You Need? How
Agent Harnesses Reshape Agentic Search* ([arXiv:2605.15184](https://arxiv.org/abs/2605.15184),
Sen, Kasturi, Lumer, Gulati and Subbiah), compared grep-based retrieval against vector
retrieval across agent harnesses on 116 LongMemEval questions and found grep generally
scored higher in that comparison. In their Table 1, grep-based inline retrieval won
its vector counterpart in every model-harness pairing tested, up to 93.1% against
83.6% with the strongest pairing, while the *same model* swung from 93.1% to 76.7% on
harness choice alone. Their own caveat matters and is repeated here: results
"depend strongly on which harness and tool-calling style is used." This is not *vector
search is bad*. It is that the shape of your file tree **is** your retrieval index, and
it is a design surface most people leave on the floor.

Three more reasons, none of them about benchmarks:

- **Auditability.** You can read the entire memory. Every claim names where it came
  from, every change is a diff, and when the agent says something wrong you can find the
  exact line that made it wrong.
- **Zero infrastructure.** Markdown in a git repo. Nothing to host, nothing to migrate,
  no embedding model to re-run when it gets deprecated.
- **Model-agnostic and portable.** The memory is not coupled to a vendor, an SDK or a
  schema version. Swap the model and the memory is untouched.

---

## The one rule

**Memory holds claims. Everything else holds artifacts.**

A **claim** is a sentence that can be true or false and can go stale.
An **artifact** is a thing; it has no truth value and becomes irrelevant instead.
The boundary is *what the thing is and who maintains it*, **never what it is about.**

That last line is the whole design. Sort by subject and every file about one customer
has two defensible homes; sort by what it *is* and there is exactly one right answer.

---

## Quickstart

```bash
git clone https://github.com/pretending2bejhon/ulpia.git
cp -r ulpia/skeleton my-vault
cp ulpia/docs/conventions.md my-vault/00-core/meta/conventions.md
```

Then:

1. Fill every angle-bracketed slot in the copied tree: OPERATOR, AGENT-NAME, VENTURE
   and the `<YYYY-MM-DD>` dates. `CLAUDE.md` carries most of them; `00-core/hot.md` and
   the `_*-index.md` files carry the rest.
   `rg '<[A-Z]' my-vault --glob '!00-core/meta/**'` lists every one. Everything under
   `00-core/meta/` keeps its slots, because templates are blank forms and the spec's
   examples are examples. Then delete the setup paragraph in `CLAUDE.md`.
2. Give the copied `conventions.md` its front-matter. Its opening lines say exactly
   what to add.
3. Point your agent at the vault. Any agent that can read and write files will do. If
   yours auto-loads a different boot filename (`AGENTS.md`, `GEMINI.md`), rename
   `CLAUDE.md` to match; nothing inside it is agent-specific.
4. First session: it reads `CLAUDE.md`, then `00-core/hot.md`, and nothing else until
   the task needs more. Capture into `10-memory/inbox.md` whenever placement is
   unclear. Sorting it out is consolidation's job, not yours.

That is the whole setup. Everything below is explanation.

---

## The typed tree

Numbers are spaced by ten so a tier can be inserted without renumbering.

| Tier | Holds | Loaded |
|---|---|---|
| `00-core/` | identity and conventions: config, not memory | boot file only |
| `10-memory/` | **claims**, agent-maintained | on demand |
| `20-ventures/` | work artifacts, five folders per venture | on demand |
| `30-personal/` | personal artifacts | on demand |
| `40-artifacts/` | rendered output a human reads | on demand |
| `90-machinery/` | code that runs | **by name only** |
| `95-data/` | bulk and raw external input | **by name only** |
| `99-archive/` | dead material | never |

**Below 90 is knowledge. 90 and above is not.** Machinery is code and raw input is
attacker-controllable text, so neither enters a session by accident. The boundary is a
number in a folder name, which means an agent enforces it without reading the contents.

Kind is carried by a filename suffix, never by a second folder layer:

| Suffix | Type | Answers |
|---|---|---|
| `__fact` | fact | what is true right now |
| `__log` | log | what happened |
| `__sop` | sop | how to do it, and what breaks when you don't |
| `__plan` | plan | what is intended |
| `__decision` | decision | what was ruled, and what was rejected |
| `__entity` | entity | stable facts about a person, org or thing |
| `__hub` | hub | one page linking the scattered parts of one system |
| `_*-index.md` | index | the routing table for one folder |

The double underscore is deliberate: it does not break into separate words for a
word-boundary matcher, so `rg --files -g '*__sop.md'` is exact. Full mechanics in
[docs/conventions.md](docs/conventions.md) §5.

### What a note actually looks like

```yaml
---
title: Kiln capacity model
type: fact
domain: studio
status: active
created: 2026-05-04
reviewed: 2026-05-04
review_interval_days: 30
owner: Mara Voss
source: "[[2026-05-02_cafe-lumen-first-order__log]]"
source_type: internal
load_bearing: "The old kiln completes four firings in a normal week."
load_bearing_status: UNVERIFIED
verify: 'rg -q "four firings a week" 10-memory/procedural/studio/bisque-firing-schedule__sop.md'
tags:
  - type/fact
  - domain/studio
  - kind/model
---
```

And the body opens with the line that matters, above the arithmetic:

> **Falls over if:** the old kiln does not complete four firings in a normal week.
> `UNVERIFIED`.

**Any number that governs a decision names its own breaking assumption first**, and
carries a `verify:` your nightly loop executes, so a gate nobody checked becomes
a row in tonight's report instead of silence. A caveat at the bottom of a note arrives
after the reader has already believed the number. The whole file is
[in the example vault](example-vault/10-memory/semantic/studio/kiln-capacity-model__fact.md).

---

## A tour of the example vault

[`example-vault/`](example-vault/) is a complete, working vault belonging to **Mara
Voss**, a fictional solo ceramicist. She sells wholesale to cafés and teaches a Saturday
class; her agent is called Clay. Over one month a café places a first wholesale order,
the order strains her single kiln, and everything that follows is recorded the way this
method says to record it: a capacity model, a decision, a supplier switch, an autumn
plan.

**She is invented. No real business, person, place or price appears anywhere in this
repository.**

Five files worth opening, each for one convention:

- [`kiln-capacity-model__fact.md`](example-vault/10-memory/semantic/studio/kiln-capacity-model__fact.md):
  **the falls-over line.** A number that governs a decision names the assumption under
  it on body line one, and carries a `verify:` your nightly loop executes.
- [`glaze-supplier-north-shore__fact.md`](example-vault/10-memory/semantic/suppliers/glaze-supplier-north-shore__fact.md):
  **validity, not patching.** A claim that stopped being true keeps its own text and
  gets `superseded_by:`. Compare it against
  [its successor](example-vault/10-memory/semantic/suppliers/glaze-supplier-hearthline__fact.md).
- [`2026-05-02_cafe-lumen-first-order__log.md`](example-vault/10-memory/episodic/2026-05/2026-05-02_cafe-lumen-first-order__log.md):
  **typed observation lines.** `- [decision]`, `- [constraint]`, `- [risk]`, so that
  one search returns every decision ever made.
- [`clay__hub.md`](example-vault/10-memory/semantic/systems/clay__hub.md):
  **a system is a `domain:` value, never a folder**, because its parts have different
  lifecycles. The hub reassembles it with one query.
- [`CLAUDE.md`](example-vault/CLAUDE.md):
  **the boot file**, filled in: load order, the one rule, the write policy.

---

## Lessons from the first months

Seven things this cost to learn. They are the reason the rules above look the way they
do, and every one of them failed **silently** the first time. They are field notes from
one operator's production vault (n = 1, no control group); take the shape, not the
decimals.

**Six processes writing one file produce fewer files than success messages.** Lost
writes do not error. They are reported as recorded, which is strictly worse than a
crash: a crash gets noticed. One working file per session, never a shared one.

**Discipline is not a control where a mechanical gate is possible.** A verification rule
followed by hand held **27 times out of 55**, about half. That number is why load-bearing
claims carry a `verify:` key a script executes, instead of a convention everyone agrees
with and forgets under pressure.

**A cache that is not overwritten is a log with a misleading name.** One stale section
sat in a session's always-loaded context and cost roughly **11% of every boot** before
anyone noticed, because nothing about a stale line looks different from a fresh one.

**An unscoped search returns several times the noise of a scoped one.** On the earlier
layout it measured three to ten times, which is precisely what motivated the
restructure into tiers. The search space is a design surface. You are already paying for
it; you may as well shape it.

**A rule that names a path from outside the thing it governs fails open when the path
moves.** Matching nothing is a valid result, not an error, so the rule keeps reporting
success while permitting exactly what it existed to prevent. Put the rule inside the
thing it protects.

**Renames orphan every inbound link and nothing errors.** Editors rewrite wikilinks only
when the editor itself performs the rename. A rename is a three-step transaction: move,
rewrite inbound links, update the index.

**Two taxonomies over one corpus is how structure rots.** Split a folder on two axes at
once and every new file gets two defensible homes. Half end up in each, and no single
search finds them all. One axis per folder level, chosen because it answers the most
queries.

---

## Honest comparison

Ulpia is not a competitor to embedding-based agent memory. It is a different trade, and
here is the trade:

| | Ulpia (files) | Embedding-based memory |
|---|---|---|
| Verbatim recall of what you wrote | strong; you get the file, not a paraphrase | weaker; retrieves by similarity |
| Similarity across different wording | weak; a literal search needs shared words | strong; this is the whole point |
| Auditability | read the whole store, diff every change | opaque by construction |
| History | git, free | needs to be built |
| Infrastructure | none | a service, a model, a schema |
| Scale across many users | poor | strong |
| Synthesis over thousands of documents | poor | strong |
| Portability across models and vendors | total | coupled |

**Use both where each is strong.** A file vault for the things you must be able to
quote, audit and correct; a vector layer for fuzzy recall across a corpus too large to
read. What this method refuses to trade away is the property that makes memory
trustworthy: you can read all of it and know exactly why it said what it said.

---

## Benchmarks, honestly

This section leads with a result that went against the author's design. That is
deliberate. A self-run benchmark is only worth reading if it can come back negative,
and this one did.

### A three-arm self-run, August 2026

**Setup.** [LongMemEval-S](https://arxiv.org/abs/2410.10813): 498 of its 500
questions, scored identically in every arm (2 questions are excluded by name because
they cannot pass the harness's gold-isolation gates by design; the exclusion list is
versioned and identical across arms). Actor model `claude-sonnet-5` in all three arms.
The judge is an LLM judge, also `claude-sonnet-5`, in the harness's local mode, which
is not the benchmark's official judge. Three arms over the same pseudonymized
transcripts:

- **vault_agent.** An agent with read and search tools over an Ulpia-shaped vault
  built from the transcripts: dated episodic tree, indexes, front matter, `hot.md`.
- **flat_grep.** The same agent, same tools, same episode caps, over a flat folder of
  the same transcripts: no front matter, no `hot.md`, no indexes, no dated tree. The
  system prompt is byte-identical except one line.
- **control_rag.** One-shot embedding retrieval: the question is embedded, the
  highest-scoring chunks go into a single prompt, one answer call, no agent loop.

The reading was pre-registered before the flat arm ran: vault ahead of flat at
p < 0.05 means the structure pays; flat ahead means the structure costs; neither
means the two are not detectably different at this sample size.

**Result.**

| Arm | Score | Cost (shadow estimate) | p50 latency | Median tool calls |
|---|---|---|---|---|
| vault_agent | 389/498 = 78.1% | $117.37 | 18.14 s | 5 |
| flat_grep | 374/498 = 75.1% | $92.08 | 14.64 s | 3 |
| control_rag | 272/498 = 54.6% | $34.24 | 5.94 s | one-shot |

McNemar exact, two-sided: vault_agent vs control_rag p = 1.23e-22, flat_grep vs
control_rag p = 1.84e-15, **vault_agent vs flat_grep p = 0.18**. Costs are shadow
estimates recorded by the harness; the runs were billed to a subscription, so they
are not invoiced API spend.

**The pre-registered third branch fired: at n = 498, Ulpia's structure is not
detectably different from agentic grep over a flat pile of transcripts.** The raw gap
is 3.0 points (discordant pairs: 62 vault-only against 47 flat-only), inside the noise
the pre-registration named.

Read that with the control arm in view. The 23.5-point win over one-shot RAG does not
belong to Ulpia's structure: the flat arm keeps 20.5 of those points with no dated
tree, no indexes, no front matter and no `hot.md`. On this benchmark, at this n, the
gain is the agent loop plus lexical search over readable files, not this method's
organisation of them.

The structure also costs. The vault agent spends its opening calls walking `hot.md`
and the indexes before its first search; the flat agent's first call is the search.
On this workload that orientation was a fixed tax the tree never earned back: $117.37
against $92.08, p50 latency 18.14 s against 14.64 s, a median of 5 tool calls per
question against 3 (2,802 total against 1,475).

Per question type (cells this small cannot power a per-type claim; read these as
directions to test, not findings):

| Question type | n | vault_agent | flat_grep | control_rag |
|---|---|---|---|---|
| knowledge-update | 78 | 80.8% | 80.8% | 53.8% |
| multi-session | 133 | 60.9% | 61.7% | 36.1% |
| single-session-assistant | 56 | 100.0% | 98.2% | 96.4% |
| single-session-preference | 30 | 60.0% | 63.3% | 50.0% |
| single-session-user | 70 | 91.4% | 81.4% | 77.1% |
| temporal-reasoning | 131 | 81.7% | 74.8% | 45.0% |

The structure led in two types: temporal-reasoning (81.7% against 74.8%), the dated
tree's home ground, and single-session-user (91.4% against 81.4%). It trailed or tied
elsewhere.

### What these numbers are not

**Not comparable to any published LongMemEval result, including the table below.**
This is a within-study comparison: same harness, same actor model, same local judge,
same exclusions, across three arms. Change any of those and the numbers move. The
harness enforces this position on itself: every run artifact records
`comparable_to_published: false`, with the blocking reasons named (local judge mode,
the exclusion list, the harness's own listed deviations). In particular, flat_grep's
75.1% sitting near the Letta filesystem agent's 74.0% below is a coincidence of
digits: different benchmark (LongMemEval-S here, LoCoMo there), different models,
different judge, different harness. It is not a head-to-head, and reading it as one
is exactly the error this section exists to prevent.

**A solo self-run, on the author's own thesis.** One operator built the memory
method, built the harness, and ran the benchmark. Every bias critics attribute to
self-run numbers applies here. What this run has going for it is its direction: the
headline finding is against the thesis, and it leads this section anyway.

**Not yet reproducible by a stranger.** The harness is a private repository as of
this writing. Pinned commits, per-question result files, cost ledgers and
gold-leakage isolation scans (0 hits on all three arms) exist, but privately. Until
the harness or the per-question artifacts are published, these numbers are the
author's report, not evidence you can check. The publication commitment in the list
at the end of this section still stands and is not discharged by this text.

**A single sample.** Each question was answered once per arm. p = 0.18 is a
single-sample point estimate; variance under repeated sampling is unmeasured.

### What survives, and what does not

A retrieval benchmark sees only the retrieval half of a memory method. What this one
measured: an agent loop with lexical search over readable files beat one-shot
embedding RAG by 23.5 points, found twice here, once through the vault and once
through the flat pile. What it failed to find: any accuracy payoff for Ulpia's
structure over flat files at this sample size. What it did not measure at all:
placement determinism, auditability, supersession, `verify:` lines, consolidation,
or what happens to a store that is maintained for months rather than ingested once.
Those are the claims the rest of this README argues, and after this run they remain
argued, not benchmarked. The retrieval claim this repository can still make honestly
is narrower than before: files plus grep plus an agent is a strong retrieval
baseline, and the tree's shape is not yet shown to add accuracy on top of it.

### The published landscape

**None of the numbers below was produced by Ulpia.** Every score is the publisher's
own reported result under its own eval setup. Actor model, judge and question subset
all differ per row, so a row compares against the baseline in its own source, never
against another row, and never against the self-run above. Where a vendor and a third
party disagree, both numbers appear.

The two benchmarks the field actually uses:

- **LongMemEval** ([arXiv:2410.10813](https://arxiv.org/abs/2410.10813), ICLR 2025,
  MIT) has 500 questions over long chat histories: information extraction,
  multi-session and temporal reasoning, knowledge updates, and abstention.
- **LoCoMo** ([arXiv:2402.17753](https://arxiv.org/abs/2402.17753), ACL 2024,
  CC BY-NC) is QA over very long multi-session dialogues. Standard practice runs four
  of its five categories; the fifth ships without gold answers.

| Benchmark | System | Published score | Actor model | Source |
|---|---|---|---|---|
| LongMemEval-S | Full context, no memory system | 60.2% | gpt-4o | [arXiv:2501.13956](https://arxiv.org/abs/2501.13956) |
| LongMemEval-S | Zep / Graphiti | 71.2% | gpt-4o | [arXiv:2501.13956](https://arxiv.org/abs/2501.13956) |
| LongMemEval-S | Mastra observational memory | 84.23% | gpt-4o | [mastra.ai](https://mastra.ai/research/observational-memory) |
| LongMemEval | Mem0, own 2026 eval setup, self-published | 94.4 | not stated | [mem0.ai/research](https://mem0.ai/research) |
| LoCoMo | Mem0 | 66.88 J | gpt-4o-mini | [arXiv:2504.19413](https://arxiv.org/abs/2504.19413) |
| LoCoMo | Mem0, graph variant | 68.44 J | gpt-4o-mini | [arXiv:2504.19413](https://arxiv.org/abs/2504.19413) |
| LoCoMo | Zep, as configured by Mem0 | 65.99 J · disputed | gpt-4o-mini | [arXiv:2504.19413](https://arxiv.org/abs/2504.19413) |
| LoCoMo | Zep, own corrected run | 75.14 J | gpt-4o-mini | [getzep.com](https://blog.getzep.com/lies-damn-lies-statistics-is-mem0-really-sota-in-agent-memory/) |
| LoCoMo | **Letta filesystem agent (markdown files + search tools)** | **74.0%** | gpt-4o-mini | [letta.com](https://www.letta.com/blog/benchmarking-ai-agent-memory/) |

*Scores marked J use the Mem0 paper's LLM-judge metric; percentage rows use each
source's own accuracy metric. The two are not interchangeable. Paper rows are pinned by
arXiv id; vendor-page rows were retrieved August 2026, and vendor pages can change.*

The row that matters most here is the last one: a third party with no stake in files
put an agent over plain markdown files with search tools on LoCoMo, and it landed above
the dedicated graph-memory variant in the same comparison. Files with a good harness
are a competitive memory substrate, measured by someone else.

**Why these numbers disagree, and why you should care.** The two loudest vendors in
this field measured each other and got different answers: Mem0's paper scored Zep at
65.99; Zep re-ran it with a corrected configuration and published 75.14. A
[third-party audit of LoCoMo](https://github.com/dial481/locomo-audit) found 6.4% of
its ground truth broken (99 of 1,540 questions) and the standard LLM judge accepting
62.81% of deliberately wrong-but-topical answers. And both corpora fit inside
a frontier model's context window, which is why a full-context baseline with no memory
system at all is competitive in every table. (DMR, the earlier standard, is omitted
entirely: a full-context baseline scores 98.0% on it, per
[arXiv:2501.13956](https://arxiv.org/abs/2501.13956).) Read every leaderboard in this
field, including any future one in this repo, with all of that in mind.

**Still open before any number above graduates:**

- At least one **external memory system** run through the same harness as a control.
  The control_rag arm is a self-built baseline, not an external system, so the
  self-graded-homework objection stands until a system the author did not build runs
  through the same gates.
- **The harness published**, with transcripts, pinned commit, actor and judge models,
  per-question results and total cost, so a stranger can reproduce every number in
  the self-run.
- The benchmark's **official judge** run alongside the local judge.
- **Repeated sampling** to put variance around the single-sample p = 0.18.
- **LoCoMo** under its research-only license, four of five categories, clearly
  framed.

Until those land, read the self-run as one operator measuring his own method with
the weaknesses stated, and read the published table as the state of the field. The
sections above this one are the argument; nothing in this section strengthens them,
and one result in it weakens one of them.

---

## Where this sits

Three 2026 results give the design bets in this repo named counterparts:

- **The harness learns; the weights stay frozen.** *Continual Harness* (Karten et
  al., [arXiv:2605.09998](https://arxiv.org/abs/2605.09998)) formalizes an agent that
  improves by editing its own prompt, memories, skills and subagent specs: an act
  phase, then a separate refine phase that rereads raw trajectories and consolidates,
  all over plain files, weights untouched. That act/refine split is this method's
  session/consolidation split, and the confidence grades their memory entries carry
  are the same instinct as this method's belief machinery.
- **Long context as an environment, not a prompt.** *Recursive Language Models*
  (Zhang, Kraska & Khattab, [arXiv:2512.24601](https://arxiv.org/abs/2512.24601))
  keeps long context outside the window as a variable the model programs over. Left
  free to choose a strategy, frontier models converge on peeking at structure
  first, then grepping. A typed tree is that environment made durable, and the
  nightly loop pre-computes at consolidation time what an RLM pays for again at every
  query.
- **Both, productized.** Prime Intellect's
  [prime-agent](https://github.com/PrimeIntellect-ai/prime-agent) (MIT, 2026) ships
  both ideas, with memory held in a machine-edited JSON state file refined between
  episodes. Ulpia is the same thesis on the opposite substrate: memory a human can
  read, diff and correct line by line, with truth maintenance at *read* time
  (supersession, review dates, a `verify:` line a script executes), not only quality
  control at write time.

---

## Documentation

| Read this | For |
|---|---|
| [docs/memory-model.md](docs/memory-model.md) | why memory splits into four types, and what goes in it |
| [docs/conventions.md](docs/conventions.md) | naming, front-matter, placement, growth, lint: the mechanics |
| [docs/consolidation.md](docs/consolidation.md) | the nightly loop: draining, promotion, belief decay |
| [skeleton/](skeleton/) | the empty tree your vault starts as a copy of |
| [example-vault/](example-vault/) | all of it, demonstrated |

---

MIT licensed. See [LICENSE](LICENSE). Built and maintained by Jhonalbert Alvarez, for
one working agent, before it was for anyone else.
