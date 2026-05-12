# OKX Growth Affiliate Tool

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

[English](README.md) | [中文](README.zh-CN.md)

一个远端 MCP（Model Context Protocol）端点，让 AI agent 能用自然语言查询 OKX Affiliate 后台
的业绩、邀请人、邀请链接、子联盟等数据，无需手动拼 API。

> **端点：** `https://www.okx.com/api/v1/mcp/growth-affiliate-mcp`
> **传输：** Streamable HTTP
> **认证：** OAuth 2.0（每个用户独立授权，由 OKX 托管）

## 长这样

完成一次性 OAuth 握手后，用自然语言跟 agent 对话即可。agent 自动选工具、调 MCP、用人话
答你：

```
你           ❯ 看下昨日节点数据

Agent        ❯ 📊 昨日（2026-05-10）
                 • 佣金：$350.59（合约 98.5% / 现货 1.5%）
                 • 成交量：$3.50M | 入金：$8,022
                 • 活跃交易者：77 | 新邀请：6

                 vs 7 天日均 $503 → 昨日 -30%，在正常波动区间内。
                 今日 1 小时已 $530，节奏比昨日快。

                 Top 3 返佣贡献：
                  • UID …910040（VIP 2）   $90.94 / $964K 成交
                  • UID …301554（VIP 1）   $50.31 / $526K
                  • UID …988366（Regular）$27.80 / $297K  ← 该升 VIP1

                 ⚠️ 24 小时内要救的入金未交易用户：
                  • …033472 入金 $872，0 交易
                  • …446587 入金 $464，0 交易（重复模式，5/6 也是这样）
                  • …298402 入金 $420，0 交易
```

```
你           ❯ 这周该联系谁？

Agent        ❯ 🎯 4 个优先级，共 33 个候选：

                 🔥 P0 — 新邀明星（加入 ≤ 7 天，已经在产出）
                   …586237（4/29 加入）：返佣 $268 / 成交 $2.0M / 入金 $5K
                   …084083（4/29 加入）：返佣 $228 / 成交 $1.7M / 入金 $4.7K
                   → 今天就升 VIP1，给专属交易顾问联系方式

                 🐋 P1 — 做着鲸鱼量的 Regular 用户 → 这周升 VIP1
                   …118301  $81 / $653K — 加入 1/23
                   …575159  $69 / $506K — 加入 4/26
                   …274299  $69 / $850K — 加入 8/3
                   …370516  $87 / $743K — 加入 12/12（最老）

                 ⚠️ P2 — 大额入金未激活（近 7 天）
                   …181120 入金 $10,000，只成交 $500（已闲 8 天）
                   …108384 入金 $7,293，成交 $3K
                   合计 $36K 新进资金闲置，窗口期快关。

                 🆕 P3 — KYC 完成但还没入金（本周新增 13 个）
```

```
你           ❯ 哪些鲸鱼在流失？

Agent        ❯ ⚠️ 生涯前 50 用户里，只有 23 个还在近 30 天前 50。

                 🔴 Type A — 已套现走人（基本回不来）
                  • UID …893796 — 生涯返佣 $104K / 提走了 $10.3M 入金的 95%
                  • UID …580269 — 生涯返佣 $37K / 提走了 76%

                 🟡 Type B — 资金还在但停止交易（最易挽回）
                  • UID …699614 — 生涯返佣 $26K / 只提走 6%（$1.47M 还在）
                    → 今天就 DM。让他了解闪赚，资金别白闲着。

                 🟠 Type C — 渐进衰退（现在干预）
                  • UID …616246 — 生涯返佣 $18K / 近 30 天 0 成交
                  • UID …690681 — 生涯返佣 $11.6K / 近 30 天 0 成交

                 风险 LTV 合计：约 $208K（≈ 节点 12 个月佣金的 46%）。
```

更多场景见下方 [使用场景](#使用场景)。

## 快速开始

对应你的客户端，按下面表格找到安装指南。每个指南都是一条命令加 4 步 OAuth 流程。

| 客户端                          | 原生 MCP OAuth | 安装指南                                                     |
| ------------------------------- | :------------: | ------------------------------------------------------------ |
| **Claude Code**（CLI）          | ✅             | [`docs/install/claude-code.md`](docs/install/claude-code.md) |
| **Codex CLI**                   | ✅             | [`docs/install/codex.md`](docs/install/codex.md)             |
| **Hermes**                      | ✅             | [`docs/install/hermes.md`](docs/install/hermes.md)           |
| **Cursor**                      | ✅             | [`docs/install/cursor.md`](docs/install/cursor.md)           |
| **通用 MCP 客户端**             | ✅             | [`docs/install/generic.md`](docs/install/generic.md)         |
| **OpenClaw**                    | ❌（用 skill） | [`docs/install/openclaw.md`](docs/install/openclaw.md)       |

> **OpenClaw 用户请注意：** OpenClaw 内建的 MCP runtime 还不支持 OKX 的非标准 OAuth（必填
> `resource` 参数 + 非 discovery 的 DCR 端点）。在上游支持之前，OpenClaw 用户通过本仓库自带
> 的 skill 安装：[`skills/openclaw-affiliate-mcp/`](skills/openclaw-affiliate-mcp/) —— 一个
> 专门引导 agent 完成 OAuth 流程的 skill pack。

## 使用场景

预置的 skill 包，教 AI agent 用本 MCP 做常见分析任务。每个 skill 是一份带触发词的
`SKILL.md`，包含 MCP 调用顺序、样例输出、推荐后续动作 —— 放到你 agent 的 skill 目录里，
用户说出触发词时会自动激活。

| 场景                                                                          | 用户怎么说                                                                  | 你拿到什么                                          |
| ----------------------------------------------------------------------------- | --------------------------------------------------------------------------- | --------------------------------------------------- |
| [`daily-briefing`](examples/daily-briefing/SKILL.md)                          | *"看下昨日数据"* / *"morning report"* / *"节点日报"*                          | EOD 汇总 + 当日入金者 + 新邀请 + 3 条行动项          |
| [`high-potential-invitees`](examples/high-potential-invitees/SKILL.md)        | *"潜力用户"* / *"who should I reach out to"*                                  | P0/P1/P2/P3 多层外联名单 + 具体 UID                   |
| [`churn-rescue`](examples/churn-rescue/SKILL.md)                              | *"流失预警"* / *"鲸鱼跑了"*                                                   | 风险用户分 Type A（已跑路）/ B（资金还在）/ C（渐衰）|
| [`whale-deep-dive`](examples/whale-deep-dive/SKILL.md)                        | *"拉一下 UID X 的完整资料"* / *"准备私聊话术"*                                 | 生涯 + 近期画像 + 健康信号 + 话术钩子                 |
| [`acquisition-trends`](examples/acquisition-trends/SKILL.md)                  | *"近 6 个月拉新"* / *"DAT 历史"* / *"拉新趋势"*                                | 月度表 + 峰值标识 + 拐点分析                          |
| [`tier-upgrade-candidates`](examples/tier-upgrade-candidates/SKILL.md)        | *"应该升 VIP1 的名单"* / *"升级 VIP 名单"*                                    | 做着鲸鱼量却还是 Regular 的用户，按预期提升排序        |

完整索引和贡献指南见 [`examples/README.md`](examples/README.md)。

## 授权范围

连接时 OKX 会问你授予哪些权限。**这个 MCP 只需要读权限**，所以推荐默认只勾选
**Live Trading → Read-only**：

| Scope                 | 推荐 | 用途                                                  |
| --------------------- | :--: | ----------------------------------------------------- |
| `live:read`           | ✅   | 下面所有读取工具（业绩/邀请人/链接/子联盟）           |
| `live:trade`          | ❌   | 下单/改单/撤单 —— 本 MCP 不用                         |
| `live:earn`           | ❌   | Earn 申购 —— 本 MCP 不用                              |
| `live:asset_transfer` | ❌   | 资金划转 —— 本 MCP 不用                               |
| `demo:*`              | ❌   | 模拟盘 —— 本 MCP 不用                                 |

之后想加 scope 重新跑 `/mcp`（或你 agent 的等效命令）即可。

## 工具一览

| #  | 工具名                                | 用途                                              |
| -- | ------------------------------------- | ------------------------------------------------- |
| 1  | `okx-affiliate-performance-summary`   | 聚合业绩指标——邀请人/入金/交易量/佣金，按现货/合约/BSC 拆分 |
| 2  | `okx-affiliate-invitee-list`          | 邀请人分页列表，含入金、交易、KYC                  |
| 3  | `okx-affiliate-invitee-detail`        | 按 UID 查单个邀请人详情                            |
| 4  | `okx-affiliate-link-list`             | 邀请链接 + 佣金比例 + 累计数据（含 24 小时佣金）   |
| 5  | `okx-affiliate-sub-affiliate-list`    | MLRS 网络中的子联盟（生涯数据）                    |
| 6  | `okx-affiliate-co-inviter-list`       | 你被列为共同邀请人的渠道                           |

完整参数和返回字段 → [`docs/tools-reference.md`](docs/tools-reference.md)。

## 文档

| 文档                                                           | 内容                                                |
| -------------------------------------------------------------- | --------------------------------------------------- |
| [工具参考](docs/tools-reference.md)                            | 每个工具的所有参数和返回字段                        |
| [使用示例](docs/usage-examples.md)                             | 自然语言提问示例                                    |
| [`periodType` 速查表](docs/period-type.md)                     | 8 种时间窗口代码                                    |
| [FAQ](docs/faq.md)                                             | Token 过期、400 错误、scope 不匹配、常见坑          |
| [Agent 安装引导](INSTALL.md)                                   | 给 AI agent 端到端读的决策树                        |
| [Skill 索引](skills/README.md)                                 | 给需要自定义 OAuth 处理的 runtime 用的 skill 列表    |
| [**使用场景**](examples/README.md)                             | 常见分析任务的 skill 包（日报、流失救援、潜力用户等）|

## 前置条件

- 一个已开通 **Affiliate** 的 OKX 账号
- 上面表格中的任一**客户端**（或其他 MCP 兼容 agent）
- 浏览器，用于完成一次性 OAuth 授权

仅此而已 —— 不需要安装 SDK，也不需要本地起 server。MCP 由 OKX 托管。

## 授权

[MIT](LICENSE)

## 联系

问题或需求：请在本 repo 提 issue。
