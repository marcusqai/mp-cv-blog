---
title: "OpenCode - The Open-Source AI Coding Agent"
date: 2026-06-09T10:00:00+08:00
draft: false
tags:
  - tech
  - ai-coding
  - open-source
summary: "OpenCode is a free, open-source AI coding agent with 75+ model providers."
description: "A complete guide to OpenCode - the GitHub 170k+ stars project for AI coding with any model."
---

## English Version

### What Is OpenCode?

**OpenCode is an open-source AI coding agent that competes with Claude Code and Codex - but with zero vendor lock-in.**

| Feature | Details |
|:---|:---|
| **GitHub** | https://github.com/anomalyco/opencode |
| **Website** | https://opencode.ai |
| **Stars** | 170,000+ |
| **License** | MIT (fully open-source) |
| **Core Advantage** | Model-agnostic: 75+ model providers supported |

### Why OpenCode Matters

Unlike Claude Code (locked to Anthropic) or Codex (locked to OpenAI), OpenCode lets you switch between any AI provider instantly.

**Key advantages:**

- **Free** - MIT license, bring your own API key
- **75+ Model Providers** - Anthropic, OpenAI, Google, DeepSeek, Ollama (local)
- **Zero Lock-In** - Switch models anytime
- **LSP Integration** - Only AI agent with Language Server Protocol support
- **Multi-Agent System** - Build, Plan, General, Explore, Scout agents
- **Local Privacy** - Support Ollama for code that never leaves your machine

### Installation

```bash
curl -fsSL https://opencode.ai/install | bash
```

### Getting Started

```bash
cd /path/to/project
opencode
/init
```

### The Two Main Modes

| Mode | Purpose |
|:---|:---|
| **Build** | Default agent, full tool access, can edit and execute |
| **Plan** | Read-only, no edits, perfect for analysis |

### Essential Commands

| Command | Function |
|:---|:---|
| `/connect` | Add model provider and API key |
| `/init` | Initialize project, create AGENTS.md |
| `/new` | Start new session |
| `/compact` | Compress current session |
| `/undo` | Undo last message (requires Git) |

### Model Configuration

```json
{
  "model": "anthropic/claude-sonnet-4-20250514",
  "small_model": "anthropic/claude-haiku-4-20250514"
}
```

### Custom Agents

Create `.opencode/agents/reviewer.md`:

```markdown
---
description: Reviews code for quality
mode: subagent
permission:
  edit: deny
---

You are in code review mode.
```

Use: `@reviewer check this function`

### OpenCode vs Claude Code vs Codex

| Feature | OpenCode | Claude Code | Codex |
|:---|:---|:---|:---|
| **Models** | 75+ providers | Claude only | OpenAI only |
| **License** | MIT | Proprietary | Proprietary |
| **Local Models** | Yes (Ollama) | No | No |
| **Cost** | Free (BYO key) | Subscription | Subscription |
| **Lock-In** | None | Full | Full |

### Who Should Use OpenCode?

- **Developers who hate lock-in**
- **Cost-conscious teams** - Use cheap models for simple tasks
- **Privacy-focused** - Run local models
- **Enterprise users** - Fine-grained permissions + MCP

---

*Have you tried OpenCode yet?*

---

## 中文版

### OpenCode 是什麼？

**OpenCode 是一個開源的 AI 編程代理工具，可以與 Claude Code 和 Codex 競爭 - 關鍵區別是：零供應商鎖定。**

| 特性 | 詳情 |
|:---|:---|
| **GitHub** | https://github.com/anomalyco/opencode |
| **官網** | https://opencode.ai |
| **Stars** | 17 萬+ |
| **授權** | MIT（完全開源） |
| **核心優勢** | 模型無關：支持 75+ 模型提供商 |

### 為什麼 OpenCode 重要

不像 Claude Code（鎖定 Anthropic）或 Codex（鎖定 OpenAI），OpenCode 讓你可以即時切換任何 AI 提供商。

**核心優勢：**

- **免費** - MIT 授權，自帶 API Key
- **75+ 模型提供商** - Anthropic、OpenAI、Google、DeepSeek、Ollama（本地）
- **零鎖定** - 隨時切換模型
- **LSP 集成** - 唯一支持語言服務器協議的 AI 編程代理
- **多代理系統** - 內建 Build、Plan、General、Explore、Scout 代理
- **本地隱私** - 支持 Ollama，代碼不上傳

### 安裝方法

```bash
curl -fsSL https://opencode.ai/install | bash
```

### 快速開始

```bash
cd /path/to/project
opencode
/init
```

### 兩種主要模式

| 模式 | 用途 |
|:---|:---|
| **Build** | 默認代理，完整工具訪問，可編輯和執行 |
| **Plan** | 只讀，禁止編輯，適合分析 |

### 常用命令

| 命令 | 功能 |
|:---|:---|
| `/connect` | 添加模型提供商和 API Key |
| `/init` | 初始化項目，創建 AGENTS.md |
| `/new` | 開始新會話 |
| `/compact` | 壓縮當前會話 |
| `/undo` | 撤銷上次消息（需要 Git） |

### 模型配置

```json
{
  "model": "anthropic/claude-sonnet-4-20250514",
  "small_model": "anthropic/claude-haiku-4-20250514"
}
```

### 自定義代理

創建 `.opencode/agents/reviewer.md`：

```markdown
---
description: 審查代碼質量
mode: subagent
permission:
  edit: deny
---

你正在代碼審查模式。
```

使用：`@reviewer 檢查這個函數`

### OpenCode vs Claude Code vs Codex

| 特性 | OpenCode | Claude Code | Codex |
|:---|:---|:---|:---|
| **模型支持** | 75+ 提供商 | 僅 Claude | 僅 OpenAI |
| **授權** | MIT（開源） | 專有 | 專有 |
| **本地模型** | 支持（Ollama） | 不支持 | 不支持 |
| **成本** | 免費（自帶 API Key） | 訂閱制 | 訂閱制 |
| **鎖定** | 無 | 完全 | 完全 |

### 誰應該使用 OpenCode？

- **討厭鎖定的開發者**
- **成本敏感的團隊** - 簡單任務用便宜模型
- **注重隱私** - 運行本地模型
- **企業用戶** - 精細權限 + MCP 集成

---

*你用過 OpenCode 嗎？*
