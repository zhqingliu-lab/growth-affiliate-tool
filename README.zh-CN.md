# OKX Growth Affiliate Tool

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

[English](README.md) | [中文](README.zh-CN.md)

一個遠端 MCP（Model Context Protocol）端點，讓 AI agent 能用自然語言查詢 OKX Affiliate 後台
的業績、邀請人、邀請鏈接、子聯盟等數據，無需手動拼 API。

> **端點：** `https://www.okx.com/api/v1/mcp/growth-affiliate-mcp`
> **傳輸：** Streamable HTTP
> **認證：** OAuth 2.0（每個用戶獨立授權，由 OKX 託管）

## 接入後可以做什麼

直接問你的 agent：
- *「看下我近 30 天的 affiliate 業績」*
- *「本季度佣金前 10 的邀請人是誰？」*
- *「列出我所有邀請鏈接，按交易者數排序」*
- *「子聯盟 `XYZ` 本月表現如何？」*

Agent 自動選工具、執行、用人話回答。

## 快速開始

對應你的客戶端，按下面表格找到安裝指南。每個指南都是一條命令加 4 步 OAuth 流程。

| 客戶端                          | 原生 MCP OAuth | 安裝指南                                                     |
| ------------------------------- | :------------: | ------------------------------------------------------------ |
| **Claude Code**（CLI）          | ✅             | [`docs/install/claude-code.md`](docs/install/claude-code.md) |
| **Codex CLI**                   | ✅             | [`docs/install/codex.md`](docs/install/codex.md)             |
| **Hermes**                      | ✅             | [`docs/install/hermes.md`](docs/install/hermes.md)           |
| **Cursor**                      | ✅             | [`docs/install/cursor.md`](docs/install/cursor.md)           |
| **通用 MCP 客戶端**             | ✅             | [`docs/install/generic.md`](docs/install/generic.md)         |
| **OpenClaw**                    | ❌（用 skill） | [`docs/install/openclaw.md`](docs/install/openclaw.md)       |

> **OpenClaw 用戶請注意：** OpenClaw 內建的 MCP runtime 還不支援 OKX 的非標準 OAuth（必填
> `resource` 參數 + 非 discovery 的 DCR 端點）。在上游支援之前，OpenClaw 用戶通過
> [`openclaw-affiliate-skill`](https://github.com/zhqingliu-lab/openclaw-affiliate-skill) 安裝
> ——這是一個專門引導 agent 完成 OAuth 流程的 skill pack。

## 授權範圍

連接時 OKX 會問你授予哪些權限。**這個 MCP 只需要讀權限**，所以推薦默認只勾選
**Live Trading → Read-only**：

| Scope                 | 推薦 | 用途                                                  |
| --------------------- | :--: | ----------------------------------------------------- |
| `live:read`           | ✅   | 下面所有讀取工具（業績/邀請人/鏈接/子聯盟）           |
| `live:trade`          | ❌   | 下單/改單/撤單 —— 本 MCP 不用                         |
| `live:earn`           | ❌   | Earn 申購 —— 本 MCP 不用                              |
| `live:asset_transfer` | ❌   | 資金劃轉 —— 本 MCP 不用                               |
| `demo:*`              | ❌   | 模擬盤 —— 本 MCP 不用                                 |

之後想加 scope 重新跑 `/mcp`（或你 agent 的等效命令）即可。

## 工具一覽

| #  | 工具名                              | 用途                                              |
| -- | ----------------------------------- | ------------------------------------------------- |
| 1  | `affiliate-pro-performance-summary` | 聚合業績指標——邀請人/入金/交易量/佣金，按現貨/合約/BSC 拆分 |
| 2  | `affiliate-pro-invitee-list`        | 邀請人分頁列表，含入金、交易、KYC                  |
| 3  | `affiliate-pro-invitee-detail`      | 按 UID 查單個邀請人詳情                            |
| 4  | `affiliate-pro-link-list`           | 邀請鏈接 + 佣金比例 + 累計數據                     |
| 5  | `affiliate-pro-sub-affiliate-list`  | MLRS 網絡中的子聯盟                                |
| 6  | `affiliate-pro-co-inviter-list`     | 你被列為共同邀請人的渠道                           |

完整參數和返回字段 → [`docs/tools-reference.md`](docs/tools-reference.md)。

## 文檔

| 文檔                                                           | 內容                                                |
| -------------------------------------------------------------- | --------------------------------------------------- |
| [工具參考](docs/tools-reference.md)                            | 每個工具的所有參數和返回字段                        |
| [使用示例](docs/usage-examples.md)                             | 自然語言提問範例                                    |
| [`periodType` 速查表](docs/period-type.md)                     | 8 種時間窗口代碼                                    |
| [FAQ](docs/faq.md)                                             | Token 過期、400 錯誤、scope 不匹配、常見坑          |
| [Agent 安裝引導](INSTALL.md)                                   | 給 AI agent 端到端讀的決策樹                        |

## 前置條件

- 一個已開通 **Affiliate** 的 OKX 帳號
- 上面表格中的任一**客戶端**（或其他 MCP 兼容 agent）
- 瀏覽器，用於完成一次性 OAuth 授權

僅此而已 —— 不需要安裝 SDK，也不需要本地起 server。MCP 由 OKX 託管。

## 授權

[MIT](LICENSE)

## 聯絡

問題或需求：請在本 repo 提 issue。
