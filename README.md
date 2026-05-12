# OKX Growth Affiliate Tool

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

[English](README.md) | [中文](README.zh-CN.md)

A remote MCP (Model Context Protocol) endpoint that lets AI agents query the OKX Affiliate
portal — performance, invitees, links, sub-affiliates — through natural language, with no
manual API plumbing.

> **Endpoint:** `https://www.okx.com/api/v1/mcp/growth-affiliate-mcp`
> **Transport:** Streamable HTTP
> **Auth:** OAuth 2.0 (per-user, hosted by OKX)

## What it looks like

After the one-time OAuth handshake, you talk to your affiliate operations through the
agent in natural language. The agent picks the right tool, runs it, and answers like this:

```
You          ❯ 看下昨日节点数据

Agent        ❯ 📊 Yesterday (2026-05-10)
                 • Commission: $350.59 (Derivatives 98.5% / Spot 1.5%)
                 • Volume:     $3.50M | Deposits: $8,022
                 • Active traders: 77 | New invitees: 6

                 vs 7-day avg ($503/day) → yesterday was -30%, within band.
                 Today so far (1 h): $530 — pacing ahead.

                 Top 3 by rebate:
                  • UID …910040 (VIP 2)     $90.94 / $964K vol
                  • UID …301554 (VIP 1)     $50.31 / $526K vol
                  • UID …988366 (Regular)   $27.80 / $297K vol  ← qualifies for VIP1

                 ⚠️ Bag-sitters needing rescue in 24h:
                  • …033472 deposited $872, 0 trades
                  • …446587 deposited $464, 0 trades (repeat — also did this 5/6)
                  • …298402 deposited $420, 0 trades
```

```
You          ❯ Who should I reach out to this week?

Agent        ❯ 🎯 4 tiers, 33 total candidates:

                 🔥 P0 — Star activators (joined ≤ 7d, already producing)
                   …586237 (joined 4/29): $268 rebate, $2.0M vol, $5K deposit
                   …084083 (joined 4/29): $228 rebate, $1.7M vol, $4.7K deposit
                   → Fast-track to VIP1, give direct trading line. Today.

                 🐋 P1 — Regular users doing whale volume → upgrade pitch this week
                   …118301 $81 / $653K vol — joined 1/23
                   …575159 $69 / $506K vol — joined 4/26
                   …274299 $69 / $850K vol — joined 8/3
                   …370516 $87 / $743K vol — joined 12/12 (longest tenure)

                 ⚠️ P2 — Big bag-sitters (deposited 7d, didn't trade)
                   …181120 deposited $10,000, only $500 traded (8d idle)
                   …108384 deposited $7,293, $3K traded
                   $36K total fresh deposits idle. Window closing fast.

                 🆕 P3 — KYC verified, no deposit yet (13 users this week)
```

```
You          ❯ Which whales are slipping away?

Agent        ❯ ⚠️ Lifetime top 50 → only 23 still in last-30-day top 50.

                 🔴 Type A — cashed out (hard to win back)
                  • UID …893796 — $104K lifetime / withdrew 95% of $10.3M deposit
                  • UID …580269 — $37K lifetime / withdrew 76% of $6.2M

                 🟡 Type B — parked & idle (easiest win, capital still here)
                  • UID …699614 — $26K lifetime / withdrew only 6% of $1.47M
                    → DM today. Walk them through Flash Earn while they wait.

                 🟠 Type C — gradual decay (intervene now)
                  • UID …616246 — $18K lifetime / last 30d vol = 0
                  • UID …690681 — $11.6K lifetime / last 30d vol = 0

                 Combined LTV at risk: ~$208K (~46% of node's 12-month commission).
```

More scenarios → [Usage scenarios](#usage-scenarios) below.

## Quick start

Pick the row that matches your client. Each guide is a single command plus a 4-step OAuth flow.

| Client                          | Native MCP OAuth | Install guide                                                |
| ------------------------------- | :--------------: | ------------------------------------------------------------ |
| **Claude Code** (CLI)           | ✅               | [`docs/install/claude-code.md`](docs/install/claude-code.md) |
| **Codex CLI**                   | ✅               | [`docs/install/codex.md`](docs/install/codex.md)             |
| **Hermes**                      | ✅               | [`docs/install/hermes.md`](docs/install/hermes.md)           |
| **Cursor**                      | ✅               | [`docs/install/cursor.md`](docs/install/cursor.md)           |
| **Generic MCP client**          | ✅               | [`docs/install/generic.md`](docs/install/generic.md)         |
| **OpenClaw**                    | ❌ (use skill)   | [`docs/install/openclaw.md`](docs/install/openclaw.md)       |

> **OpenClaw note:** OpenClaw's built-in MCP runtime does not yet handle OKX's non-standard
> OAuth requirements (mandatory `resource` parameter and a non-discovery DCR endpoint). Until
> upstream support lands, OpenClaw users install via the bundled skill at
> [`skills/openclaw-affiliate-mcp/`](skills/openclaw-affiliate-mcp/) — a drop-in skill pack
> that walks the agent through the OAuth flow.

## Usage scenarios

Pre-built skill packs that teach an AI agent how to use the MCP for common analysis tasks.
Each one is a `SKILL.md` with trigger phrases, an MCP call sequence, sample output, and
recommended follow-ups — drop it into your agent's skill directory and it activates
automatically when the user's request matches the triggers.

| Scenario                                                                      | What the user says                                                  | What you get back                                       |
| ----------------------------------------------------------------------------- | ------------------------------------------------------------------- | ------------------------------------------------------- |
| [`daily-briefing`](examples/daily-briefing/SKILL.md)                          | *"Show me yesterday's data"* / *"morning report"* / *"节点日报"*       | EOD summary + same-day depositors + new invitees + 3 action items |
| [`high-potential-invitees`](examples/high-potential-invitees/SKILL.md)        | *"Who should I reach out to?"* / *"潜力用户"*                          | P0/P1/P2/P3 outreach tiers with concrete UIDs           |
| [`churn-rescue`](examples/churn-rescue/SKILL.md)                              | *"Which whales are slipping?"* / *"流失预警"*                          | At-risk users split into Type A (cashed out) / B (parked) / C (gradual) |
| [`whale-deep-dive`](examples/whale-deep-dive/SKILL.md)                        | *"Pull everything on UID X"* / *"prep me to DM this user"*            | Lifetime + recent profile, health signals, outreach hook |
| [`acquisition-trends`](examples/acquisition-trends/SKILL.md)                  | *"6-month acquisition trend"* / *"DAT history"* / *"拉新趋势"*         | Monthly table + peaks + inflection-point analysis        |
| [`tier-upgrade-candidates`](examples/tier-upgrade-candidates/SKILL.md)        | *"Who should I push to VIP1?"* / *"升级 VIP 名单"*                     | Regular-tier users doing whale volume, sorted by uplift  |

See [`examples/README.md`](examples/README.md) for the full index and contribution guide.

## Authorization scopes

When you connect, OKX will ask which scopes to grant. **For this MCP only the read paths are
needed**, so the recommended default is just **Live Trading → Read-only**:

| Scope               | Recommended | What it unlocks                                       |
| ------------------- | :---------: | ----------------------------------------------------- |
| `live:read`         | ✅          | All read tools below (performance, invitees, links…)  |
| `live:trade`        | ❌          | Place / modify / cancel orders — not used by this MCP |
| `live:earn`         | ❌          | Earn product subscriptions — not used by this MCP     |
| `live:asset_transfer` | ❌        | Transfer assets — not used by this MCP                |
| `demo:*`            | ❌          | Demo trading — not used by this MCP                   |

You can always re-run `/mcp` (or your agent's equivalent) to widen scopes later.

## Tools at a glance

| #  | Tool                                  | Purpose                                              |
| -- | ------------------------------------- | ---------------------------------------------------- |
| 1  | `okx-affiliate-performance-summary`   | Aggregate metrics — invitees, deposits, volume, commission, broken down by Spot / Derivatives / BSC |
| 2  | `okx-affiliate-invitee-list`          | Paginated invitee list with deposits, trading, KYC   |
| 3  | `okx-affiliate-invitee-detail`        | Deep dive on a single invitee by external UID         |
| 4  | `okx-affiliate-link-list`             | Your invite links + commission rates + cumulative stats (incl. 24h commission) |
| 5  | `okx-affiliate-sub-affiliate-list`    | Sub-affiliates in your MLRS network (lifetime data)  |
| 6  | `okx-affiliate-co-inviter-list`       | Channels where you are listed as a co-inviter        |

Full parameters and return fields → [`docs/tools-reference.md`](docs/tools-reference.md).

## Documentation

| Document                                                       | Description                                                  |
| -------------------------------------------------------------- | ------------------------------------------------------------ |
| [Tools reference](docs/tools-reference.md)                     | Every tool, every parameter, every return field              |
| [Usage examples](docs/usage-examples.md)                       | Natural-language prompts that route to each tool             |
| [`periodType` quick reference](docs/period-type.md)            | The eight time-window codes used across most tools           |
| [FAQ](docs/faq.md)                                             | Token expiry, 400 errors, scope mismatches, common pitfalls  |
| [Agent install bootstrap](INSTALL.md)                          | Decision tree an AI agent can read end-to-end                |
| [Skills index](skills/README.md)                               | Skills for runtimes that need custom OAuth handling          |
| [**Usage scenarios**](examples/README.md)                      | Drop-in skill packs for common analysis tasks (daily briefing, churn rescue, etc.) |

## Prerequisites

- An **OKX account** that is enrolled as an Affiliate
- A **client** in the table above (or any other MCP-compliant agent)
- A browser to complete the one-time OAuth handshake

That is it — there is no SDK to install and no local server to run. The MCP is hosted by OKX.

## License

[MIT](LICENSE).

## Contact

Issues and feature requests: please open a GitHub issue on this repo.
