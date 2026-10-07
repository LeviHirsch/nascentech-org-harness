# bin/

Source of truth for the `clv` tools. Deployed to `~/.collevity/bin` on the mini.

| Tool | Role |
|---|---|
| `clv` | the identity seam — `whoami`, `env` |
| `clv-login` | SSH forced-command entry point, sets the principal |
| `clv-stamp` | emits an attribution record for an action — reads identity only via `clv whoami`, writes to stdout, stores nothing |
| `clv-tmux` | joins the shared tmux session with a per-principal view; names the view from `clv whoami` |
| `clv-dwd` | Workspace domain-wide delegation as a subject (`--as <email>`): mail, calendar, tasks, Drive, Docs and Sheets reads and writes, Gmail labels, Drive sharing. Signs with the local service-account key |
| `clv-dwd-verify` | standalone DWD proof for two or more subjects, does not mutate anything |
| `clv-dwd-health` | walks the DWD chain rung by rung so a failure localises; `--write` adds a create/delete round-trip |
| `clv-dwd-watch` | stdlib-only watchdog for a timer: the key still signs and the Admin grant still honours an impersonation; appends to a log, `--status` reads it back |
| `clv-dwd-selftest-writes` | self-cleaning write test for a `clv-dwd` build (Drive, Docs, Sheets, calendar, tasks, drafts) |
| `clv-dwd-selftest-labels-sharing` | self-cleaning test for a `clv-dwd` build's label and sharing commands |

Imported from the live mini, verbatim and byte-identical to what was running
there: first on 2026-08-16 (T2), again on 2026-10-07 (T91) after the mini had
moved ahead of the repo. **The repo is the source; the mini is a deployment.**
Change tools here and deploy, never edit `~/.collevity/bin` in place — an edit on
the mini is a fork that this README cannot see, and the next `./deploy-mini
--apply` rolls it back.

The two selftests default to testing `~/.collevity/bin/clv-dwd.next`, a staged
build, not the live tool. Pass the path to test something else.

No tool holds a secret. The DWD tools read the service-account key from
`~/.collevity/auth/_service/sa-key.json` on the mini; that file is not in this
repo and must never be.

Deployment paths are hardcoded to `/Users/nascentech/...` and `clv-dwd` carries
`ADMIN_SUBJECT = "levi@nascentech.com"`. That is fine for the single-mini v0;
it is what has to change before a second host exists.
