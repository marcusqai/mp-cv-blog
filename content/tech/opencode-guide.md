---
title: "OpenCode - The Complete Guide to Open-Source AI Coding Agent"
date: 2026-06-09T10:00:00+08:00+08:00
draft: false
tags:
  - tech
  - ai-coding
  - open-source
  - developer-tools
summary: "Complete guide to OpenCode - open-source AI coding agent with 75+ model providers."
description: "Comprehensive guide covering installation, configuration, custom agents, commands, skills, tools, permissions, MCP integration, and comparison with Claude Code and Codex."
---

## English Version

### What Is OpenCode?

**OpenCode is an open-source AI coding agent that competes with Claude Code and Codex - with zero vendor lock-in.**

| Item | Details |
|:---|:---|
| **GitHub** | https://github.com/anomalyco/opencode |
| **Website** | https://opencode.ai |
| **Stars** | 170,000+ |
| **License** | MIT (fully open-source) |
| **Core Advantage** | Model-Agnostic: 75+ model providers, zero vendor lock-in |

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

### Two Main Modes

| Mode | Purpose |
|:---|:---|
| **Build** | Full tool access, can edit and execute |
| **Plan** | Read-only, perfect for analysis |

### Essential Commands

| Command | Function |
|:---|:---|
| `/connect` | Add model provider and API key |
| `/init` | Initialize project, create AGENTS.md |
| `/new` | Start new session |
| `/compact` | Compress current session |
| `/undo` | Undo last message (requires Git) |

### Custom Agents

Create `.opencode/agents/reviewer.md`:

```markdown
---
description: Reviews code for quality
mode: subagent
permission:
  edit: deny
---
```

Use: `@reviewer check this function`

### OpenCode vs Claude Code vs Codex

| Feature | OpenCode | Claude Code | Codex |
|:---|:---|:---|:---|
| **Models** | 75+ providers | Claude only | OpenAI only |
| **License** | MIT | Proprietary | Proprietary |
| **Local Models** | Yes (Ollama) | No | No |
| **Lock-In** | None | Full | Full |

---

*Have you tried OpenCode yet?*

---

## 中文版

# OpenCode 深度分析與完全指南¶

> 基於影片「不得不裝的 AI 代理工具｜GitHub 萬星項目｜OPENCODE」（Rick Hau, 2026-06-05）影片連結：https://www.youtube.com/watch?v=__bcJHoTE08分析時間：2026-06-09版本：OpenCode v0.1.x+（GitHub 17 萬+ Stars）

---

## 📑 目錄¶
- OpenCode 是什麼- 為什麼「不得不裝」- 安裝與啟動- 交互模式（TUI + CLI）- 常用命令- CLI 命令- 模型配置- 配置文件- 自定義智能體（Agents）- 自定義命令（Custom Commands）- Agent Skills- Custom Tools- 權限控制（Permissions）- MCP 服務器- 插件系統- GitHub 與 GitLab 集成- 注意事項- 與 Claude Code / Codex 比較- 推薦工作流程- 快速參考速查表
---

## 1. OpenCode 是什麼¶

OpenCode 是一個開源的 AI 編程代理工具（AI Coding Agent），可以與 Claude Code、Codex 等並駕齊驅。

| 項目 | 詳情 ||---|---|| GitHub | https://github.com/anomalyco/opencode || 官網 | https://opencode.ai || 文檔 | https://opencode.ai/docs || Stars | 17 萬+ || 授權 | MIT（完全開源） || 開發語言 | Go（TUI，基於 Bubble Tea）+ JavaScript/Bun（HTTP 服務器，基於 Hono） || 架構 | Client-Server（支持終端 TUI、桌面應用、IDE 擴展、HTTP 客戶端） || 核心賣點 | 模型無關（Model-Agnostic）：支持 75+ 模型提供商，零供應商鎖定 |

### 與其他工具的本質區別¶

| 工具 | 核心策略 ||---|---|| Claude Code | 單一模型家族（Claude），深度優化，追求速度 || Codex | OpenAI 生態系（ChatGPT 綁定），追求集成深度 || OpenCode | 模型無關 + 開源 + 零鎖定，追求靈活性與隱私 |

---

## 2. 為什麼「不得不裝」¶

### 2.1 核心優勢¶

| 優勢 | 說明 ||---|---|| 🆓完全免費 | MIT 授權，自己帶 API Key 即可，工具本身零費用 || 🌐75+ 模型提供商 | Anthropic、OpenAI、Google、DeepSeek、Groq、Ollama（本地模型） || 🔒零供應商鎖定 | 隨時切換模型，不怕任何一家漲價或降質 || 💻LSP 集成 | 業界唯一支持 Language Server Protocol 的 AI 編程代理 || 🤖多代理系統 | Build、Plan、General、Explore、Scout 五大內建代理 || 🔧高度可定制 | Agents、Commands、Skills、Tools、MCP 全部可自定義 || 📱多表面支持 | TUI（終端）、Desktop App、VS Code 擴展、HTTP API || 🛡️安全控制 | Git 快照 + /undo + /redo + 精細權限控制 || 🏠完全本地隱私 | 支持 Ollama 本地模型，代碼不上傳 || 🌍20+ 語言 | 包含繁體中文（README.zht.md） |

### 2.2 適合誰用¶
- ✅不想被鎖定的開發者：不想只綁定 Anthropic 或 OpenAI- ✅成本敏感者：可以用便宜的模型做簡單任務，貴的模型做複雜任務- ✅隱私要求高：代碼不能上傳到雲端 → 用 Ollama 本地模型- ✅多語言項目：需要不同模型處理不同語言- ✅企業用戶：需要精細權限控制 + MCP 集成
---

## 3. 安裝與啟動¶

### 3.1 一鍵安裝（推薦）¶

```
curl -fsSL https://opencode.ai/install | bash

```

### 3.2 各平台安裝方法¶

| 平台 | 命令 ||---|---|| npm | npm install -g opencode-ai || Bun | bun install -g opencode-ai || pnpm | pnpm install -g opencode-ai || Yarn | yarn global add opencode-ai || Homebrew（macOS/Linux） | brew install anomalyco/tap/opencode || Arch Linux（穩定） | sudo pacman -S opencode || Arch Linux（最新） | paru -S opencode-bin || Windows（Chocolatey） | choco install opencode || Windows（Scoop） | scoop install opencode || Mise（任意 OS） | mise use -g github:anomalyco/opencode || Docker | docker run -it --rm ghcr.io/anomalyco/opencode || Nix | nix run nixpkgs#opencode |

### 3.3 桌面應用¶

| 平台 | 下載 ||---|---|| macOS（Apple Silicon） | opencode-desktop-mac-arm64.dmg || macOS（Intel） | opencode-desktop-mac-x64.dmg || Windows | opencode-desktop-windows-x64.exe || Linux | .deb / .rpm / .AppImage |

macOS 安裝：brew install --cask opencode-desktopWindows 安裝：scoop bucket add extras; scoop install extras/opencode-desktop

### 3.4 前置要求¶
- 現代終端模擬器：WezTerm、Alacritty、Ghostty、Kitty（任一）- LLM API Key：至少一個模型提供商的 API Key- Git：/undo 和 /redo 功能需要 Git 倉庫

### 3.5 啟動¶

```
# 進入項目目錄
cd /path/to/project

# 啟動 TUI
opencode

# 或指定目錄
opencode /path/to/project

```

### 3.6 首次配置¶

啟動後運行/init，OpenCode 會分析項目結構並創建AGENTS.md文件，幫助 AI 理解你的代碼庫。

---

## 4. 交互模式（TUI + CLI）¶

### 4.1 TUI（終端用戶界面）¶

```
┌─────────────────────────────────┐
│  OpenCode TUI                   │
│                                 │
│  💬 消息歷史                     │
│  ─────────────────────          │
│  Q: 幫我解釋這個函數...          │
│  A: 這個函數的作用是...          │
│  ─────────────────────          │
│  > 輸入新消息...                │
│                                 │
│  [Build]  [Tab 切換代理]         │
└─────────────────────────────────┘

```

### 4.2 文件引用¶

使用@符號引用文件：

```
How is authentication handled in @packages/functions/src/api/index.ts?

```
- 支持模糊搜索- 文件內容自動載入對話- 支持別名引用（@docs/README.md）

### 4.3 Bash 命令注入¶

消息以!開頭直接執行 Shell 命令：

```
!ls -la
!git status
!npm test

```

命令輸出自動載入對話。

### 4.4 兩種主要代理模式¶

| 模式 | 快捷鍵 | 說明 ||---|---|---|| Build | Tab 切換 | 默認代理，完整工具訪問權限 || Plan | Tab 切換 | 只讀代理，禁止編輯和執行命令 |

工作流程建議：1. 用Plan模式分析和規劃2. 用Build模式執行和構建

### 4.5 圖像支持¶

直接拖放圖片到終端，OpenCode 會掃描圖片並加入提示詞。

---

## 5. 常用命令¶

### 5.1 內建斜線命令¶

| 命令 | 快捷鍵 | 功能 ||---|---|---|| /connect | — | 添加模型提供商和 API Key || /init | — | 初始化項目，創建 AGENTS.md || /new | Ctrl+X N | 開始新會話 || /compact | Ctrl+X C | 壓縮/總結當前會話 || /undo | Ctrl+X U | 撤銷上次消息（需要 Git） || /redo | Ctrl+X R | 重做已撤銷的消息 || /share | — | 分享當前會話（生成鏈接） || /help | — | 顯示幫助 || /models | Ctrl+X M | 列出可用模型 || /sessions | Ctrl+X L | 列出/切換會話 || /themes | Ctrl+X T | 列出可用主題 || /editor | Ctrl+X E | 打開外部編輯器 || /export | Ctrl+X X | 導出會話為 Markdown || /details | — | 切換工具執行詳情顯示 || /thinking | — | 切換模型推理過程可見性 || /quit//q | Ctrl+X Q | 退出 OpenCode |

### 5.2 對話模式切換¶
- Tab 鍵：在 Build 和 Plan 代理間切換- 右下角指示器：顯示當前代理模式
---

## 6. CLI 命令¶

### 6.1 非交互模式¶

```
# 直接發送消息（不進入 TUI）
opencode run "幫我解釋 src/index.ts 的功能"

# 指定目錄
opencode run "幫我重構這段代碼" /path/to/project

```

### 6.2 服務器模式¶

```
# 啟動 HTTP 服務器
opencode serve

# 啟動 Web 界面
opencode web

```

### 6.3 自定義安裝路徑¶

```
# 指定安裝目錄
OPENCODE_INSTALL_DIR=/usr/local/bin curl -fsSL https://opencode.ai/install | bash

# XDG 標準路徑
XDG_BIN_DIR=$HOME/.local/bin curl -fsSL https://opencode.ai/install | bash

```

---

## 7. 模型配置¶

### 7.1 連接模型提供商¶

```
/connect

```

選擇提供商 → 輸入 API Key → 完成。

### 7.2 支持的主要模型提供商¶

| 提供商 | 模型 | 用途 ||---|---|---|| Anthropic | Claude Sonnet 4.5, Opus 4.6, Haiku 4.5 | 代碼生成、推理 || OpenAI | GPT-5.1 Codex, GPT-5 Codex | 編程專用 || Google | Gemini 3.1 Pro | 多模態 || DeepSeek | DeepSeek-V3 | 性價比高 || Groq | 各種開源模型 | 高速推理 || Ollama | 本地模型 | 隱私優先 || LM Studio | 本地模型 | 隱私優先 |

### 7.3 模型配置格式¶

```
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-20250514",
  "small_model": "anthropic/claude-haiku-4-20250514",
  "provider": {
    "anthropic": {
      "options": {
        "timeout": 600000,
        "chunkTimeout": 30000,
        "setCacheKey": true
      }
    }
  }
}

```

small_model：用於輕量任務（標題生成等），自動使用更便宜的模型。

### 7.4 OpenCode Zen（推薦新手）¶
- 官方測試驗證的模型列表- 按需付費- 運行/connect→ 選擇opencode→ 前往 opencode.ai/auth

### 7.5 不同任務用不同模型¶

```
{
  "agent": {
    "build": {
      "model": "anthropic/claude-sonnet-4-20250514"
    },
    "plan": {
      "model": "anthropic/claude-haiku-4-20250514"
    }
  }
}

```

---

## 8. 配置文件¶

### 8.1 配置優先級（從低到高）¶

| 優先級 | 來源 | 說明 ||---|---|---|| 1 | Remote config | 組織默認值（.well-known/opencode） || 2 | Global config | ~/.config/opencode/opencode.json || 3 | Custom config | OPENCODE_CONFIG環境變數 || 4 | Project config | 項目根目錄opencode.json || 5 | .opencode 目錄 | agents、commands、plugins || 6 | Inline config | OPENCODE_CONFIG_CONTENT環境變數 || 7 | Managed config | 管理員配置（最高優先級） |

重要：配置文件是合併的，不是替換。不同來源的非衝突設置都會保留。

### 8.2 全局配置¶

```
// ~/.config/opencode/opencode.json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-5",
  "autoupdate": true,
  "server": {
    "port": 4096
  }
}

```

### 8.3 項目配置¶

```
// 項目根目錄 opencode.json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-20250514",
  "permission": {
    "edit": "ask",
    "bash": "ask"
  }
}

```

✅可以安全提交到 Git

### 8.4 TUI 配置¶

```
// ~/.config/opencode/tui.json
{
  "$schema": "https://opencode.ai/tui.json",
  "scroll_speed": 3,
  "scroll_acceleration": {
    "enabled": true
  },
  "diff_style": "auto",
  "mouse": true,
  "attention": {
    "enabled": true,
    "notifications": true,
    "sound": true,
    "volume": 0.4
  }
}

```

### 8.5 自定義配置路徑¶

```
export OPENCODE_CONFIG=/path/to/my/custom-config.json
opencode run "Hello world"

```

---

## 9. 自定義智能體（Agents）¶

### 9.1 內建代理¶

| 代理 | 類型 | 說明 ||---|---|---|| Build | 主代理 | 默認，所有工具可用，完整開發權限 || Plan | 主代理 | 只讀，禁止編輯和執行命令，適合分析和規劃 || General | 子代理 | 通用研究，多步任務，可並行執行 || Explore | 子代理 | 快速只讀代碼庫探索，不可修改文件 || Scout | 子代理 | 外部文檔和依賴研究，克隆依賴倉庫到緩存 |

### 9.2 隱藏系統代理¶

| 代理 | 功能 ||---|---|| Compaction | 自動壓縮長上下文 || Title | 自動生成會話標題 || Summary | 自動創建會話摘要 |

### 9.3 使用子代理¶

```
# 在消息中 @ 提及
@general help me search for this function
@explore 幫我找出所有與認證相關的文件
@scout 幫我查看這個庫的源代碼實現

```

### 9.4 子代理會話導航¶

| 操作 | 快捷鍵 ||---|---|| 進入子會話 | Ctrl+Down（或+Down） || 切換到下一個子會話 | Right || 切換到上一個子會話 | Left || 返回父會話 | Up |

### 9.5 自定義代理（JSON 配置）¶

```
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "code-reviewer": {
      "description": "Review code for best practices and potential issues",
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4-20250514",
      "prompt": "You are a code reviewer. Focus on security, performance, and maintainability.",
      "permission": {
        "edit": "deny",
        "bash": "deny"
      },
      "temperature": 0.1
    }
  }
}

```

### 9.6 自定義代理（Markdown 配置）¶

創建文件~/.config/opencode/agents/review.md：

```
---
description: Reviews code for quality and best practices
mode: subagent
model: anthropic/claude-sonnet-4-20250514
temperature: 0.1
permission:
  edit: deny
  bash: deny
---

You are in code review mode. Focus on:
- Code quality and best practices
- Potential bugs and edge cases
- Performance implications
- Security considerations

Provide constructive feedback without making direct changes.

```

文件名即代理名稱（review.md →@review）

### 9.7 代理配置選項¶

| 選項 | 說明 ||---|---|| description | 代理描述（必填） || mode | primary或subagent || model | 覆蓋默認模型 || temperature | 0.0-1.0（0=確定性，1=創造性） || prompt | 自定義系統提示詞文件路徑 || permission | 權限控制 || steps | 最大代理步驟數（控制成本） || disable | true禁用代理 |

---

## 10. 自定義命令（Custom Commands）¶

### 10.1 創建命令（Markdown）¶

創建.opencode/commands/test.md：

```
---
description: Run tests with coverage
agent: build
model: anthropic/claude-3-5-sonnet-20241022
---

Run the full test suite with coverage report and show any failures.
Focus on the failing tests and suggest fixes.

```

使用：/test

### 10.2 創建命令（JSON）¶

```
{
  "$schema": "https://opencode.ai/config.json",
  "command": {
    "test": {
      "template": "Run the full test suite with coverage report and show any failures.\nFocus on the failing tests and suggest fixes.",
      "description": "Run tests with coverage",
      "agent": "build",
      "model": "anthropic/claude-3-5-sonnet-20241022"
    }
  }
}

```

### 10.3 命令參數¶

使用$ARGUMENTS或$1,$2,$3：

```
---
description: Create a new component
---

Create a new React component named $ARGUMENTS with TypeScript support.
Include proper typing and basic structure.

```

使用：/component Button→$ARGUMENTS替換為Button

```
---
description: Create a new file with content
---

Create a file named $1 in the directory $2
with the following content: $3

```

使用：/create-file config.json src '{ "key": "value" }'

### 10.4 命令嵌入 Shell 輸出¶

使用!command注入 Shell 命令輸出：

```
---
description: Review recent changes
---

Recent git commits:

!`git log --oneline -10`

Review these changes and suggest any improvements.

```

### 10.5 命令嵌入文件¶

使用@filename引用文件：

```
---
description: Review component
---

Review the component in @src/components/Button.tsx.
Check for performance issues and suggest improvements.

```

### 10.6 命令選項¶

| 選項 | 說明 ||---|---|| template | 提示詞模板（必填） || description | TUI 中顯示的描述 || agent | 指定執行代理 || subtask | true強制作為子代理執行 || model | 覆蓋默認模型 |

---

## 11. Agent Skills¶

### 11.1 什麼是 Skills¶

Skills 是可重用的知識文件（SKILL.md），包含特定領域的專業指令和流程。

### 11.2 使用 Skills¶

```
skill tool 自動加載 SKILL.md 文件內容到對話中

```

### 11.3 Skills 存放位置¶

| 範圍 | 路徑 ||---|---|| 全局 | ~/.config/opencode/skills/ || 項目 | .opencode/skills/ |

### 11.4 創建 Skill¶

每個 Skill 是一個目錄，包含SKILL.md：

```
.opencode/skills/
├── my-skill/
│   └── SKILL.md
└── another-skill/
    └── SKILL.md

```

---

## 12. Custom Tools¶

### 12.1 什麼是 Custom Tools¶

自定義工具允許你定義自己的函數，讓 LLM 調用執行任意代碼。

### 12.2 內建工具列表¶

| 工具 | 說明 ||---|---|| bash | 執行 Shell 命令 || edit | 修改現有文件 || write | 創建新文件或覆蓋現有文件 || read | 讀取文件內容 || grep | 正則表達式搜索文件內容 || glob | 模式匹配查找文件 || lsp | 語言服務器協議（定義跳轉、引用查找等） || apply_patch | 應用補丁文件 || skill | 加載 SKILL.md 文件 || todowrite | 管理待辦列表 || webfetch | 獲取網頁內容 || websearch | 使用 Exa AI 搜索網路 || question | 向用戶提問 |

### 12.3 底層技術¶
- grep和glob使用ripgrep（rg）- 默認遵守.gitignore規則- 可使用.ignore文件覆蓋忽略規則
---

## 13. 權限控制（Permissions）¶

### 13.1 權限級別¶

| 級別 | 說明 ||---|---|| allow | 允許所有操作，無需批准 || ask | 執行前需要用戶批准 || deny | 禁用該工具 |

### 13.2 權限鍵¶

| 鍵 | 控制的工具 ||---|---|| read | read || edit | write, edit, apply_patch || glob | glob || grep | grep || list | list || bash | bash || task | task || external_directory | 訪問項目目錄外的文件 || todowrite | writetodo, readtodo || webfetch | webfetch || websearch | websearch || lsp | lsp || skill | skill || question | question || doom_loop | 代理卡住時的恢復提示 |

### 13.3 精細控制（Glob 模式）¶

```
{
  "permission": {
    "edit": "deny",
    "bash": {
      "*": "ask",
      "git diff": "allow",
      "git log*": "allow",
      "grep *": "allow",
      "rm -rf *": "deny"
    },
    "webfetch": "deny"
  }
}

```

### 13.4 通配符控制¶

```
{
  "permission": {
    "mymcp_*": "ask"
  }
}

```

### 13.5 每個代理獨立權限¶

```
{
  "permission": {
    "edit": "deny"
  },
  "agent": {
    "build": {
      "permission": {
        "edit": "ask"
      }
    }
  }
}

```

### 13.6 Markdown 代理權限¶

```
---
description: Code review without edits
mode: subagent
permission:
  edit: deny
  bash:
    "*": ask
    "git diff": allow
    "git log*": allow
---

```

---

## 14. MCP 服務器¶

### 14.1 什麼是 MCP¶

MCP（Model Context Protocol）服務器允許集成外部工具和服務，包括數據庫訪問、API 集成、第三方服務等。

### 14.2 MCP 配置¶

```
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "jira": {
      "type": "remote",
      "url": "https://jira.example.com/mcp",
      "enabled": true
    }
  }
}

```

### 14.3 遠程 MCP¶

組織可以通過.well-known/opencode端點提供默認 MCP 服務器，用戶可以按需啟用。

### 14.4 使用場景¶
- 數據庫查詢- Jira / GitHub 集成- 第三方 API 調用- 內部工具集成
---

## 15. 插件系統¶

OpenCode 支持插件擴展功能。插件存放在.opencode/目錄下：

```
.opencode/
├── agents/     # 自定義代理
├── commands/   # 自定義命令
├── skills/     # 自定義技能
└── plugins/    # 插件

```

---

## 16. GitHub 與 GitLab 集成¶

### 16.1 Git 安全網¶
- 自動快照：每次更改前自動創建 Git 快照- /undo：撤銷上次消息的所有更改- /redo：重做已撤銷的更改- /share：生成會話分享鏈接

### 16.2 會話分享¶

```
/share

```

創建當前對話的分享鏈接，複製到剪貼板。

示例：https://opencode.ai/s/4XP1fce5

### 16.3 Git Worktrees¶

OpenCode 支持 Git Worktrees 作為隔離策略，讓 AI 在獨立分支工作而不影響主分支。

---

## 17. 注意事項¶

### 17.1 ⚠️ 重要提醒¶

| 項目 | 說明 ||---|---|| 版本清理 | 安裝前移除 0.1.x 之前的舊版本 || Git 依賴 | /undo 和 /redo 需要項目是 Git 倉庫 || API Key 安全 | 妥善保管 API Key，不要提交到公共倉庫 || 模型選擇 | 根據任務複雜度選擇合適的模型，平衡成本和質量 || 權限設置 | 生產環境建議設置bash: "ask"防止意外命令執行 || 上下文管理 | 長對話使用/compact壓縮上下文以節省 token || 命名衝突 | 如果創建與 OpenCode 相關的項目，名稱中必須包含註釋說明非官方 |

### 17.2 成本優化建議¶

| 策略 | 說明 ||---|---|| 分層模型 | 簡單任務用便宜模型（Haiku），複雜任務用貴模型（Sonnet/Opus） || small_model | 配置 small_model 用於標題生成等輕量任務 || temperature | 降低 temperature 減少不必要的多樣性輸出 || steps 限制 | 設置最大步驟數控制成本 || 本地模型 | 使用 Ollama 完全免費 |

### 17.3 安全最佳實踐¶

| 實踐 | 配置 ||---|---|| 禁止危險命令 | "bash": { "rm -rf *": "deny" } || 編輯需批准 | "edit": "ask" || 外部目錄禁止 | "external_directory": "deny" || Web 訪問控制 | "webfetch": "deny"或"ask" |

---

## 18. 與 Claude Code / Codex 比較¶

### 18.1 架構對比¶

| 維度 | Claude Code | Codex | OpenCode ||---|---|---|---|| 運行時 | 本地終端 / 雲端 VM | 本地 CLI/應用/IDE（+ 雲端） | 本地 Client-Server || 語言 | TypeScript/Node.js | Rust（CLI） | Go（TUI）+ Bun/JS（服務器） || 模型鎖定 | 僅 Claude | 僅 GPT-Codex | 75+ 提供商 + 本地 || LSP 集成 | ❌ | ❌ | ✅（獨有功能） || 代理協調 | Teams 2-16 個代理 | 多任務並行 | 子代理 || 開源 | ❌ | 部分（CLI Apache 2.0） | ✅ MIT |

### 18.2 基準測試¶

| 測試 | Claude Code | Codex | OpenCode ||---|---|---|---|| SWE-bench Pro | Opus 4.6: 57.5% | GPT-5-Codex: 57.0% | 取決於使用的模型 || Terminal-Bench 2.0 | Sonnet 4.6: 53.0% | GPT-5 Codex: 53.0% | 取決於使用的模型 || 速度（相同模型） | 基準 | 相近 | 慢 78%，但更徹底 |

### 18.3 價格對比¶

| 工具 | 最便宜 | 中等 | 高端 ||---|---|---|---|| OpenCode | $0（Ollama 本地） | API 費用 | $200/月（Black） || Claude Code | $20/月（Pro） | $100/月（Max 5x） | $200/月（Max 20x） || Codex | $0（免費/Go） | $20/月（Plus） | $200/月（Pro） |

### 18.4 選擇建議¶

| 需求 | 推薦 ||---|---|| 模型靈活性 + 零鎖定 | OpenCode || 本地隱私 | OpenCode + Ollama || 開源代碼可 fork | OpenCode || LSP 智能代碼分析 | OpenCode || 最快上手 + 最簡單 | Codex（ChatGPT 自帶） || 最高代碼質量 | Claude Code || GitHub PR 自動創建 | Codex |

---

## 19. 推薦工作流程¶

### 19.1 日常開發流程¶

```
1. cd /path/to/project
2. opencode                              # 啟動 TUI
3. /init                                  # 初始化項目（首次）
4. Tab → Plan                             # 切換到規劃模式
5. "幫我分析一下當前項目的架構..."          # 分析
6. Tab → Build                            # 切換到構建模式
7. "幫我添加這個功能..."                    # 執行
8. /undo                                  # 如果不滿意，撤銷
9. 調整提示詞，重試                         # 迭代
10. /share                                # 分享給團隊

```

### 19.2 代碼審查流程¶

```
1. @explore 幫我找出所有與認證相關的文件    # 快速探索
2. @code-reviewer 檢查這段代碼的質量        # 使用自定義代理
3. @general 幫我搜索這個庫的最佳實踐        # 外部研究

```

### 19.3 成本優化配置¶

```
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-20250514",
  "small_model": "anthropic/claude-haiku-4-20250514",
  "agent": {
    "build": {
      "model": "anthropic/claude-sonnet-4-20250514",
      "temperature": 0.3
    },
    "plan": {
      "model": "anthropic/claude-haiku-4-20250514",
      "temperature": 0.1,
      "steps": 10
    },
    "code-reviewer": {
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4-20250514",
      "permission": { "edit": "deny" }
    }
  }
}

```

---

## 20. 快速參考速查表¶

### 20.1 安裝¶

```
curl -fsSL https://opencode.ai/install | bash

```

### 20.2 啟動¶

```
cd /path/to/project && opencode

```

### 20.3 常用快捷鍵¶

| 快捷鍵 | 功能 ||---|---|| Tab | 切換代理（Build ↔ Plan） || Ctrl+X | 前導鍵（Leader Key） || Ctrl+X N | 新會話 || Ctrl+X U | 撤銷 || Ctrl+X R | 重做 || Ctrl+X C | 壓縮會話 || Ctrl+X M | 列出模型 || Ctrl+X T | 列出主題 || Ctrl+X L | 列出會話 || Ctrl+X P | 命令面板 |

### 20.4 常用命令¶

| 命令 | 說明 ||---|---|| /connect | 添加 API Key || /init | 初始化項目 || /new | 新會話 || /undo | 撤銷 || /redo | 重做 || /compact | 壓縮上下文 || /share | 分享會話 |

### 20.5 文件引用格式¶

```
@文件路徑        → 引用文件
!shell 命令      → 執行 Shell 命令
$ARGUMENTS       → 命令參數
$1, $2, $3       → 定位參數

```

### 20.6 關鍵資源¶

| 資源 | 連結 ||---|---|| GitHub | https://github.com/anomalyco/opencode || 官網 | https://opencode.ai || 文檔 | https://opencode.ai/docs || Discord | https://discord.gg/opencode || X.com | https://x.com/opencode || 安裝指南 | https://opencode.ai/docs#install || 配置指南 | https://opencode.ai/docs/config || 代理文檔 | https://opencode.ai/docs/agents || 命令文檔 | https://opencode.ai/docs/commands || 工具文檔 | https://opencode.ai/docs/tools || TUI 文檔 | https://opencode.ai/docs/tui || 影片教程 | https://www.youtube.com/watch?v=__bcJHoTE08 |

---

本文檔基於 Rick Hau 的 OpenCode 教學影片（2026-06-05）及官方文檔編制。分析時間：2026-06-09 08:52 HK版本：v1.0
==================== 影片完整原文逐字稿 ====================

# 📝 影片完整原文逐字稿

> 📌 重要說明：此逐字稿由 Vosk 離線語音識別（ASR）自動轉錄完整影片音訊，經 183 處手動修正 ASR 識別錯誤。影片原始作者：Rick Hau (@rickhau99) ｜影片日期：2026-06-05技術細節：使用 Vosk 中文小模型（vosk-model-small-cn-0.22，42MB）進行 CPU 推理；處理 20:47 音訊耗時約 9 分鐘。識別率：約 75-80%（中文小模型極限），建議對照影片圖像理解。
🎙️ 影片資訊：Rick Hau ｜ 不得不裝的 AI 代理工具｜GitHub 萬星項目｜OPENCODE📊 統計：20:47 完整音訊 / 原文 10,693 字元 / 修正後 10,565 字元🔧 已修正：183 處 ASR 識別錯誤
大家好 本期 视频 为 为 大家 详细 介绍 一款 开源 AI 編程代理 OpenCode 对 工具 目前 在 GitHub 上 拥有 超过 十七萬 可行 可以说 是 目前 最 受欢迎 的 开源 AI 編程助手 之 一 如果 你 已经 在 有 OpenCode 的 你 可能会 觉得 他 无非 就是 一个 终端 里 的 聊天 工具 但 说实话 大多数 用户 只用 到 他 能力 的 一 小 部分 这 既是 别 误会 从零开始 但 你 逐步深入 安装 交互 模式 命令行 斜線指令 模型 配置 配置 文件 详解 在 到 自定义 智能 体MCP 服務器 就 开始 插件系統 以及 完整生態 无论 你 是 刚 说 OpenCode 的新手 还是 已经 用 很久 的 老用户 我相信 看完 之后 多么 发现 一些 让 效率 翻倍 的 用法 着 我們正式開始 安裝 OpenCode 得 非常 简单 最 推荐 的 方式 是 通过 官方 安装 脚本 一條命令即可搞定 如果 你 更 习惯 用 包管理器 OpenCode 别致 是 多种 安装 方式 macOS 用戶 的 用户 可以 用 Brew 推薦 太 不 的 因为 他 的 更新 频率 更高 Linux 用戶 可以 通过 嗯 便 全权 装 当然 批文 偏僻 按 让 也 都 可以 温度 是 用户 推荐 的 大不了 喜爱 我 下 使用 疑惑 和 的 最佳 体验 也 可以 直接 通过 Chocolatey 勾 Scoop 安裝 啊 之类 的 惊呼 用 拍 满 即可 如果 你 想 装 特定 版本 和 理想 安装 还 可以 直接 去 GitHub 的 人 历史 页面 下载 对应 平台 的 二进制 文件 安裝完成後，在終端輸入 OpenCode 的 怎么 启动 他 的 交互 界面 也 就是 TUI 終端用戶界面 这时候 OpenCode 的 最 核心 的 使用 方式 你 做 的 对话 代码 修改 文件 操作 都 在 这个 终端机 命理 完成 此外 有 爱 OpenCode 還支援其他幾種方式 如果 你 不 想 就 交互 界面 只 想 快速 问 一个 问题 可用 CLI 模式 这个 模式 尤其 适合 脚本 话 和 CI 集成 另外 OpenCode 他 提供 桌面 應用和 IDE 擴展 如果 你 更 习惯 在 圖形界面操作 这 两个 也是 不错 的 选择 还有 一个 很容易 被 忽略 的 外部 模式 一些 我们 靠 的 外部 之后 会 自动 打开 浏览器 你 可以 直接 的 网页 里 可爱 对话 界面 和 TUI 幾乎一致 甚至 可以 通过 局域網讓其他設備連上 了 使用 接下来 我们 就 进入 OpenCode子 的 实际 交互 看看它最基礎的兩種工作模式 其中 我 打開 OpenCode 進入一個項目 目錄後 首先 会 接触 到 两个 那 这个 主 智能 智能體 的 与 厂 配件 就能 在 两者 之间 切换 界面右下角 会 显示 当前 处于 哪个 模式 Build 模式是默認模式 拥有 完整 的 文件 读写 和 命令執行權限 呢 日常 的 编码 修改 重構都通過它 了 完成。Plan 模式 则 完全 不同 的 是 一个 只读 的 规划 模式 不能修改任何文件 也 不能 执行 命令 它的作用是讓你在動手之前 先把方案想清楚 這個分工在實際使用中非常實用 比如 你 接手 一个 陌生的代碼庫 或者 做 一个 涉及 多 个 文件 的 改动 先用 Plan 模式 把你的需求告訴它 他 会 分析 代码 结构 然后 给 你 一个 详细 的 执行 方案 当你覺得沒問題 再切換到 Build 模式 让 他 执行 很多糟糕的問題發生 不是 因为 能力 不够 而是 提示詞沒寫清楚 或者 改 了 不該動 的 地方 不 那么 是 相当于 给 你 加 了 一個確認環節 能 避免大量返工 着 Build 模式和 Plan 模式 OpenCode 的 还 内置 了 几个 子智能體 你 可以 在 输入框 里 用 按 符号 和 呼叫 他们 比如 @explore 是一個只讀的代碼探索助手 擅長在大型項目裡快速找到 某功能的具體位置 @general 是 一个 通用 的 字 这么 题 是 和 处理 那些 不需要 污染 主 对话 上下文 的 独立 任务 Plan 模式。再來 看 几个 最常用的基礎操作 @ 符號除了 呼叫子智能體 能 提 还 可以用來模糊搜索 項目中的文件 輸入 @ 後再 再说 文件名 鬆手後，文件內容會自動 加入對話中 如果 想 二 少爷 改 某 个 组建 用 @ 找到它 然后 描述 你 的 修改 需求 回车 搞定 当 輸入框以 ! 開頭 可以 直接執行 Shell 命令 比 太 好 而 爱 死 瀏覽當前目錄結構 跑測試 她 [unk] 查看倉庫狀態 你 在 做 这些 的 时候 完全不用退出 扣 的 爱 自己 会 主动 用 這些命令 比如 代码 自动 跑測試看有沒有錯誤 如果 你 做 的 是 前端項目 或者 是 需要 AI 理解 直接拖入截圖到終端即可 OpenCode 會自動讀取圖片內容 并 将 对话 跟 口头 描述 比 起来 一張圖往往能節省幾百字提示詞 另外 OpenCode 還支援 支援粘貼剪貼板圖片 不用先存文件再拖拽 最后 说 一下 /init 是很多新手容易跳過 的步驟 /init 後 的 会 自动 扫描 旅游 目錄結構、技術棧和代碼規範 然後在項目根目錄生成 AGENTS.md 這個文件就是 AI 閱讀的項目說明書 裡麵包含你的項目概要 目錄結構 构建 命令 以及 代码 风格 等 的 有了它之後，AI 每次回答 都会 都更貼合你的實際項目 你 可以 把 这个 文件 提交 的 并 仓库 里头 的 成为 讓團隊共享同一套規則 这 一分鐘不到的效果 立竿見影。反而忽略它的人 往往會發現需要反覆給出 不符合 项目 规范 的 代码 OpenCode 的 的 命令 分为 两大类 的 案例 斜线 开头 交互 命令 和 众多 你 直接 掉 了 相爱 命令 我们 先 看 题 玩命 命理 谢谢 可 是 最 基础 的 命令 用 它 来 连接 模型 共商 区 扣 的 通过 基层 猫 都 改 第一杯 平台 支持 超过 七十五 的 但 模型 共商 从 欧派 哎 哎 所 家 狗 悲催 哥 等 云端 大厂 当你 本地 通过 啦 吗 运行 的 模型 全部 通知 住 谢谢 可 难 后 会 列出 所有 可用 的 工商 选择 一个 供应商 然后 数 对应 的 一篇 既 即可 集中 后台 那 这 自家 的 扣分 扣 的 阵营 里面 提供 了 机会 完全 免费 的 模型 无需 注册 即可 使用 速度 与 能力 均衡 日常 开发 完全 够用 谢谢 猫 的 用来 查看 和 切换 当前 可用 的 模型 列表 输入 后悔 列出 你 已经 连接 的 所有 模型 你 可以 直接 在 里面 切换 如果 你 家 了 多 个 供应商 的 命令 能 让 你 快速 对比 和 选择 同一 个 绘画 里 随时 可以 奇幻 模型 无需 重启 或 重建 对话 机械 三 婶 用 查看 你 的 历史 绘画 行列 表 区 回扣 的 会 保存 你 所有 的 对话 记录 通过 这个 命令 可以 浏览 搜索 和 恢复 之 前 的 绘画 每个 绘画 的 自己 的 标题 和 摘要 方便 快速 定位 这些 现在 会 把 当前 整个 对话 生成 一个 分享 链接 并 不知道 简帖 把 对方 打开 后 这么 看到 你 完整 的 提问 和 爱 的 回复 在 团队 协作 和 向 别人 求助 的 时候 非常 实用 分享 是 手动 出发 的 默认 不会 自动 上传 任何 内容 谢谢 安度 是 很多人 最喜欢 的 命 之 一 没 自爱 做出 文件 修改 前 区 扣 的 会 自动 创建 一个 给 的 快照 该 错 了 或者 不 满意 之间 安度 几个 回头 的 放 一部 脱 连续 撤销 多次 没 什么 改动 都 找 回来 如果 要 恢复 刚才 的 撤销 谢谢 锐度 即可 最 和 用来 切换 显示 模型 的 推理 过程 开启 后 你们 看到 烟 是 怎么 一步步 分析 问题 的 答案 的 这个 功能 不仅仅 是 为了 满足 好奇心 更重要 的 是 你 能 判断 他 的 思考 方向 对不对 如果 发现 的 例子 偏 了 及时 纠正 几个 不用 等 他 跑偏 了 在 反攻 当然 你 也 可以 支书 一条 斜线 系统 将 自动 弹出 可用 的 命令 供选择 和 和 装 下 快捷键 可 出家 屁 在 弹出 的 面板 中 可以 看到 更 详细 的 使用 功能 比如 切换 绘画 历史 消息 跳转 以及 切换 主题 等等 住在 体外 比 交互 欧佩克 后台 提供 了 一套 完整 相爱 恐惧 让 你 可以 在 中国 你 直接 操控 OpenCode 无需 就 交互 界面 这 是 命令 在 脚本 话 些 基地 和 日常 誉为 躺着 中 非常 有用 哦 本科 的 乱 非 交互 模式 的 入口 直接 在 终端 你 跟 一段 提示 词 我 跟 扣子 就 在 当前 没 下车 任务 并 反馈 结果 正 模式 知识 丰富 的 参数 猫 都 可以 指定 模型 雷震 的 指定 智能 在 可以 附带 文件 孝子 用 自动 分享 结果 你 可以 把 它 将 到 西安 流水线 种 代码 提交 后 自动 袍子 代码 审查 过 的 自动 生成 变更 日志 区 本色 不 油 启动 一个 汉族 烂 后端 而 我们 的 外部 不仅 可以 启动 后 短 还 可以 自动 打开 浏览器 设置 这个 环境 变量 即可 开解 一只 鸡 的 屁 基本 认证 局域网 内 其他 设备 也 可以 发现 这 的 机器 上 的 问 扣 的 服务 通过 二分 扣 的 他 只 让 题 外联 知道 一个 已经 运行 的 后端 并 呢 在 服务器 上 跑 的 口算 然后 在 本地 用 OpenCode 的 太 了 服务器端 的 地址 人 后 操作 都 在 远程 服务器 上 执行 但 界面 在 你 本地 终端 里 缝 扣子 对 行为 查看 你 的 头 很 用料 和 费用 统计 支持 按 时间 过滤 俺 模型 拆分 关 项目 过滤 入 你 同时 用 了 多 个 模型 共商 这个 命令 能 让 你 一目了然 的 看到 钱 花 在 了 哪里 区 扣 的 猫 董 家 共商 可以 列出 所有 可 模型 加上 我 不 可以 看到 没种 模型 的 定价 信息 而 瑞丰 乱石 有 强制 刷洗 墨镜 缓存 跟 用于 工商 刚 发布 新 没 的 情况 除此之外 OpenCode 三星 累死 用 查看 所有 绘画 有 得 累 的 加上 指定 的 哎 的 既 可以 删除 制定 绘画 我 不 扣子 家 泡 的 用来 导出 绘画 为 阶层 奔跑 的 字 可以 支持 从 本地 文件 和 区 跟 的 分享 链接 导入 历史 绘画 我们 还 可以 使 晚期 漆皮 命令 来 添加 鸡皮 服务器 和 查看 以 配置 单 嬉皮 及其 连接 状态 通关 鸡皮 傲娇 名称 完成 区 授权 转载 一批 大海 还有 一种 通过 问 扣 得 真 快 创建 新 的 自定义 智能 起 选定 名称 描述 权限 模型 然后 自动 剩 的 配置 文件 OpenCode 得 真 的 累死 可以 查看 所有 以 配置 整体 另外 开始 欧盟 叩 打 的 着 的 扣 的 升级 到 最新 版本 最后 如果 你 厌倦 楼盘 扣子 可以 时候 扣篮 的 报告 称 卸载 OpenCode 的 并 清理 所有 文件 什么 提高 的 扣 的 不 强制 绑定 任何 一家 模型 厂商 他 的 模型 管理 核心 在 油泵 抠 的 的 止损 中 的 几个 配置 像 掌握 之后 你们 时间 不同 任务 用 不同 模型 同期 相比 随时 切换 甚至 把 账单 控制 到 最低 OpenCode 的 神功 工商 模型 名称 的 格式 来 指定 模型 在 区 丰厚 的 的 止损 中 猫 都 配 不 像 指定 主 模型 毛毛 都 可以 指定 小 任务 模型 记住 死 毛毛 的 可以 用 标题 生成 咱 要 等 清亮 人 欧佩克 的 内部 会 自动 将 真 的 操作 路由 的 小 模型 帮 你 在 不 应当 主任 质量 的 前提 下 降低 费用 如果 没 碰 死 猫猫 的 我 不 扣 的 会 尝试 使用 当前 共商 动脚 便宜的模型 找 不 回 退到 主 模型 如果 你 想 对 某 工伤 的 连接 行为 做 精细 控制 在 可以 通过 不外 的 来 配 就像 这样 啊 增 白 的 让 用户 还 可以 配 阮总 部分 的 的 等于 打 不 啊 可 参数 我们 还 可以 使用 卑琐 不 歪 的 虽然 内部 歪 的 来 控制 工伤 的 黑白 名单 激动 黑名单 列表 中 的 共商 局 使用 凭证 也 不会 被 加载 白名单 中 只 加载 列表 中 的 共商 局 全部 忽略 但 如果 同时 配置 的 两个 参数 杯子 不 此外 的 的 优先级 会 更高 不 想 花钱 话 部分 扣 的 资源 提供 计划 完全 免费 的 模型 日常 开发 完全 够用 这 烟 的 模型 列表 在 持续 更新 有 欧佩克 的 猫 的 OpenCode 的 可以 查看 最新 行情 如果 你 更 关心 隐私 和 着 而 需要 离线 工作 可以 搭配 啦 马熊 本地 模型 与 代码 全程 不 出 电脑 谁 完全 的 理想 说 变成 体验 OpenCode 的 核心 配置 有 一个 OpenCode子 在 节省 监控 控制 如果 这个 文件 弄清楚 你 就 掌握 老板 扣 的 绝大部分 的 定制 能力 配置 文件 可以 放 得 多 个 位置 区 被扣 的 会 啊 优先级 从 低 到 高 着 曾 合并 后 加载 的 覆盖 前面 的 同名 字段 非 冲突 字段 的 全部 保留 日常 使用 中 前 四 的 就 够了 简单 来说 车 放 通用 配置 项目 放 项目 专用 配 区 扣 的 同时 知识 阶层 和 这次 是 有 两种 文件 格式 这里 推荐 后者 这样 你 可以 在 配置 密切 注视 方便 自己 和 团队 成员 理解 没 像 用途 为 加上 到 了 父子 该 马刺 的 后 大多数 编辑器 还 能够 自动 补全 和 校验 我们 认识 权限 策略 的 也 可以 做 什么 操作 没 人 全部 放开 也 可以 收紧 有 二十个 表示 每次 操作 真 询问 对内 则 会 直接 禁止 他 制止 让 共 军 类别 单独 设置 印刷 整治 可以 指定 而言 需要 遵循 的 规则 文件 如果 你 的 咳嗽 现成 的 入 死后 可靠 的 展地 就 可以 直接 饮用 通过 事儿 可以 分享 你 绘画 他 有 三种 模式 手动 自动 和 仅 用 那 少 的 表示 是否 启用 变更 快照 默认 开启 哎 每次 修改 前 创建 更 快 到 让 你 能 用 血 写 安度 回 这 大 仓库 和 多 怎么 行 项目 中 如 感到 卡顿 可以 设置 放肆 关闭 但 代价 就是 失去 撤销 能力 跟 拍 拍成 表示 上下文 压缩 策略 哎呀 对话 长 了 会 但 很多 偷 偷看 OpenCode 的 会 自动 压缩 历史 上下文 区 头 控制 是否 自动 压缩 不爱 控制 是否 一 出 就 的 工具 输出 约瑟夫 的 可以 设置 压缩 是 保留 的 偷看 和 充值 再来 看 笑 他 给 指标 OpenCode 的 使用 的 终端 默认 自动 检测 系统 说 坑 显示 设置 别 温度 下 可以 设置 坏 打 不 打 虽然 我 先帝 的 呀 踢 北方 的 一 整个 可以 设置 启动 时 的 没 人 只能 提 你 习惯 先 规划 再 动手 被 设置 为 不 让 我 这 可以 设置 文件 监控 忽略 模式 避免 建立 生动 的 猫 等 着 给 的 真 的 目录 而 地位 语言 夫妻 协议 他 呀 能够 在 开发 过程 中 读取 福气 返回 的 诊断 信息 并 主动 发现 自己 写 的 错误 而外 屁 默认 为 关闭 状态 设备 住 则 会 开启 所有 的 妹子 海外 屁 或者 可以 将 某 个 特定 的 语言 信用 可 慢 的 是 自定 命令 可以 把 你 的 长期 词 变得 斜线 命 的 模式 他 只是 船舱 还 可以 选择 执行 职能 题 和 指定 专人 模型 悲愤 的 用于 自定义 智能 题 可以 为 不同 角色 冲着 不同 的 也 助手 氨气 屁 用 阳气 佩服 其 配置 连接 外部 工具 和 数据源 抓紧 之 插件 列表 通过 安排 命令 安装 的 插件 写 在这里 以上 这些 配置 像 不需要 全部 在 初始化 化石 就 写好 建议 从 最 经典 的 配置 开始 是 好猫 逗号 的 啊 不对 的 然后 在 日常 使用 中 逐步 添加 区 扣 的 慢 提供 被 的 和 不 了 两个 主 能 提单 你 完全 可以 创建 自己 的 专属 角色 每个 真 的 你 可以 独立 配置 指定 专用 模型 设定 温度 限制 最大 值 部署 分配 不同 的 权限 以及 配置 专属 的 系统 提示 词 的 春天 之 道 题 有 两种 方式 最 简单 的 生命 的 行 直接 输入 这个 命令 会 赢 当你 完成 选 名称 写 描述 勾 选 选举 的 权限 指令 模型 然后 自动 上 的 配置 文件 当然 可以 手动 的 相应 的 目录 下 创建 但 文件 文件名 就是 这种 挺 名称 内容 要 么 的 生命 配置 正文 就是 系统 提示 比如 创立 一个 只读 的 代码 审查 整体 的 暗地 很 想 代表 只能 提案 模式 可以 的 三类 中 正当 体积 不 来 和 别 的 可以 用 太 部件 之间 切换 资质 抗体 可以 通过 爱 的 呼叫 和 背弃 他 这么 体内 不 调用 深奥 的 两种 方式 都 支持 还有 一个 嗨 的 选项 是 处 后 该 子峥 个体 不会 出现 在 爱 的 菜单 总值 那个 程序 内部 调用 适合 用来 做 系统 内部 的 自动化 任务 你 可能会 有 一些 反复 用到 的 提示 词 留 跑 一边 测试 然后 分析 失败 的 用力 并 修复 他们 每次 都 收到 会 很麻烦 此时 我们 就 可以 把 这样 提示 词 变成 撕逼 命名 不用 谢谢 废 太子 猪肉 后 就 直接 执行 创建 方式 与 真 挺 类似 只需 在 OpenCode 的 的 甄审 中 配 嘴馋 那 融资 的 命 的 知识 几种 特殊 语法 你 可以 使用 到 了 复杂 给面子 了 代替 命 后面 跟着 所有 参数 和 熊 到 了 服 一二三 分别 代表 第一 第二 第三 的 位置 的 参数 另一个 强大 的 个性 是 将 命令 的 输出 住 到 提示 此种 只需 在 模板 中 写上 繁衍 好 在 太 好 以及 中 的 命令 这样 革命 的 输出 内容 最终 会 直接 作业 题 这次 注入 并 重新 的 体质 差 下面 我们 来 看一下 死掉 死 就是 是 一种 可以 不用 的 记得 模块 通道 放 的 资料 木下 的 死掉 的 的 文件 中 哎 会 在 需要 时 自动 加载 只有 你 问 了 一个 关于 部署 的 问题 哎呀 检测 的 给 的 为 例子 这个 死掉 就会 扎 在 他 的 内容 了 指导 操作 欧巴 叩打 键入 考 扣 的 的 有 梦 路 两边 可以 共享 同 一套 技能 酷 就是 鼻子 拼命 的 更灵活 命令 是 固定 的 提示 怎么 把 死 就此 上下文 感知 的 指令 及 适合 那些 只 在 特定 场景 下 的 需要 的 林芝 是 不用 每次 都 塞 到 一 的 自然地理 比如 荣誉 项目 有 一套 特殊 的 部署 流程 写 个 被 破坏 就 说 在 做 部署 相关 操作 操作时 会 自动 加载 然后 我们 再来 看看 更 破 这 是 最 硬核 和 的 境界 功能 前面 讲 的 都是 配置 层面 的 定制 看似 挣脱 可以 让 给 说 血腥 的 工具 的 不 让 说 在 对话 中 可以 直接 掉 你 写 函数 只 听 一声 太子 的 和 当时 快递员 编写 通常 放在 以下 两个 目录 中 这里 创建 了 一个 名 为 被 被子 的 工具 可以 在 对话 中 职业 调用 他 来 查询 数据库 尊 工具 定义 用 惕然 时候 电子 语言 的 孩子 内部 求 黑 调用 任何 语言 用 拍手 笑 够 的 你 只要 用 逼问 的 道孚 和 的 普赛 发起 挤掉 即可 OpenCode 的 权限 系统 表面 看起来 强大 得 多 前面 提到 过 妹子 怎么 基本 用法 但 真正 的 威力 在于 它 的 犀利 的 控制 权限 黑暗 共 类别 单独 设定 为 的 爱 得 鬼子 等 每个 类别 可以 是 简单 的 往 啊 我 得 那 也 可以 是 个 对象 有 个 老婆 模式 匹配 具体 操作 对 办事 自己 兴趣 控制 尤其 使用 全 血 还能 二 智能 题 单独 配置 你 可以 给 背后 的 智能 题 完整 权限 给 破案 的 智能机 全部 本来 为 六 真 的 体质 都 权限 甚至 控制 某 个 呢 题 可以 掉 哪些 子峥 总体 而 一个 常 被 忽略 的 是 的 的 责任 只 权限 控制 人 能否 东西 项目 之外 的 文件 这个 奔波 不能 微博 和 多 多项 连接 汤 底下 特别 关键 安睡 脆皮 是 一种 标准 协议 老爷 能够 掉 外部 工具 和 数据源 欧佩克 的 人生 资产 鸡皮 你 可以 在 OpenCode子 的 甄审 中 佩兰 鸡皮 服务器 配置 完成 后 哎呀 能够 在 对话 中 之 查询 外部 服 厂 脆皮 分 两种 类型 楼 口语 六 楼 普通 的 地面 的 启用 韦某 的 连接 远程 服务 最 典型 的 三 场景 包括 看 三 稳 查询 最新 文档 传统 说 变动 住 手机 训练 数据 支持 有 截止 日期 监控 探头 的 三个 后边 的 实时 搜索 最新 的 库 文档 减少 过 建议 配置 好 后 再 提 支持率 加 一句 使用 狂 太 塞 稳 查询 罪行 文档 就会 主动 去 检索 三尺 用于 查询 现场 报名 错 就 三权 批评 后 人 都 不 直接 督军 你 线上 的 出 日志 遇到 暴食 不 叫 手动 复制 粘贴 报复 信息 哎呀 自己 会 去 三成 你 查 被子 他 需要 完成 区 授权 然后 又 最好 命令 完成 一次 浏览器 授权 之后 怎么 正常 使用 一批批 我 搜索 GitHub 代码 有 收 你 不知道 蘑菇 库 怎么 用 想 看 别人 是 怎么 写 的 怪不得 一批批 虽 个 搜索 GitHub 公开 代码 的 服务 结束 后 人 能够 直接 查找 真实 用地 处理 上 三个 任何 时间 自闭 协议 的 服务 都 能 接入 你 用 最好 命令 可以 交互 是 添加 有 最好 命 来 查看 以 配置 的 服务 几年 罪状 谈 另外 OpenCode 的 也 支持 从 便 安装 社区 插件 安装 后 插件 和 修改 和 着 后 的 行为 社区 中 比较 活跃 的 插件 还 包括 偶尔 猫 他 提供 里头 轻量级 项目 管理 工具 让 你们 更高效 的 在 多 不 之间 切换 和 管理 对话 上下文 如果 你 有 特定 需求 区 扣 他 提供 而 的 可以 供 里 开发 自己 的 插件 插件 可以 注册制 的 钩子 添加 意外 组建 扩展 各类 功能 同时 时候 扣 的 提供 深度 给 的 平台 集成 在 给 他 仓库 中 运行 你 下 命令 这 会 自动 配备 GitHub爱 婶子 工作流 之后 仓库 就 帽子 些 过程 中 调用 区 透 的 来 做 自动化 代码 审查 自动 生成 二逼 描述 等 操作 对于 日常 使用 与 蜡笔 用户 同样 可以 用 对应 的 及 成功 等 呃 编码 工具 不 应该 只 在 本地 它 可以 将 到 宁 协作 流程 中 这种 审查 减少 人工 过 代码 时间 家 中 的 自动 修复 让 派 破烂 不再 应 格式 问题 来回 折腾 着 到 这里 我们 抠 的 的 着 功能 就 讲完 了 从 安装 启动 交互 博士 偿命 命令 模型 配置 配置 体系 到 自定义 只能 体制 的 命名 的 死 就 高盛 吐 权限 控制 一件 坏 坏 插件 和 给 的 集成 我们 基本 覆盖 了 OpenCode 的 的 完整 能 里面 如果 你 是 新 用户 建议 从 前面 几节 开始 先 把 基础 操作 没 熟 在 逐步深入 后面 的 进阶 功能 如果 你 是 老用户 希望 刚才 的 境界 部分 的 给 你 一些 新 的 启发 注意 区 抠 的 说 关 全面 说 开源 的 说 变成 等 安装 方式 和 相关 链接 详解 視頻介紹。如果在 向外 和 使用過程中有提示 要求收費，請一定不要相信 着 如果 你 的 今天介紹的某個功能產生興趣 那么 这个 影片 中 发挥 着 作用 现在 我 很想聽聽 你 的 想法 呢 目前 个 熊 那 款 AI 編程工具 是 有 尝试 过 OpenCode 以及 最 感兴趣 的 是 哪个 经 在 下面 的 评论 区 进行 留言 這裡是偽代碼 频道 實例為 本期 视频 到这里 我们 下期再見
💡 閱讀提示：- 此為完整影片逐字稿，涵蓋全部 16 個章節- 影片無官方字幕，本逐字稿由小 E 透過 Vosk 離線 ASR 自動生成- 已修正 183 處 ASR 識別錯誤（主要是「OpenCode」被誤識別為「我們扣/欧盟扣」等）- 由於是語音轉錄，部分專業術語可能仍有誤差，建議對照影片圖像==================== 影片原始描述 ====================

# 📄 影片原始描述

> 影片作者：Rick Hau (@rickhau99)影片連結：https://www.youtube.com/watch?v=__bcJHoTE08

### 影片簡介

這裡分享了一個 AI 代理工具 OpenCode，此工具可與 Claude Code、Codex等並駕齊驅。目前此項目在 GitHub 上已獲讚 17 萬+。希望你能喜歡，別忘點贊訂閱哦！

APP下載地址：

opencode  🌟🌟🌟🌟 170k
https://github.com/anomalyco/opencode

### 章節列表（作者提供）

| 時間 | 章節 | 時長 ||---|---|---|

📚 OpenCode 深度分析與完全指南（含完整影片原文）

基於 Rick Hau 影片教程（2026-06-05）、OpenCode 官方文檔、Vosk 離線 ASR 轉錄

分析時間：2026-06-09 09:35 HK | 版本：v3.0（完整版：含 20:47 影片原文）

🤖 Generated by 小E (CKC) | Pure Local · No Public Upload · 100% Open Source