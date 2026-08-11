# CLAUDE.md — you are reading a spec, not a vault

**This repository is Ulpia: a template and a written method for agent memory. It is not
itself a memory store.** Nothing here is a fact about the person who cloned it. Do not
answer questions from it as though it were their vault, and do not write notes into it.

This file is also a working demonstration of the pattern it teaches: one small always-
loaded boot file, one cache of what is true now, everything else pulled on demand.

## What is in here

```
README.md          the argument, the quickstart, the honest comparison
docs/              the method in full — read on demand, not up front
skeleton/          the empty tree a new vault is a copy of
example-vault/     a complete worked vault, entirely fictional
```

## Instantiating a vault from this repository

Do these four things in order. They are tool-agnostic: any agent that can read and write
files can do them.

1. **Copy `skeleton/` to wherever the vault will live.** The copy is the vault. Do not
   work inside this repository.
2. **Copy `docs/conventions.md` into the new vault at `00-core/meta/conventions.md`.**
   The skeleton deliberately ships without it so there is one source of truth per repo;
   every pointer in the skeleton expects it at that path once copied.
3. **Fill every angle-bracketed slot in the vault's `CLAUDE.md`** — OPERATOR (whose brain
   this is), AGENT-NAME (what the maintaining agent is called) and VENTURE (one line of
   work). Then delete the setup paragraph. The same slots appear in `00-core/hot.md`,
   `10-memory/_memory-index.md` and the six templates under `00-core/meta/templates/`.
4. **Ask the operator for the first `hot.md`.** Three to five lines of what is true right
   now. A vault whose cache is empty has no boot path, and the boot path is the method.

## Read order once a vault exists

```
every session   CLAUDE.md  →  00-core/hot.md
on demand       10-memory/_memory-index.md  →  _<type>-index.md  →  the page
by name only    90-machinery/   95-data/   99-archive/
```

Do not preload the docs. Reach for them by task:

| Task | Read |
|---|---|
| Deciding where a file goes | `docs/conventions.md` §3 |
| Naming a file, writing front-matter | `docs/conventions.md` §5–§6 |
| Understanding what memory is for | `docs/memory-model.md` |
| Wiring or running the nightly loop | `docs/consolidation.md` |

## The one rule, so you have it without reading further

**Memory holds claims; everything else holds artifacts.** A claim can be true or false
and can go stale. An artifact has no truth value and becomes irrelevant instead. The
boundary is what the thing is and who maintains it, never what it is about.

## Rules that bind you while you are in this repository

- **`example-vault/` is fiction.** Mara Voss, her studio, Café Lumen, her suppliers and
  every price and date in that tree are invented. They are there to demonstrate
  conventions. **Never present anything from `example-vault/` as a fact about a real
  person, business or product**, and never copy its content into a real vault — copy
  `skeleton/` instead.
- **Do not edit `docs/` to match one user's preferences.** It is the published method. A
  vault-specific deviation belongs in that vault's own `00-core/`, not here.
- **Do not add a `conventions.md` to `skeleton/`.** Step 2 above exists so the method has
  exactly one copy in this repository.
- **The angle-bracketed slots remaining in `skeleton/` are intentional** and must survive
  until a user fills them. Do not "helpfully" resolve them inside this repository.

## If you are asked to extend the method

Say what it costs. Every rule in `docs/conventions.md` earns its place by naming the
tool behaviour that breaks without it — a rule that cannot name its failure mode is a
preference, and preferences belong in a vault, not in a spec.
