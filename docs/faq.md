# FAQ

Common errors and questions, with the actual fix.

## Authentication

### Q: I authenticated but every tool call returns empty data.

The OAuth account you signed in with is **not** the OKX account that is enrolled as an
Affiliate. The MCP only sees data for the connected affiliate.

**Fix:**
1. Sign out / revoke the wrong account at OKX → *Settings → Connected apps*.
2. Re-run your client's MCP auth command (`/mcp` in Claude Code).
3. Sign in with the correct affiliate account on the OKX consent page.

### Q: My tool calls return 401 hours after install.

The OAuth `access_token` lifetime is approximately **1 hour**. Native-OAuth clients refresh
automatically — if you still see 401s after a retry, your client may have lost the
`refresh_token`.

**Fix:** Re-run your client's MCP auth command. You will breeze through the consent screen
without any prompts because OKX still has the grant on file.

### Q: I see `invalid_grant: resource does not match`.

Your MCP client is not adding the required `resource` parameter to OAuth requests
([RFC 8707](https://datatracker.ietf.org/doc/html/rfc8707)).

**Fix:** Use a client that handles this natively (Claude Code, Codex, Hermes, Cursor are all
known to work). For OpenClaw, use the [skill workaround](install/openclaw.md).

### Q: How do I revoke the OAuth grant?

OKX → *Settings → Connected apps* → find `growth-affiliate-pro-tools` → *Revoke*. The
`access_token` and `refresh_token` will both stop working immediately.

---

## Tool errors

### Q: `affiliate-pro-performance-summary` returns 400 Bad Request.

You probably passed `pageType=2`. The endpoint only supports `pageType=1` (the default).

**Fix:** Drop the `pageType` argument entirely, or set it to `1`.

### Q: `affiliate-pro-invitee-list` errors with "page is required".

`page` is the only required argument on this tool — even `page=1` must be set.

**Fix:** Always pass `{"page": 1, ...}` at minimum.

### Q: I get 429 Too Many Requests.

You are hitting the per-account rate limit. Bursting more than ~10 requests per second to
this MCP returns `code: 50011`.

**Fix:** Space your calls (a 200-500ms delay between requests works for most workloads). If
you are pulling a large multi-month trend, prefer fewer larger ranges over many small daily
calls.

### Q: A tool returns 200 but `data` is empty.

Two common causes:

1. The connected affiliate genuinely has no data in the requested window (very young
   affiliate, or a `periodType` that is too narrow).
2. The OKX account is not enrolled as an Affiliate — see "empty data" Q above.

---

## Scope and permissions

### Q: Should I grant Trade / Earn / Asset Transfer scopes?

**No.** This MCP is read-only. Granting write scopes is unnecessary and only widens the
blast radius if your token leaks. The recommended config is *Live Trading → Read-only* and
nothing else.

### Q: Can I use my Demo trading account?

The MCP server does not currently expose demo affiliate data. The OAuth flow lets you toggle
the *Demo trading* scope, but tool calls will return empty for demo-only accounts. Use a
live affiliate account.

---

## Data semantics

### Q: Are the timestamps UTC?

Yes. `relateTime`, `firstTimeTraded`, `kycVerifiedTime`, `lastUpdatedTime` are all Unix
**milliseconds** in UTC.

### Q: Are the financial fields period-scoped or lifetime?

It depends on the tool:

- `affiliate-pro-performance-summary` — period-scoped (`periodType` chooses the window).
- `affiliate-pro-invitee-list` — period-scoped per row (`deposited`, `fees`, `totalReward`,
  `totalTradingVolume` apply to the requested `periodType`).
- `affiliate-pro-invitee-detail` — **lifetime totals** (no `periodType` parameter), plus
  `volMonth` for the current calendar month.

### Q: Why are some fields decimal strings, not numbers?

To preserve precision. Affiliate balances and trading volumes can have many significant
digits; converting to a JSON number can lose precision in some clients. Always parse as
`Decimal` / `BigDecimal` if you do arithmetic on them.

### Q: How fresh is the data?

The `lastUpdatedTime` field on every response tells you when OKX last refreshed the
underlying aggregates. Snapshots typically lag real-time by 30–60 minutes for high-fanout
metrics like `traders` and `volume`. New invitees and deposits appear within a few minutes.

---

## Operational

### Q: Where is the token stored?

It depends on the client:

- **Claude Code** — encrypted in `~/.claude/state.json` (or platform keychain on macOS).
- **Codex / Hermes / Cursor** — see your client's docs; usually a per-app config dir.
- **OpenClaw (via skill)** — `~/.openclaw/data/okx-affiliate-mcp/token.json` in plain JSON.

### Q: Can I share the token with a teammate?

**No.** OAuth tokens are tied to a single OKX account. Each teammate completes their own
OAuth flow.

### Q: I changed the OKX password / 2FA. Do I need to re-auth?

OKX may invalidate active OAuth grants when you reset credentials. If you start seeing 401s
after a security change, re-run `/mcp` to mint new tokens.
