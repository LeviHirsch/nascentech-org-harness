# collevity-org-harness

A context-building and retrieval engine for an organization. It identifies a
person at entry and routes behavior — including AI interaction — by who they
are. NascenTech is the first full-stack example.

Deployed to a Mac mini (`nt`) running a **single shared macOS account**. Every
session is uid 502. The harness supplies attribution, not isolation.

## The load-bearing rule

**Read identity only through `clv whoami`.**

Never `$USER`, never the SSH key, never a roster file, never an env var you set
yourself. `clv whoami` returns `principal`, `method`, and `subject`, and exits
non-zero when no identity is established.

The substrate behind it will change — per-person SSH keys today, Cloudflare
Access short-lived certs later. Consumers must not notice. Any code that
derives identity another way is a bug, even when it happens to be correct.

`method: none` is **first-class**. An unattributed action is a real, allowed
state. Label it; never quietly attribute it to a person.

## Vocabulary

Fixed before any store was written. Use these words exactly.

| Term | Meaning |
|---|---|
| `principal` | who authenticated |
| `actor` | who acted — may be an agent |
| `subject` | whose Google account is being acted as (DWD impersonation target) |
| `method` | how identity was established (`ssh-key`, `declared`, `none`, …) |

Every record carries how identity was established, not just who. An agent
writing on someone's behalf sets `actor` to the agent and `provenance` honestly.
**Never stamp `author: user` for an agent write** — that is the Dropper D-002
defect, and this is where it would become org-scale infrastructure.

## Layout

```
bin/        clv, clv-login, clv-dwd — source of truth, deployed to ~/.collevity/bin
AGENTS.md   this file (CLAUDE.md is a symlink to it)
.tasks/     dev task board — lives on the integration branch only
```

No `docs/` sprawl. Docs live next to what they describe.

## Invariants

Decided rules, not preferences. Each was ruled deliberately; overturn only
with an explicit decision, not by drifting.

**Identity and permissions**
- User and org paths are **convention, not isolation**. The OS does not
  separate people here. Do not build an ACL product; do not imply one exists.
- The harness refuses to act as a Google subject other than `whoami`, unless
  the integrator overrides explicitly — and the override is logged.
- Keep the auth planes distinct. `clv whoami` (principal) · `clv-dwd`
  (Workspace subject) · `workspace-mcp` OAuth (`mini@` only) · the model API
  key (the model account). An agent's API call is **not** acting as the user.

**Nodes**
- A node is a directory. Its address is its **full path** — bare names are not
  addresses, and there is no uid.
- Work happens **inside** the node directory. Agent and human share one
  surface; never create a parallel agent-workspace tree.
- Rollups and derived summaries are **views** — regenerable from children,
  never sources of truth.
- Spec-level work happens in the node directory. Do not stretch `/spec` into
  strategy; do not build `/strategy` or `/scope` skills.

**Capture**
- *Ingestion* is any input. *Digestion* is the full breakdown. They are
  different operations; do not collapse them.
- Captured think-and-toss items may exist and still be excluded from rollups.
  The state is **not-promoted** — not a demotion.
- Intent lives on Calendar/Tasks (projection). What happened lives in the
  action log (actual). Keep them as separate records; never smash them into one.

**Agents**
- Agents attach through the **CLI** (`clv`, `clv-dwd`, hooks). No embedded LLM,
  no vendor SDK as the harness itself. Any CLI agent can drive it.

**Boundaries**
- The personal suite (`/checkin`, `/transcribe`, the personal laptop lake) is
  **not** an org default. Offer; never pull it in.

## Gotchas

These cost real time. They are not obvious from the code.

- **Google Drive FS enumerates lazily.** One `find` is not an inventory — the
  same unchanged tree reported 697 files then 2464. Walk twice and require
  agreement before trusting a count.
- **`~/00_NASCENTECH` is a symlink.** A bare `find` returns nothing. `cd` first,
  or use `find -L`.
- **`clv` names two different programs.** On the laptop it is a personal flat
  CLI (`list/add/show/rm`). On the mini it is the identity seam
  (`whoami`/`env`). Same path, different machines — always say which.
- **This repo's `.git` is a file, not a directory**, pointing outside iCloud.
  iCloud corrupts live `.git` directories. Never convert it back to a folder,
  and note that `gh repo create --source=.` fails on this pattern.
- Comparing manifests across machines: `LC_ALL=C sort` both sides.
  `rsync -n --stats` always reports "0 transferred" — use `--itemize-changes`.

## Out of scope

Named so they are not re-litigated. Their seams exist; the products do not.

- A client UI — a read-seam only, so a UI can arrive later without a rewrite
- A graph database — proximity is scored without one
- An ACL product — see the isolation invariant above
- A Dropper rewrite — the personal lake stays on the laptop
