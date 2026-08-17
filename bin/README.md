# bin/

Source of truth for the `clv` tools. Deployed to `~/.collevity/bin` on the mini.

| Tool | Role |
|---|---|
| `clv` | the identity seam — `whoami`, `env` |
| `clv-login` | SSH forced-command entry point, sets the principal |
| `clv-dwd` | Workspace domain-wide-delegation, mints subject tokens |
| `clv-dwd-verify` | standalone DWD check, does not mutate token state |

Imported 2026-08-16 from the live mini (T2), verbatim and byte-identical to
what was running there. **The repo is now the source; the mini is a deployment.**
Change tools here and pull, never edit `~/.collevity/bin` in place — an edit on
the mini is a fork that this README cannot see.

Deployment paths are hardcoded to `/Users/nascentech/...` and `clv-dwd` carries
`ADMIN_SUBJECT = "levi@nascentech.com"`. That is fine for the single-mini v0;
it is what has to change before a second host exists.
