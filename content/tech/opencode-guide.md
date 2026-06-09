---
title: "OpenCode - The Complete Guide to Open-Source AI Coding Agent"
date: 2026-06-09T10:00:00+08:00
draft: false
tags:
  - tech
  - ai-coding
  - open-source
  - developer-tools
summary: "Complete guide to OpenCode - open-source AI coding agent with 75+ model providers."
description: "Comprehensive guide covering installation, configuration, custom agents, commands, skills, tools, permissions, MCP integration, and comparison with Claude Code and Codex."
---





> Based on video "Must-Have AI Agent Tool | GitHub 170k Stars Project | OPENCODE" (Rick Hau, 2026-06-05)
> Video link: https://www.youtube.com/watch?v=__bcJHoTE08
> Analysis date: 2026-06-09
> Version: OpenCode v0.1.x+ (GitHub 170k+ Stars)

---

## 📑 Table of Contents
- What Is OpenCode
- Why You "Must Install" It
- Installation and Launch
- Interactive Mode (TUI + CLI)
- Common Commands
- CLI Commands
- Model Configuration
- Configuration Files
- Custom Agents
- Custom Commands
- Agent Skills
- Custom Tools
- Permission Control
- MCP Servers
- Plugin System
- GitHub and GitLab Integration
- Important Notes
- Comparison with Claude Code / Codex
- Recommended Workflow
- Quick Reference Cheat Sheet

---

## 1. What Is OpenCode

OpenCode is an open-source AI coding agent that can compete with Claude Code, Codex, and similar tools.

| Item | Details |
|---|---|
| **GitHub** | https://github.com/anomalyco/opencode |
| **Website** | https://opencode.ai |
| **Documentation** | https://opencode.ai/docs |
| **Stars** | 170,000+ |
| **License** | MIT (fully open-source) |
| **Built With** | Go (TUI, based on Bubble Tea) + JavaScript/Bun (HTTP server, based on Hono) |
| **Architecture** | Client-Server (supports Terminal TUI, Desktop App, IDE extensions, HTTP clients) |
| **Core Advantage** | **Model-Agnostic**: supports 75+ model providers, zero vendor lock-in |

### Essential Difference from Other Tools

| Tool | Core Strategy |
|---|---|
| **Claude Code** | Single model family (Claude), deep optimization, pursuing speed |
| **Codex** | OpenAI ecosystem (ChatGPT binding), pursuing integration depth |
| **OpenCode** | Model-agnostic + open-source + zero lock-in, pursuing flexibility and privacy |

---

## 2. Why You "Must Install" It

### 2.1 Core Advantages

| Advantage | Description |
|---|---|
| 🆓 **Completely Free** | MIT license, bring your own API key, zero cost for the tool itself |
| 🌐 **75+ Model Providers** | Anthropic, OpenAI, Google, DeepSeek, Groq, Ollama (local models) |
| 🔒 **Zero Vendor Lock-In** | Switch models anytime, immune to price hikes or quality drops |
| 💻 **LSP Integration** | Only AI coding agent with Language Server Protocol support |
| 🤖 **Multi-Agent System** | Build, Plan, General, Explore, Scout - five built-in agents |
| 🔧 **Highly Customizable** | Agents, Commands, Skills, Tools, MCP all configurable |
| 📱 **Multi-Surface Support** | TUI (Terminal), Desktop App, VS Code extension, HTTP API |
| 🛡️ **Security Controls** | Git snapshots + /undo + /redo + fine-grained permission control |
| 🏠 **Complete Local Privacy** | Support Ollama local models, code never uploaded |
| 🌍 **20+ Languages** | Includes Traditional Chinese (README.zht.md) |

### 2.2 Who Should Use It

- ✅ **Developers who don't want lock-in**: Don't want to be tied to Anthropic or OpenAI
- ✅ **Cost-conscious users**: Can use cheap models for simple tasks, expensive for complex
- ✅ **High privacy requirements**: Code cannot be uploaded to cloud → use Ollama local models
- ✅ **Multi-language projects**: Need different models for different languages
- ✅ **Enterprise users**: Need fine-grained permission control + MCP integration

---

## 3. Installation and Launch

### 3.1 One-Click Install (Recommended)

```bash
curl -fsSL https://opencode.ai/install | bash
```bash

### 3.2 Platform-Specific Methods

| Platform | Command |
|---|---|
| **npm** | `npm install -g opencode-ai` |
| **Bun** | `bun install -g opencode-ai` |
| **pnpm** | `pnpm install -g opencode-ai` |
| **Yarn** | `yarn global add opencode-ai` |
| **Homebrew (macOS/Linux)** | `brew install anomalyco/tap/opencode` |
| **Arch Linux (stable)** | `sudo pacman -S opencode` |
| **Arch Linux (latest)** | `paru -S opencode-bin` |
| **Windows (Chocolatey)** | `choco install opencode` |
| **Windows (Scoop)** | `scoop install opencode` |
| **Mise (any OS)** | `mise use -g github:anomalyco/opencode` |
| **Docker** | `docker run -it --rm ghcr.io/anomalyco/opencode` |
| **Nix** | `nix run nixpkgs#opencode` |

### 3.3 Desktop Applications

| Platform | Download |
|---|---|
| **macOS (Apple Silicon)** | opencode-desktop-mac-arm64.dmg |
| **macOS (Intel)** | opencode-desktop-mac-x64.dmg |
| **Windows** | opencode-desktop-windows-x64.exe |
| **Linux** | .deb / .rpm / .AppImage |

macOS install: `brew install --cask opencode-desktop`
Windows install: `scoop bucket add extras; scoop install extras/opencode-desktop`

### 3.4 Prerequisites

- **Modern terminal emulator**: WezTerm, Alacritty, Ghostty, Kitty (any one)
- **LLM API Key**: At least one model provider's API key
- **Git**: /undo and /redo features require Git repository

### 3.5 Launch

```bash
# Enter project directory
cd /path/to/project

# Launch TUI
opencode

# Or specify directory
opencode /path/to/project
```bash

### 3.6 First-Time Setup

After launch, run `/init`. OpenCode will analyze project structure and create `AGENTS.md` file to help AI understand your codebase.

---

## 4. Interactive Mode (TUI + CLI)

### 4.1 TUI (Terminal User Interface)

```
┌─────────────────────────────────┐
│  OpenCode TUI                   │
│                                 │
│  💬 Message History             │
│  ─────────────────────          │
│  Q: Explain this function...    │
│  A: This function does...       │
│  ─────────────────────          │
│  > Enter new message...         │
│                                 │
│  [Build]  [Tab to switch]       │
└─────────────────────────────────┘
```bash

### 4.2 File References

Use `@` symbol to reference files:

```
How is authentication handled in @packages/functions/src/api/index.ts?
```bash

- Supports fuzzy search
- File content automatically loaded into conversation
- Supports alias references (@docs/README.md)

### 4.3 Bash Command Injection

Messages starting with `!` execute Shell commands directly:

```bash
!ls -la
!git status
!npm test
```bash

Command output automatically loaded into conversation.

### 4.4 Two Main Agent Modes

| Mode | Shortcut | Description |
|---|---|---|
| **Build** | Tab to switch | Default agent, full tool access |
| **Plan** | Tab to switch | Read-only agent, no edits or commands |

**Recommended workflow:**
1. Use **Plan** mode to analyze and plan
2. Use **Build** mode to execute and build

### 4.5 Image Support

Drag and drop images directly into terminal. OpenCode will scan images and add to prompt.

---

## 5. Common Commands

### 5.1 Built-in Slash Commands

| Command | Shortcut | Function |
|---|---|---|
| `/connect` | — | Add model provider and API key |
| `/init` | — | Initialize project, create AGENTS.md |
| `/new` | Ctrl+X N | Start new session |
| `/compact` | Ctrl+X C | Compress/summarize current session |
| `/undo` | Ctrl+X U | Undo last message (requires Git) |
| `/redo` | Ctrl+X R | Redo undone message |
| `/share` | — | Share current session (generate link) |
| `/help` | — | Show help |
| `/models` | Ctrl+X M | List available models |
| `/sessions` | Ctrl+X L | List/switch sessions |
| `/themes` | Ctrl+X T | List available themes |
| `/editor` | Ctrl+X E | Open external editor |
| `/export` | Ctrl+X X | Export session as Markdown |
| `/details` | — | Toggle tool execution details |
| `/thinking` | — | Toggle model reasoning visibility |
| `/quit` / `/q` | Ctrl+X Q | Exit OpenCode |

### 5.2 Conversation Mode Switch

- **Tab key**: Switch between Build and Plan agents
- **Bottom-right indicator**: Shows current agent mode

---

## 6. CLI Commands

### 6.1 Non-Interactive Mode

```bash
# Send message directly (without entering TUI)
opencode run "Explain the functionality of src/index.ts"

# Specify directory
opencode run "Help me refactor this code" /path/to/project
```bash

### 6.2 Server Mode

```bash
# Start HTTP server
opencode serve

# Start Web interface
opencode web
```bash

### 6.3 Custom Install Path

```bash
# Specify install directory
OPENCODE_INSTALL_DIR=/usr/local/bin curl -fsSL https://opencode.ai/install | bash

# XDG standard path
XDG_BIN_DIR=$HOME/.local/bin curl -fsSL https://opencode.ai/install | bash
```bash

---

## 7. Model Configuration

### 7.1 Connect Model Providers

```
/connect
```bash

Select provider → Enter API Key → Done.

### 7.2 Supported Model Providers

| Provider | Models | Use Case |
|---|---|---|
| **Anthropic** | Claude Sonnet 4.5, Opus 4.6, Haiku 4.5 | Code generation, reasoning |
| **OpenAI** | GPT-5.1 Codex, GPT-5 Codex | Programming-specific |
| **Google** | Gemini 3.1 Pro | Multimodal |
| **DeepSeek** | DeepSeek-V3 | Cost-effective |
| **Groq** | Various open-source models | High-speed inference |
| **Ollama** | Local models | Privacy-first |
| **LM Studio** | Local models | Privacy-first |

### 7.3 Model Configuration Format

```json
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
```bash

**small_model**: Used for lightweight tasks (title generation, etc.), automatically uses cheaper model.

### 7.4 OpenCode Zen (Recommended for Beginners)

- Officially tested and verified model list
- Pay-as-you-go
- Run `/connect` → Select `opencode` → Go to opencode.ai/auth

### 7.5 Different Models for Different Tasks

```json
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
```bash

---

## 8. Configuration Files

### 8.1 Configuration Priority (Low to High)

| Priority | Source | Description |
|---|---|---|
| 1 | Remote config | Organization defaults (.well-known/opencode) |
| 2 | Global config | `~/.config/opencode/opencode.json` |
| 3 | Custom config | `OPENCODE_CONFIG` environment variable |
| 4 | Project config | Project root `opencode.json` |
| 5 | .opencode directory | agents, commands, plugins |
| 6 | Inline config | `OPENCODE_CONFIG_CONTENT` environment variable |
| 7 | Managed config | Administrator configuration (highest priority) |

**Important**: Configuration files are merged, not replaced. Non-conflicting settings from different sources are preserved.

### 8.2 Global Configuration

```json
// ~/.config/opencode/opencode.json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-5",
  "autoupdate": true,
  "server": {
    "port": 4096
  }
}
```bash

### 8.3 Project Configuration

```json
// Project root opencode.json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-20250514",
  "permission": {
    "edit": "ask",
    "bash": "ask"
  }
}
```bash

✅ **Safe to commit to Git**

### 8.4 TUI Configuration

```json
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
```bash

### 8.5 Custom Configuration Path

```bash
export OPENCODE_CONFIG=/path/to/my/custom-config.json
opencode run "Hello world"
```bash

---

## 9. Custom Agents

### 9.1 Built-in Agents

| Agent | Type | Description |
|---|---|---|
| **Build** | Primary | Default, all tools available, full development permissions |
| **Plan** | Primary | Read-only, no edits or commands, perfect for analysis and planning |
| **General** | Subagent | General research, multi-step tasks, can execute in parallel |
| **Explore** | Subagent | Quick read-only codebase exploration, cannot modify files |
| **Scout** | Subagent | External documentation and dependency research, clones dependency repos to cache |

### 9.2 Hidden System Agents

| Agent | Function |
|---|---|
| **Compaction** | Automatically compresses long context |
| **Title** | Automatically generates session titles |
| **Summary** | Automatically creates session summaries |

### 9.3 Using Subagents

```bash
# Mention with @ in messages
@general help me search for this function
@explore find all authentication-related files
@scout check the source code implementation of this library
```bash

### 9.4 Subagent Session Navigation

| Action | Shortcut |
|---|---|
| Enter sub-session | `Ctrl+Down` (or `+Down`) |
| Switch to next sub-session | `Right` |
| Switch to previous sub-session | `Left` |
| Return to parent session | `Up` |

### 9.5 Custom Agents (JSON Configuration)

```json
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
```bash

### 9.6 Custom Agents (Markdown Configuration)

Create file `~/.config/opencode/agents/review.md`:

```markdown
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
```bash

Filename is agent name (review.md → `@review`)

### 9.7 Agent Configuration Options

| Option | Description |
|---|---|
| `description` | Agent description (required) |
| `mode` | `primary` or `subagent` |
| `model` | Override default model |
| `temperature` | 0.0-1.0 (0=deterministic, 1=creative) |
| `prompt` | Custom system prompt file path |
| `permission` | Permission control |
| `steps` | Maximum agent steps (control costs) |
| `disable` | `true` to disable agent |

---

## 10. Custom Commands

### 10.1 Create Commands (Markdown)

Create `.opencode/commands/test.md`:

```markdown
---
description: Run tests with coverage
agent: build
model: anthropic/claude-3-5-sonnet-20241022
---

Run the full test suite with coverage report and show any failures.
Focus on the failing tests and suggest fixes.
```bash

Use: `/test`

### 10.2 Create Commands (JSON)

```json
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
```bash

### 10.3 Command Arguments

Use `$ARGUMENTS` or `$1`, `$2`, `$3`:

```markdown
---
description: Create a new component
---

Create a new React component named $ARGUMENTS with TypeScript support.
Include proper typing and basic structure.
```text

Use: `/component Button` → `$ARGUMENTS` replaced with `Button`

```markdown
---
description: Create a new file with content
---

Create a file named $1 in the directory $2
with the following content: $3
```bash

Use: `/create-file config.json src '{ "key": "value" }'`

### 10.4 Command Embedded Shell Output

Use `!command` to inject Shell command output:

```markdown
---
description: Review recent changes
---

Recent git commits:

!`git log --oneline -10`

Review these changes and suggest any improvements.
```bash

### 10.5 Command Embedded Files

Use `@filename` to reference files:

```markdown
---
description: Review component
---

Review the component in @src/components/Button.tsx.
Check for performance issues and suggest improvements.
```bash

### 10.6 Command Options

| Option | Description |
|---|---|
| `template` | Prompt template (required) |
| `description` | Description shown in TUI |
| `agent` | Specify execution agent |
| `subtask` | `true` forces execution as subagent |
| `model` | Override default model |

---

## 11. Agent Skills

### 11.1 What Are Skills

Skills are reusable knowledge files (`SKILL.md`) containing specialized instructions and processes for specific domains.

### 11.2 Using Skills

```
skill tool automatically loads SKILL.md file content into conversation
```bash

### 11.3 Skills Storage Location

| Scope | Path |
|---|---|
| **Global** | `~/.config/opencode/skills/` |
| **Project** | `.opencode/skills/` |

### 11.4 Creating Skills

Each Skill is a directory containing `SKILL.md`:

```
.opencode/skills/
├── my-skill/
│   └── SKILL.md
└── another-skill/
    └── SKILL.md
```bash

---

## 12. Custom Tools

### 12.1 What Are Custom Tools

Custom tools allow you to define your own functions for LLM to call and execute arbitrary code.

### 12.2 Built-in Tools List

| Tool | Description |
|---|---|
| `bash` | Execute Shell commands |
| `edit` | Modify existing files |
| `write` | Create new files or overwrite existing files |
| `read` | Read file contents |
| `grep` | Regular expression search in file contents |
| `glob` | Pattern matching to find files |
| `lsp` | Language Server Protocol (go-to-definition, find references, etc.) |
| `apply_patch` | Apply patch files |
| `skill` | Load SKILL.md files |
| `todowrite` | Manage to-do lists |
| `webfetch` | Fetch web content |
| `websearch` | Search web using Exa AI |
| `question` | Ask user questions |

### 12.3 Underlying Technology

- `grep` and `glob` use **ripgrep** (rg)
- Default respects `.gitignore` rules
- Can use `.ignore` files to override ignore rules

---

## 13. Permission Control

### 13.1 Permission Levels

| Level | Description |
|---|---|
| `allow` | Allow all operations, no approval needed |
| `ask` | Requires user approval before execution |
| `deny` | Disable the tool |

### 13.2 Permission Keys

| Key | Controlled Tools |
|---|---|
| `read` | read |
| `edit` | write, edit, apply_patch |
| `glob` | glob |
| `grep` | grep |
| `list` | list |
| `bash` | bash |
| `task` | task |
| `external_directory` | Access files outside project directory |
| `todowrite` | writetodo, readtodo |
| `webfetch` | webfetch |
| `websearch` | websearch |
| `lsp` | lsp |
| `skill` | skill |
| `question` | question |
| `doom_loop` | Recovery prompt when agent is stuck |

### 13.3 Fine-Grained Control (Glob Patterns)

```json
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
```bash

### 13.4 Wildcard Control

```json
{
  "permission": {
    "mymcp_*": "ask"
  }
}
```bash

### 13.5 Independent Permissions Per Agent

```json
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
```bash

### 13.6 Markdown Agent Permissions

```markdown
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
```bash

---

## 14. MCP Servers

### 14.1 What Is MCP

MCP (Model Context Protocol) servers allow integration with external tools and services, including database access, API integration, third-party services, etc.

### 14.2 MCP Configuration

```json
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
```bash

### 14.3 Remote MCP

Organizations can provide default MCP servers via `.well-known/opencode` endpoint, users can enable as needed.

### 14.4 Use Cases

- Database queries
- Jira / GitHub integration
- Third-party API calls
- Internal tool integration

---

## 15. Plugin System

OpenCode supports plugin extensions. Plugins stored in `.opencode/` directory:

```
.opencode/
├── agents/     # Custom agents
├── commands/   # Custom commands
├── skills/     # Custom skills
└── plugins/    # Plugins
```bash

---

## 16. GitHub and GitLab Integration

### 16.1 Git Safety Net

- **Automatic snapshots**: Creates Git snapshot before each change
- **/undo**: Undo all changes from last message
- **/redo**: Redo undone changes
- **/share**: Generate session share link

### 16.2 Session Sharing

```
/share
```bash

Creates share link for current conversation, copies to clipboard.

Example: https://opencode.ai/s/4XP1fce5

### 16.3 Git Worktrees

OpenCode supports Git Worktrees as isolation strategy, letting AI work on independent branches without affecting main branch.

---

## 17. Important Notes

### 17.1 ⚠️ Critical Reminders

| Item | Description |
|---|---|
| **Version cleanup** | Remove versions before 0.1.x before installing |
| **Git dependency** | /undo and /redo require project to be Git repository |
| **API Key security** | Keep API keys safe, don't commit to public repositories |
| **Model selection** | Choose appropriate model based on task complexity, balance cost and quality |
| **Permission settings** | Production environment recommends `bash: "ask"` to prevent accidental command execution |
| **Context management** | Use `/compact` to compress context for long conversations to save tokens |
| **Naming conflicts** | If creating OpenCode-related projects, name must include note that it's unofficial |

### 17.2 Cost Optimization Suggestions

| Strategy | Description |
|---|---|
| **Tiered models** | Use cheap models (Haiku) for simple tasks, expensive models (Sonnet/Opus) for complex tasks |
| **small_model** | Configure small_model for lightweight tasks like title generation |
| **temperature** | Lower temperature to reduce unnecessary diverse output |
| **steps limit** | Set maximum steps to control costs |
| **Local models** | Use Ollama for non-critical tasks, completely free |

---

## 18. Comparison with Claude Code / Codex

| Feature | OpenCode | Claude Code | Codex |
|---|---|---|---|
| **Model Support** | 75+ providers | Claude only | OpenAI only |
| **License** | MIT (open-source) | Proprietary | Proprietary |
| **Local Models** | Yes (Ollama) | No | No |
| **Cost** | Free (BYO API key) | Subscription | Subscription |
| **Vendor Lock-In** | None | Full | Full |
| **LSP Support** | Yes | No | No |
| **Custom Agents** | Yes | Limited | Limited |
| **Custom Commands** | Yes | No | No |
| **MCP Integration** | Yes | No | No |
| **Git Integration** | /undo, /redo, snapshots | Basic | Basic |

---

## 19. Recommended Workflow

1. **Start with Plan mode** - Analyze codebase, understand structure
2. **Use @file references** - Point AI to specific files
3. **Leverage subagents** - Use @explore for codebase exploration, @general for research
4. **Create custom commands** - Automate repetitive tasks
5. **Set appropriate permissions** - Balance safety and efficiency
6. **Use /compact regularly** - Prevent context overflow in long sessions
7. **Commit frequently** - Use /undo when needed to revert changes

---

## 20. Quick Reference Cheat Sheet

```bash
# Installation
curl -fsSL https://opencode.ai/install | bash

# Launch
cd /path/to/project && opencode

# First-time setup
/init

# Connect provider
/connect

# Essential commands
/new          # New session
/compact      # Compress context
/undo         # Undo last change
/models       # List models
/share        # Share session

# Agent modes (Tab to switch)
Build         # Full access
Plan          # Read-only

# File references
@filename     # Reference file

# Bash commands
!command      # Execute shell command

# Subagents
@explore      # Codebase exploration
@general      # General research
@scout        # External docs research
```bash

---

*Have you tried OpenCode yet? What model provider do you prefer for AI coding?*

---

## 中文版



> 基於影片「不得不裝的 AI 代理工具｜GitHub 萬星項目｜OPENCODE」（Rick Hau, 2026-06-05）影片連結：https://www.youtube.com/watch?v=__bcJHoTE08分析時間：2026-06-09版本：OpenCode v0.1.x+（GitHub 17 萬+ Stars）

---

## 📑 目錄
- OpenCode 是什麼- 為什麼「不得不裝」- 安裝與啟動- 交互模式（TUI + CLI）- 常用命令- CLI 命令- 模型配置- 配置文件- 自定義智能體（Agents）- 自定義命令（Custom Commands）- Agent Skills- Custom Tools- 權限控制（Permissions）- MCP 服務器- 插件系統- GitHub 與 GitLab 集成- 注意事項- 與 Claude Code / Codex 比較- 推薦工作流程- 快速參考速查表
---

## 1. OpenCode 是什麼

OpenCode 是一個開源的 AI 編程代理工具（AI Coding Agent），可以與 Claude Code、Codex 等並駕齊驅。

| 項目 | 詳情 ||---|---|| GitHub | https://github.com/anomalyco/opencode || 官網 | https://opencode.ai || 文檔 | https://opencode.ai/docs || Stars | 17 萬+ || 授權 | MIT（完全開源） || 開發語言 | Go（TUI，基於 Bubble Tea）+ JavaScript/Bun（HTTP 服務器，基於 Hono） || 架構 | Client-Server（支持終端 TUI、桌面應用、IDE 擴展、HTTP 客戶端） || 核心賣點 | 模型無關（Model-Agnostic）：支持 75+ 模型提供商，零供應商鎖定 |

### 與其他工具的本質區別

| 工具 | 核心策略 ||---|---|| Claude Code | 單一模型家族（Claude），深度優化，追求速度 || Codex | OpenAI 生態系（ChatGPT 綁定），追求集成深度 || OpenCode | 模型無關 + 開源 + 零鎖定，追求靈活性與隱私 |

---

## 2. 為什麼「不得不裝」

### 2.1 核心優勢

| 優勢 | 說明 ||---|---|| 🆓完全免費 | MIT 授權，自己帶 API Key 即可，工具本身零費用 || 🌐75+ 模型提供商 | Anthropic、OpenAI、Google、DeepSeek、Groq、Ollama（本地模型） || 🔒零供應商鎖定 | 隨時切換模型，不怕任何一家漲價或降質 || 💻LSP 集成 | 業界唯一支持 Language Server Protocol 的 AI 編程代理 || 🤖多代理系統 | Build、Plan、General、Explore、Scout 五大內建代理 || 🔧高度可定制 | Agents、Commands、Skills、Tools、MCP 全部可自定義 || 📱多表面支持 | TUI（終端）、Desktop App、VS Code 擴展、HTTP API || 🛡️安全控制 | Git 快照 + /undo + /redo + 精細權限控制 || 🏠完全本地隱私 | 支持 Ollama 本地模型，代碼不上傳 || 🌍20+ 語言 | 包含繁體中文（README.zht.md） |

### 2.2 適合誰用
- ✅不想被鎖定的開發者：不想只綁定 Anthropic 或 OpenAI- ✅成本敏感者：可以用便宜的模型做簡單任務，貴的模型做複雜任務- ✅隱私要求高：代碼不能上傳到雲端 → 用 Ollama 本地模型- ✅多語言項目：需要不同模型處理不同語言- ✅企業用戶：需要精細權限控制 + MCP 集成
---

## 3. 安裝與啟動

### 3.1 一鍵安裝（推薦）

```
curl -fsSL https://opencode.ai/install | bash

```bash

### 3.2 各平台安裝方法

| 平台 | 命令 ||---|---|| npm | npm install -g opencode-ai || Bun | bun install -g opencode-ai || pnpm | pnpm install -g opencode-ai || Yarn | yarn global add opencode-ai || Homebrew（macOS/Linux） | brew install anomalyco/tap/opencode || Arch Linux（穩定） | sudo pacman -S opencode || Arch Linux（最新） | paru -S opencode-bin || Windows（Chocolatey） | choco install opencode || Windows（Scoop） | scoop install opencode || Mise（任意 OS） | mise use -g github:anomalyco/opencode || Docker | docker run -it --rm ghcr.io/anomalyco/opencode || Nix | nix run nixpkgs#opencode |

### 3.3 桌面應用

| 平台 | 下載 ||---|---|| macOS（Apple Silicon） | opencode-desktop-mac-arm64.dmg || macOS（Intel） | opencode-desktop-mac-x64.dmg || Windows | opencode-desktop-windows-x64.exe || Linux | .deb / .rpm / .AppImage |

macOS 安裝：brew install --cask opencode-desktopWindows 安裝：scoop bucket add extras; scoop install extras/opencode-desktop

### 3.4 前置要求
- 現代終端模擬器：WezTerm、Alacritty、Ghostty、Kitty（任一）- LLM API Key：至少一個模型提供商的 API Key- Git：/undo 和 /redo 功能需要 Git 倉庫

### 3.5 啟動

```
# 進入項目目錄
cd /path/to/project

# 啟動 TUI
opencode

# 或指定目錄
opencode /path/to/project

```bash

### 3.6 首次配置

啟動後運行/init，OpenCode 會分析項目結構並創建AGENTS.md文件，幫助 AI 理解你的代碼庫。

---

## 4. 交互模式（TUI + CLI）

### 4.1 TUI（終端用戶界面）

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

```bash

### 4.2 文件引用

使用@符號引用文件：

```
How is authentication handled in @packages/functions/src/api/index.ts?

```bash
- 支持模糊搜索- 文件內容自動載入對話- 支持別名引用（@docs/README.md）

### 4.3 Bash 命令注入

消息以!開頭直接執行 Shell 命令：

```
!ls -la
!git status
!npm test

```bash

命令輸出自動載入對話。

### 4.4 兩種主要代理模式

| 模式 | 快捷鍵 | 說明 ||---|---|---|| Build | Tab 切換 | 默認代理，完整工具訪問權限 || Plan | Tab 切換 | 只讀代理，禁止編輯和執行命令 |

工作流程建議：1. 用Plan模式分析和規劃2. 用Build模式執行和構建

### 4.5 圖像支持

直接拖放圖片到終端，OpenCode 會掃描圖片並加入提示詞。

---

## 5. 常用命令

### 5.1 內建斜線命令

| 命令 | 快捷鍵 | 功能 ||---|---|---|| /connect | — | 添加模型提供商和 API Key || /init | — | 初始化項目，創建 AGENTS.md || /new | Ctrl+X N | 開始新會話 || /compact | Ctrl+X C | 壓縮/總結當前會話 || /undo | Ctrl+X U | 撤銷上次消息（需要 Git） || /redo | Ctrl+X R | 重做已撤銷的消息 || /share | — | 分享當前會話（生成鏈接） || /help | — | 顯示幫助 || /models | Ctrl+X M | 列出可用模型 || /sessions | Ctrl+X L | 列出/切換會話 || /themes | Ctrl+X T | 列出可用主題 || /editor | Ctrl+X E | 打開外部編輯器 || /export | Ctrl+X X | 導出會話為 Markdown || /details | — | 切換工具執行詳情顯示 || /thinking | — | 切換模型推理過程可見性 || /quit//q | Ctrl+X Q | 退出 OpenCode |

### 5.2 對話模式切換
- Tab 鍵：在 Build 和 Plan 代理間切換- 右下角指示器：顯示當前代理模式
---

## 6. CLI 命令

### 6.1 非交互模式

```
# 直接發送消息（不進入 TUI）
opencode run "幫我解釋 src/index.ts 的功能"

# 指定目錄
opencode run "幫我重構這段代碼" /path/to/project

```bash

### 6.2 服務器模式

```
# 啟動 HTTP 服務器
opencode serve

# 啟動 Web 界面
opencode web

```bash

### 6.3 自定義安裝路徑

```
# 指定安裝目錄
OPENCODE_INSTALL_DIR=/usr/local/bin curl -fsSL https://opencode.ai/install | bash

# XDG 標準路徑
XDG_BIN_DIR=$HOME/.local/bin curl -fsSL https://opencode.ai/install | bash

```bash

---

## 7. 模型配置

### 7.1 連接模型提供商

```
/connect

```bash

選擇提供商 → 輸入 API Key → 完成。

### 7.2 支持的主要模型提供商

| 提供商 | 模型 | 用途 ||---|---|---|| Anthropic | Claude Sonnet 4.5, Opus 4.6, Haiku 4.5 | 代碼生成、推理 || OpenAI | GPT-5.1 Codex, GPT-5 Codex | 編程專用 || Google | Gemini 3.1 Pro | 多模態 || DeepSeek | DeepSeek-V3 | 性價比高 || Groq | 各種開源模型 | 高速推理 || Ollama | 本地模型 | 隱私優先 || LM Studio | 本地模型 | 隱私優先 |

### 7.3 模型配置格式

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

```bash

small_model：用於輕量任務（標題生成等），自動使用更便宜的模型。

### 7.4 OpenCode Zen（推薦新手）
- 官方測試驗證的模型列表- 按需付費- 運行/connect→ 選擇opencode→ 前往 opencode.ai/auth

### 7.5 不同任務用不同模型

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

```bash

---

## 8. 配置文件

### 8.1 配置優先級（從低到高）

| 優先級 | 來源 | 說明 ||---|---|---|| 1 | Remote config | 組織默認值（.well-known/opencode） || 2 | Global config | ~/.config/opencode/opencode.json || 3 | Custom config | OPENCODE_CONFIG環境變數 || 4 | Project config | 項目根目錄opencode.json || 5 | .opencode 目錄 | agents、commands、plugins || 6 | Inline config | OPENCODE_CONFIG_CONTENT環境變數 || 7 | Managed config | 管理員配置（最高優先級） |

重要：配置文件是合併的，不是替換。不同來源的非衝突設置都會保留。

### 8.2 全局配置

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

```bash

### 8.3 項目配置

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

```bash

✅可以安全提交到 Git

### 8.4 TUI 配置

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

```bash

### 8.5 自定義配置路徑

```
export OPENCODE_CONFIG=/path/to/my/custom-config.json
opencode run "Hello world"

```bash

---

## 9. 自定義智能體（Agents）

### 9.1 內建代理

| 代理 | 類型 | 說明 ||---|---|---|| Build | 主代理 | 默認，所有工具可用，完整開發權限 || Plan | 主代理 | 只讀，禁止編輯和執行命令，適合分析和規劃 || General | 子代理 | 通用研究，多步任務，可並行執行 || Explore | 子代理 | 快速只讀代碼庫探索，不可修改文件 || Scout | 子代理 | 外部文檔和依賴研究，克隆依賴倉庫到緩存 |

### 9.2 隱藏系統代理

| 代理 | 功能 ||---|---|| Compaction | 自動壓縮長上下文 || Title | 自動生成會話標題 || Summary | 自動創建會話摘要 |

### 9.3 使用子代理

```
# 在消息中 @ 提及
@general help me search for this function
@explore 幫我找出所有與認證相關的文件
@scout 幫我查看這個庫的源代碼實現

```bash

### 9.4 子代理會話導航

| 操作 | 快捷鍵 ||---|---|| 進入子會話 | Ctrl+Down（或+Down） || 切換到下一個子會話 | Right || 切換到上一個子會話 | Left || 返回父會話 | Up |

### 9.5 自定義代理（JSON 配置）

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

```bash

### 9.6 自定義代理（Markdown 配置）

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

```bash

文件名即代理名稱（review.md →@review）

### 9.7 代理配置選項

| 選項 | 說明 ||---|---|| description | 代理描述（必填） || mode | primary或subagent || model | 覆蓋默認模型 || temperature | 0.0-1.0（0=確定性，1=創造性） || prompt | 自定義系統提示詞文件路徑 || permission | 權限控制 || steps | 最大代理步驟數（控制成本） || disable | true禁用代理 |

---

## 10. 自定義命令（Custom Commands）

### 10.1 創建命令（Markdown）

創建.opencode/commands/test.md：

```
---
description: Run tests with coverage
agent: build
model: anthropic/claude-3-5-sonnet-20241022
---

Run the full test suite with coverage report and show any failures.
Focus on the failing tests and suggest fixes.

```bash

使用：/test

### 10.2 創建命令（JSON）

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

```bash

### 10.3 命令參數

使用$ARGUMENTS或$1,$2,$3：

```
---
description: Create a new component
---

Create a new React component named $ARGUMENTS with TypeScript support.
Include proper typing and basic structure.

```text

使用：/component Button→$ARGUMENTS替換為Button

```
---
description: Create a new file with content
---

Create a file named $1 in the directory $2
with the following content: $3

```bash

使用：/create-file config.json src '{ "key": "value" }'

### 10.4 命令嵌入 Shell 輸出

使用!command注入 Shell 命令輸出：

```
---
description: Review recent changes
---

Recent git commits:

!`git log --oneline -10`

Review these changes and suggest any improvements.

```bash

### 10.5 命令嵌入文件

使用@filename引用文件：

```
---
description: Review component
---

Review the component in @src/components/Button.tsx.
Check for performance issues and suggest improvements.

```bash

### 10.6 命令選項

| 選項 | 說明 ||---|---|| template | 提示詞模板（必填） || description | TUI 中顯示的描述 || agent | 指定執行代理 || subtask | true強制作為子代理執行 || model | 覆蓋默認模型 |

---

## 11. Agent Skills

### 11.1 什麼是 Skills

Skills 是可重用的知識文件（SKILL.md），包含特定領域的專業指令和流程。

### 11.2 使用 Skills

```
skill tool 自動加載 SKILL.md 文件內容到對話中

```bash

### 11.3 Skills 存放位置

| 範圍 | 路徑 ||---|---|| 全局 | ~/.config/opencode/skills/ || 項目 | .opencode/skills/ |

### 11.4 創建 Skill

每個 Skill 是一個目錄，包含SKILL.md：

```
.opencode/skills/
├── my-skill/
│   └── SKILL.md
└── another-skill/
    └── SKILL.md

```bash

---

## 12. Custom Tools

### 12.1 什麼是 Custom Tools

自定義工具允許你定義自己的函數，讓 LLM 調用執行任意代碼。

### 12.2 內建工具列表

| 工具 | 說明 ||---|---|| bash | 執行 Shell 命令 || edit | 修改現有文件 || write | 創建新文件或覆蓋現有文件 || read | 讀取文件內容 || grep | 正則表達式搜索文件內容 || glob | 模式匹配查找文件 || lsp | 語言服務器協議（定義跳轉、引用查找等） || apply_patch | 應用補丁文件 || skill | 加載 SKILL.md 文件 || todowrite | 管理待辦列表 || webfetch | 獲取網頁內容 || websearch | 使用 Exa AI 搜索網路 || question | 向用戶提問 |

### 12.3 底層技術
- grep和glob使用ripgrep（rg）- 默認遵守.gitignore規則- 可使用.ignore文件覆蓋忽略規則
---

## 13. 權限控制（Permissions）

### 13.1 權限級別

| 級別 | 說明 ||---|---|| allow | 允許所有操作，無需批准 || ask | 執行前需要用戶批准 || deny | 禁用該工具 |

### 13.2 權限鍵

| 鍵 | 控制的工具 ||---|---|| read | read || edit | write, edit, apply_patch || glob | glob || grep | grep || list | list || bash | bash || task | task || external_directory | 訪問項目目錄外的文件 || todowrite | writetodo, readtodo || webfetch | webfetch || websearch | websearch || lsp | lsp || skill | skill || question | question || doom_loop | 代理卡住時的恢復提示 |

### 13.3 精細控制（Glob 模式）

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

```bash

### 13.4 通配符控制

```
{
  "permission": {
    "mymcp_*": "ask"
  }
}

```bash

### 13.5 每個代理獨立權限

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

```bash

### 13.6 Markdown 代理權限

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

```bash

---

## 14. MCP 服務器

### 14.1 什麼是 MCP

MCP（Model Context Protocol）服務器允許集成外部工具和服務，包括數據庫訪問、API 集成、第三方服務等。

### 14.2 MCP 配置

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

```bash

### 14.3 遠程 MCP

組織可以通過.well-known/opencode端點提供默認 MCP 服務器，用戶可以按需啟用。

### 14.4 使用場景
- 數據庫查詢- Jira / GitHub 集成- 第三方 API 調用- 內部工具集成
---

## 15. 插件系統

OpenCode 支持插件擴展功能。插件存放在.opencode/目錄下：

```
.opencode/
├── agents/     # 自定義代理
├── commands/   # 自定義命令
├── skills/     # 自定義技能
└── plugins/    # 插件

```bash

---

## 16. GitHub 與 GitLab 集成

### 16.1 Git 安全網
- 自動快照：每次更改前自動創建 Git 快照- /undo：撤銷上次消息的所有更改- /redo：重做已撤銷的更改- /share：生成會話分享鏈接

### 16.2 會話分享

```
/share

```bash

創建當前對話的分享鏈接，複製到剪貼板。

示例：https://opencode.ai/s/4XP1fce5

### 16.3 Git Worktrees

OpenCode 支持 Git Worktrees 作為隔離策略，讓 AI 在獨立分支工作而不影響主分支。

---

## 17. 注意事項

### 17.1 ⚠️ 重要提醒

| 項目 | 說明 ||---|---|| 版本清理 | 安裝前移除 0.1.x 之前的舊版本 || Git 依賴 | /undo 和 /redo 需要項目是 Git 倉庫 || API Key 安全 | 妥善保管 API Key，不要提交到公共倉庫 || 模型選擇 | 根據任務複雜度選擇合適的模型，平衡成本和質量 || 權限設置 | 生產環境建議設置bash: "ask"防止意外命令執行 || 上下文管理 | 長對話使用/compact壓縮上下文以節省 token || 命名衝突 | 如果創建與 OpenCode 相關的項目，名稱中必須包含註釋說明非官方 |

### 17.2 成本優化建議

| 策略 | 說明 ||---|---|| 分層模型 | 簡單任務用便宜模型（Haiku），複雜任務用貴模型（Sonnet/Opus） || small_model | 配置 small_model 用於標題生成等輕量任務 || temperature | 降低 temperature 減少不必要的多樣性輸出 || steps 限制 | 設置最大步驟數控制成本 || 本地模型 | 使用 Ollama 完全免費 |

### 17.3 安全最佳實踐

| 實踐 | 配置 ||---|---|| 禁止危險命令 | "bash": { "rm -rf *": "deny" } || 編輯需批准 | "edit": "ask" || 外部目錄禁止 | "external_directory": "deny" || Web 訪問控制 | "webfetch": "deny"或"ask" |

---

## 18. 與 Claude Code / Codex 比較

### 18.1 架構對比

| 維度 | Claude Code | Codex | OpenCode ||---|---|---|---|| 運行時 | 本地終端 / 雲端 VM | 本地 CLI/應用/IDE（+ 雲端） | 本地 Client-Server || 語言 | TypeScript/Node.js | Rust（CLI） | Go（TUI）+ Bun/JS（服務器） || 模型鎖定 | 僅 Claude | 僅 GPT-Codex | 75+ 提供商 + 本地 || LSP 集成 | ❌ | ❌ | ✅（獨有功能） || 代理協調 | Teams 2-16 個代理 | 多任務並行 | 子代理 || 開源 | ❌ | 部分（CLI Apache 2.0） | ✅ MIT |

### 18.2 基準測試

| 測試 | Claude Code | Codex | OpenCode ||---|---|---|---|| SWE-bench Pro | Opus 4.6: 57.5% | GPT-5-Codex: 57.0% | 取決於使用的模型 || Terminal-Bench 2.0 | Sonnet 4.6: 53.0% | GPT-5 Codex: 53.0% | 取決於使用的模型 || 速度（相同模型） | 基準 | 相近 | 慢 78%，但更徹底 |

### 18.3 價格對比

| 工具 | 最便宜 | 中等 | 高端 ||---|---|---|---|| OpenCode | $0（Ollama 本地） | API 費用 | $200/月（Black） || Claude Code | $20/月（Pro） | $100/月（Max 5x） | $200/月（Max 20x） || Codex | $0（免費/Go） | $20/月（Plus） | $200/月（Pro） |

### 18.4 選擇建議

| 需求 | 推薦 ||---|---|| 模型靈活性 + 零鎖定 | OpenCode || 本地隱私 | OpenCode + Ollama || 開源代碼可 fork | OpenCode || LSP 智能代碼分析 | OpenCode || 最快上手 + 最簡單 | Codex（ChatGPT 自帶） || 最高代碼質量 | Claude Code || GitHub PR 自動創建 | Codex |

---

## 19. 推薦工作流程

### 19.1 日常開發流程

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

```bash

### 19.2 代碼審查流程

```
1. @explore 幫我找出所有與認證相關的文件    # 快速探索
2. @code-reviewer 檢查這段代碼的質量        # 使用自定義代理
3. @general 幫我搜索這個庫的最佳實踐        # 外部研究

```bash

### 19.3 成本優化配置

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

```bash

---

## 20. 快速參考速查表

### 20.1 安裝

```
curl -fsSL https://opencode.ai/install | bash

```bash

### 20.2 啟動

```
cd /path/to/project && opencode

```bash

### 20.3 常用快捷鍵

| 快捷鍵 | 功能 ||---|---|| Tab | 切換代理（Build ↔ Plan） || Ctrl+X | 前導鍵（Leader Key） || Ctrl+X N | 新會話 || Ctrl+X U | 撤銷 || Ctrl+X R | 重做 || Ctrl+X C | 壓縮會話 || Ctrl+X M | 列出模型 || Ctrl+X T | 列出主題 || Ctrl+X L | 列出會話 || Ctrl+X P | 命令面板 |

### 20.4 常用命令

| 命令 | 說明 ||---|---|| /connect | 添加 API Key || /init | 初始化項目 || /new | 新會話 || /undo | 撤銷 || /redo | 重做 || /compact | 壓縮上下文 || /share | 分享會話 |

### 20.5 文件引用格式

```
@文件路徑        → 引用文件
!shell 命令      → 執行 Shell 命令
$ARGUMENTS       → 命令參數
$1, $2, $3       → 定位參數

```

### 20.6 關鍵資源

| 資源 | 連結 ||---|---|| GitHub | https://github.com/anomalyco/opencode || 官網 | https://opencode.ai || 文檔 | https://opencode.ai/docs || Discord | https://discord.gg/opencode || X.com | https://x.com/opencode || 安裝指南 | https://opencode.ai/docs#install || 配置指南 | https://opencode.ai/docs/config || 代理文檔 | https://opencode.ai/docs/agents || 命令文檔 | https://opencode.ai/docs/commands || 工具文檔 | https://opencode.ai/docs/tools || TUI 文檔 | https://opencode.ai/docs/tui || 影片教程 | https://www.youtube.com/watch?v=__bcJHoTE08 |

---

本文檔基於 Rick Hau 的 OpenCode 教學影片（2026-06-05）及官方文檔編制。分析時間：2026-06-09 08:52 HK版本：v1.0
