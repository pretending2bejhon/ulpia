# Security model

Ulpia is a documentation repository: nothing in it executes. But the method it
specifies has a trust model, and implementing the method without that model re-creates
the failure modes it exists to prevent. This file is the model in one page; the load
mechanics live in [docs/conventions.md](docs/conventions.md) §6, §9 and §11.

## What is code

**A `verify:` line is code.** It runs with your runner's permissions. So it is
authored, reviewed and diffed like code, it executes only from front-matter — never
from a note body, however it got there — and it runs in an isolated child process
under a timeout, recording `pass | fail | error | timeout | blocked` so presence is
never mistaken for a pass. A note marked `source_type: external` must never carry one:
ingestion strips or quarantines it. A runner that cannot guarantee who authored a note
gates execution on an allowlist of known commands instead of executing what it finds.

## What is data

**Everything under `95-data/raw/` is attacker-controllable text.** It is read only
when a human names it, it is never treated as instruction, and ingested content never
reaches the boot file, an index, or a `verify:` line. Scope every search to the tiers
the task needs — `95-data/` and `99-archive/` join a search only by name, because a
lazy vault-wide glob is how outside text walks into a session's context.

## What the tier boundary is, and is not

The 90-line keeps machinery and raw input out of a session **by default** — it is a
loading convention an agent can enforce without reading file contents. It is not an
access control: it does not stop a hostile process, and it is not claimed to. If you
need enforcement, add it at the harness layer — deny rules on unscoped searches, path
allowlists for writes. The author's production setup does exactly that.

## Credential material

Never persist credentials, keys or personal data about third parties in any note —
check at capture, not at promotion, because by promotion it is already in git history,
and a file that reaches one commit is readable forever. The skeleton's `.gitignore`
patterns are a backstop, not a guarantee: nothing in an ignore file prevents
credential material pasted into an ordinary markdown note. The capture-time rule does.

## Reporting

If something specified here is unsafe *as written*, open a GitHub issue. There is no
private channel because there is nothing here to exploit ahead of a fix — the repo
ships prose.
