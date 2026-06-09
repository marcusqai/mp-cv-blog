---
title: "OpenCode - The Open-Source AI Coding Agent That Beats Vendor Lock-In"
date: 2026-06-09T10:30:00+08:00
draft: false
tags:
  - tech
  - ai-coding
  - open-source
  - developer-tools
summary: "OpenCode is a free, open-source AI coding agent supporting 75+ model providers with zero vendor lock-in."
description: "A complete guide to OpenCode - the GitHub 170k+ stars project that lets you use any AI model for coding. Learn installation, configuration, and advanced features like custom agents and MCP integration."
---

## English Version

### What Is OpenCode?

**OpenCode is an open-source AI coding agent that competes with Claude Code and Codex - but with one crucial difference: zero vendor lock-in.**

| Feature | Details |
|:---|:---|
| **GitHub** | https://github.com/anomalyco/opencode |
| **Website** | https://opencode.ai |
| **Stars** | 170,000+ |
| **License** | MIT (fully open-source) |
| **Built With** | Go (TUI) + JavaScript/Bun (HTTP server) |
| **Core Advantage** | Model-agnostic: 75+ model providers supported |

### Why OpenCode Matters

Unlike Claude Code (locked to Anthropic models) or Codex (locked to OpenAI), OpenCode lets you switch between any AI provider instantly. Use cheap models for simple tasks, expensive models for complex ones. Run local models via Ollama for complete privacy.

**Key advantages:**

- **Free** - MIT license, bring your own API key
- **75+ Model Providers** - Anthropic, OpenAI, Google, DeepSeek, Groq, Ollama (local)
- **Zero Lock-In** - Switch models anytime, immune to price hikes
- **LSP Integration** - Only AI coding agent with Language Server Protocol support
- **Multi-Agent System** - Build, Plan, General, Explore, Scout agents built-in
- **Fully Customizable** - Agents, Commands, Skills, Tools, MCP all configurable
- **Local Privacy** - Support Ollama for code that never leaves your machine

### Installation

**One-liner (recommended):**
```bash
curl -fsSL https://opencode.ai/install | bash
```

**Other methods:**

| Platform | Command |
|:---|:---|
| npm | `npm install -g opencode-ai` |
| Homebrew | `brew install anomalyco/tap/opencode` |
| Windows (Scoop) | `scoop install opencode` |
| Docker | `docker run -it --rm ghcr.io/anomalyco/opencode` |

### Getting Started

```bash
# Enter your project directory
cd /path/to/project

# Launch OpenCode TUI
opencode

# First-time setup - analyze project structure
/init
```

### The Two Main Modes

| Mode | Shortcut | Purpose |
|:---|:---|:---|
| **Build** | Tab to switch | Default agent, full tool access, can edit and execute |
| **Plan** | Tab to switch | Read-only, no edits or commands, perfect for analysis |

**Recommended workflow:** Use Plan mode to analyze and design, then switch to Build mode to execute.

### Essential Commands

| Command | Shortcut | Function |
|:---|:---|:---|
| `/connect` | - | Add model provider and API key |
| `/init` | - | Initialize project, create AGENTS.md |
| `/new` | Ctrl+X N | Start new session |
| `/compact` | Ctrl+X C | Compress/summarize current session |
| `/undo` | Ctrl+X U | Undo last message (requires Git) |
| `/redo` | Ctrl+X R | Redo undone message |
| `/models` | Ctrl+X M | List available models |

### Model Configuration

Connect providers with `/connect`, then configure in `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-20250514",
  "small_model": "anthropic/claude-haiku-4-20250514"
}
```

**Pro tip:** Use `small_model` for lightweight tasks (title generation, etc.) to save costs.

### Custom Agents

Create `.opencode/agents/reviewer.md`:

```markdown
---
description: Reviews code for quality and best practices
mode: subagent
model: anthropic/claude-sonnet-4-20250514
permission:
  edit: deny
  bash: deny
---

You are in code review mode. Focus on:
- Code quality and best practices
- Potential bugs and edge cases
- Performance implications
- Security considerations
```

Use it: `@reviewer check this function`

### Custom Commands

Create `.opencode/commands/test.md`:

```markdown
---
description: Run tests with coverage
agent: build
---

Run the full test suite with coverage report and show any failures.
Focus on the failing tests and suggest fixes.
```

Use it: `/test`

### Built-in Tools

| Tool | Purpose |
|:---|:---|
| `bash` | Execute shell commands |
| `edit` | Modify existing files |
| `write` | Create or overwrite files |
| `read` | Read file contents |
| `grep` | Regex search in files |
| `lsp` | Language Server Protocol (go-to-definition, find references) |
| `webfetch` | Fetch web content |
| `websearch` | Search the web (via Exa AI) |

### Permission Control

Fine-grained control over what the AI can do:

```json
{
  "permission": {
    "edit": "ask",
    "bash": {
      "*": "ask",
      "git diff": "allow",
      "git log*": "allow",
      "rm -rf *": "deny"
    }
  }
}
```

Permission levels: `allow` (no approval needed), `ask` (requires approval), `deny` (disabled).

### MCP Integration

Connect to external services via MCP (Model Context Protocol):

```json
{
  "mcp": {
    "jira": {
      "type": "remote",
      "url": "https://jira.example.com/mcp",
      "enabled": true
    }
  }
}
```

Use cases: database queries, Jira/GitHub integration, third-party APIs.

### OpenCode vs Claude Code vs Codex

| Feature | OpenCode | Claude Code | Codex |
|:---|:---|:---|:---|
| **Model Support** | 75+ providers | Claude only | OpenAI only |
| **License** | MIT (open-source) | Proprietary | Proprietary |
| **Local Models** | Yes (Ollama) | No | No |
| **Cost** | Free (BYO API key) | Subscription | Subscription |
| **Vendor Lock-In** | None | Full | Full |
| **LSP Support** | Yes | No | No |

### Who Should Use OpenCode?

- **Developers who hate lock-in** - Switch models freely
- **Cost-conscious teams** - Use cheap models for simple tasks
- **Privacy-focused** - Run local models, code never leaves your machine
- **Enterprise users** - Fine-grained permissions + MCP integration
- **Multi-language projects** - Different models for different languages

### Quick Start Checklist

1. Install: `curl -fsSL https://opencode.ai/install | bash`
2. Navigate to project: `cd /path/to/project`
3. Launch: `opencode`
4. Connect provider: `/connect`
5. Initialize: `/init`
6. Start coding!

---

*Have you tried OpenCode yet? What model provider do you prefer for AI coding?*

---

## 中文版

### OpenCode 是什麼？

**OpenCode 是一個開源的 AI 編程代理工具，可以與 Claude Code 和 Codex 競爭 - 但有一個關鍵區別：零供應商鎖定。**

| 特性 | 詳情 |
|:---|:---|
| **GitHub** | https://github.com/anomalyco/opencode |
| **官網** | https://opencode.ai |
| **Stars** | 17 萬+ |
| **授權** | MIT（完全開源） |
| **技術棧** | Go（TUI）+ JavaScript/Bun（HTTP 服務器） |
| **核心優勢** | 模型無關：支持 75+ 模型提供商 |

### 為什麼 OpenCode 重要

不像 Claude Code（鎖定 Anthropic 模型）或 Codex（鎖定 OpenAI），OpenCode 讓你可以即時切換任何 AI 提供商。簡單任務用便宜模型，複雜任務用貴模型。通過 Ollama 運行本地模型實現完全隱私。

**核心優勢：**

- **免費** - MIT 授權，自帶 API Key
- **75+ 模型提供商** - Anthropic、OpenAI、Google、DeepSeek、Groq、Ollama（本地）
- **零鎖定** - 隨時切換模型，不怕漲價
- **LSP 集成** - 唯一支持語言服務器協議的 AI 編程代理
- **多代理系統** - 內建 Build、Plan、General、Explore、Scout 代理
- **完全可定制** - Agents、Commands、Skills、Tools、MCP 全部可配置
- **本地隱私** - 支持 Ollama，代碼不上傳

### 安裝方法

**一鍵安裝（推薦）：**
```bash
curl -fsSL https://opencode.ai/install | bash
```

**其他方法：**

| 平台 | 命令 |
|:---|:---|
| npm | `npm install -g opencode-ai` |
| Homebrew | `brew install anomalyco/tap/opencode` |
| Windows (Scoop) | `scoop install opencode` |
| Docker | `docker run -it --rm ghcr.io/anomalyco/opencode` |

### 快速開始

```bash
# 進入項目目錄
cd /path/to/project

# 啟動 OpenCode TUI
opencode

# 首次設置 - 分析項目結構
/init
```

### 兩種主要模式

| 模式 | 快捷鍵 | 用途 |
|:---|:---|:---|
| **Build** | Tab 切換 | 默認代理，完整工具訪問，可編輯和執行 |
| **Plan** | Tab 切換 | 只讀，禁止編輯和命令，適合分析 |

**推薦工作流：** 用 Plan 模式分析和設計，然後切換到 Build 模式執行。

### 常用命令

| 命令 | 快捷鍵 | 功能 |
|:---|:---|:---|
| `/connect` | - | 添加模型提供商和 API Key |
| `/init` | - | 初始化項目，創建 AGENTS.md |
| `/new` | Ctrl+X N | 開始新會話 |
| `/compact` | Ctrl+X C | 壓縮/總結當前會話 |
| `/undo` | Ctrl+X U | 撤銷上次消息（需要 Git） |
| `/redo` | Ctrl+X R | 重做已撤銷的消息 |
| `/models` | Ctrl+X M | 列出可用模型 |

### 模型配置

用 `/connect` 連接提供商，然後在 `opencode.json` 配置：

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-20250514",
  "small_model": "anthropic/claude-haiku-4-20250514"
}
```

**小技巧：** 用 `small_model` 處理輕量任務（標題生成等）以節省成本。

### 自定義代理

創建 `.opencode/agents/reviewer.md`：

```markdown
---
description: 審查代碼質量和最佳實踐
mode: subagent
model: anthropic/claude-sonnet-4-20250514
permission:
  edit: deny
  bash: deny
---

你正在代碼審查模式。專注於：
- 代碼質量和最佳實踐
- 潛在的 bug 和邊界情況
- 性能影響
- 安全考慮
```

使用：`@reviewer 檢查這個函數`

### 自定義命令

創建 `.opencode/commands/test.md`：

```markdown
---
description: 運行測試並生成覆蓋率報告
agent: build
---

運行完整測試套件並生成覆蓋率報告，顯示所有失敗。
專注於失敗的測試並建議修復方案。
```

使用：`/test`

### 內建工具

| 工具 | 用途 |
|:---|:---|
| `bash` | 執行 Shell 命令 |
| `edit` | 修改現有文件 |
| `write` | 創建新文件或覆蓋 |
| `read` | 讀取文件內容 |
| `grep` | 正則表達式搜索 |
| `lsp` | 語言服務器協議（跳轉定義、查找引用） |
| `webfetch` | 獲取網頁內容 |
| `websearch` | 搜索網絡（通過 Exa AI） |

### 權限控制

精細控制 AI 可以做什麼：

```json
{
  "permission": {
    "edit": "ask",
    "bash": {
      "*": "ask",
      "git diff": "allow",
      "git log*": "allow",
      "rm -rf *": "deny"
    }
  }
}
```

權限級別：`allow`（無需批准）、`ask`（需要批准）、`deny`（禁用）。

### MCP 集成

通過 MCP（模型上下文協議）連接外部服務：

```json
{
  "mcp": {
    "jira": {
      "type": "remote",
      "url": "https://jira.example.com/mcp",
      "enabled": true
    }
  }
}
```

使用場景：數據庫查詢、Jira/GitHub 集成、第三方 API。

### OpenCode vs Claude Code vs Codex

| 特性 | OpenCode | Claude Code | Codex |
|:---|:---|:---|:---|
| **模型支持** | 75+ 提供商 | 僅 Claude | 僅 OpenAI |
| **授權** | MIT（開源） | 專有 | 專有 |
| **本地模型** | 支持（Ollama） | 不支持 | 不支持 |
| **成本** | 免費（自帶 API Key） | 訂閱制 | 訂閱制 |
| **供應商鎖定** | 無 | 完全 | 完全 |
| **LSP 支持** | 支持 | 不支持 | 不支持 |

### 誰應該使用 OpenCode？

- **討厭鎖定的開發者** - 自由切換模型
- **成本敏感的團隊** - 簡單任務用便宜模型
- **注重隱私** - 運行本地模型，代碼不上傳
- **企業用戶** - 精細權限 + MCP 集成
- **多語言項目** - 不同語言用不同模型

### 快速開始清單

1. 安裝：`curl -fsSL https://opencode.ai/install | bash`
2. 進入項目：`cd /path/to/project`
3. 啟動：`opencode`
4. 連接提供商：`/connect`
5. 初始化：`/init`
6. 開始編程！

---

*你用過 OpenCode 嗎？你最喜歡哪個模型提供商做 AI 編程？*
