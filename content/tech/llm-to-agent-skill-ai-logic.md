---
title: "From LLM to Agent Skill: Understanding AI's Underlying Logic"
date: 2026-06-13T08:20:00+08:00
draft: false
tags:
  - tech
  - ai
  - llm
  - agent-framework
summary: "AI concepts from LLM to Agent explained."
description: "A comprehensive guide to AI core concepts: LLM, Token, Context, Prompt, Tool, MCP, Agent, and Agent Skill, based on a viral 1.1M-view video analysis."
---

## The Concept Chain

AI has a logical structure that builds from simple to complex. Understanding this chain — LLM → Token → Context → Prompt → Tool → MCP → Agent → Agent Skill — reveals how modern AI systems actually work.

A Bilibili creator (Mark's Tech Workshop) published a 32-minute video explaining these concepts from a bottom-up engineering perspective. It has since garnered 1.13 million views and 54,000 likes, making it one of the most-watched AI tutorials in Chinese.

## 1. LLM: The Foundation

**What is LLM?**

Large Language Model — a Transformer-based architecture (2017, Google) that works as a "text continuation game." The core principle: predict the next most probable word based on probability, outputting one token at a time.

| Component | Description |
|---|---|
| Architecture | Decoder-only Transformer |
| Key Paper | "Attention Is All You Need" (2017) |
| Breakthrough | GPT-3.5 (2022), GPT-4 (2023) |
| Current Leaders | GPT-5.4, Claude, Gemini |

**Key Insight:** "LLMs are essentially a text continuation game" — they predict the next most probable word in a continuous loop until completion.

## 2. Token: The Basic Unit

A token is the smallest text unit processed by LLM. Importantly, tokens are not equal to words.

| Example | Tokens | Token IDs | Notes |
|---|---|---|---|
| 马克的视频怎么样 | 4 tokens | [马克, 的, 视频, 怎么样] | Chinese characters |
| Transformer | 1 token | [Transformer] | English word |

**Tokenization Process:**
- **Encoding:** Text → Tokens → Token IDs
- **Decoding:** Token IDs → Tokens → Text
- **Algorithms:** BPE, WordPiece, SentencePiece

**Cost factor:** Input tokens are typically more expensive than output tokens.

## 3. Context & Context Window

**Context** is the conversation history and memory that maintains continuity. The **Context Window** is the maximum tokens a model can process.

| Aspect | Details |
|---|---|
| Range | 8K to 2M tokens (2026) |
| Challenge | Quadratic attention scaling |
| Solutions | FlashAttention, KV caching |
| Trade-off | Cost vs. performance |

**Warning:** Even with large context windows, models suffer from "lost in the middle" phenomenon — they pay less attention to information in the middle of long contexts.

## 4. Prompt Engineering

| Type | Purpose | Example |
|---|---|---|
| User Prompt | Dynamic, task-specific instructions | "Explain quantum computing" |
| System Prompt | Model behavior constraints | Defines agent capabilities and limits |

**Advanced Technique: Chain-of-Thought**

Chain-of-thought prompting significantly improves model performance by breaking complex problems into step-by-step reasoning. Particularly effective for mathematical, logical, and planning tasks.

## 5. Tools & Function Calling

Tools are external capabilities that extend LLM beyond text generation — APIs, databases, computation engines.

**Function Calling Process:**
1. User query received
2. Context assembly (tools + messages)
3. LLM decides if tool needed
4. Tool execution by developer code
5. Results returned to LLM
6. Final response generated

**Tool Definition Structure:**

| Field | Purpose | Example |
|---|---|---|
| Name | Clear identifier | search_web |
| Description | When to use tool | Search the internet for current information |
| Parameters | Inputs with types | query: string, max_results: integer |

## 6. MCP: Model Context Protocol

MCP is an open standard for connecting AI applications to external systems — think of it as "USB-C for AI applications." Created by Anthropic, open-sourced November 2024.

**MCP Architecture:**
- **Protocol:** JSON-RPC 2.0 based
- **Components:** Host, Client, Server
- **Host:** Container/coordinator
- **Client:** 1:1 server connections
- **Server:** Provides capabilities

**MCP Capabilities:**
- Resources: Data access (files, databases)
- Tools: Functions to execute
- Prompts: Reusable templates
- Sampling: LLM inference requests
- Roots: Filesystem boundaries

**MCP Adoption (2026):**
- SDK Downloads: 97M+/month (from 100K in 1 year)
- Public Servers: 13,230+
- Company Servers: Grew 232% in 6 months
- Supported Tools: Claude, ChatGPT, VS Code, Cursor, 30+ more

## 7. AI Agent: Autonomous Systems

**AI Agent = LLM + Planning + Memory + Tools**

AI Agents are not just LLMs — they are complete systems that use LLMs as reasoning engines with supporting components to reason, plan, and execute autonomously.

**Agent Components:**
- LLM Brain: Cognitive core for reasoning
- Planning Logic: Problem decomposition
- Memory: Short-term (context) + Long-term (vector DB)
- Tools: External capabilities
- Observation: Environment perception

**Agent Loop (See-Think-Act-Learn):**
1. **See:** Observe environment (emails, sensors, web)
2. **Think:** Decide actions using LLM reasoning
3. **Act:** Execute actions (send emails, update CRMs)
4. **Learn:** Log outcomes for improvement
5. **Repeat:** Continuous operation cycle

**Agent Types:**

| Type | Complexity | Example |
|---|---|---|
| Simple Reflex | Low | Condition-action rules |
| Model-Based | Medium | Internal environment model |
| Goal-Based | Medium-High | Outcome-focused planning |
| Utility-Based | High | Optimization with utility functions |
| Learning | Very High | Improves over time |

## 8. Agent Skill: Extensible Capabilities

Agent Skills are lightweight, open format for extending agent capabilities. Created by Anthropic, now an open standard supported by 30+ AI tools.

**Skill Structure:**
- SKILL.md: Required metadata + instructions
- scripts/: Optional executable code
- references/: Optional documentation
- assets/: Optional templates, resources

**Progressive Disclosure Pattern:**
- Discovery (~100 tokens): Load name and description at startup
- Activation (< 5000 tokens): Load full SKILL.md when task matches
- Execution (as needed): Load additional resources

**Security Warning:** Security researchers have found 341 malicious agent skills. Always verify skill sources before installation.

## Real-World Applications

| Industry | Use Cases | Examples |
|---|---|---|
| Customer Support | 26.5% of deployments | Tier-1 bots, sentiment analysis |
| Research | 24.4% of deployments | Data analysis, literature review |
| Internal Automation | 18% of deployments | Workflow automation, scheduling |
| Education | Growing sector | Adaptive tutors, virtual TAs |
| Finance | High-value applications | Fraud detection, compliance |
| Healthcare | Regulated sector | Pre-visit triage, medical scribing |

## Framework Comparison

| Framework | Focus | Best For |
|---|---|---|
| LangChain | Modular, flexible | General-purpose agents |
| CrewAI | Multi-agent collaboration | Complex workflows |
| AutoGen | Conversational agents | Enterprise applications |
| LlamaIndex | Data-centric | Knowledge-intensive tasks |
| OpenAI Agents SDK | Direct API integration | OpenAI-focused projects |

## Key Takeaways

1. **AI Agents ≠ LLMs** — Agents are complete systems with planning, memory, and tools
2. **MCP is the Standard** — Open protocol connecting AI to external systems
3. **Tools are Essential** — Function calling transforms LLMs into interactive agents
4. **Skills Enable Extensibility** — Lightweight format for adding capabilities
5. **Progressive Disclosure** — Load skills on-demand to optimize token usage
6. **Security Matters** — Verify skill sources to prevent malicious code execution
7. **Enterprise Adoption Growing** — 57% of organizations have agents in production

---

*What AI concept surprised you the most when you first understood it?*

---

## 從 LLM 到 Agent Skill：理解 AI 底層邏輯

## 概念鏈

AI 有一個從簡單到複雜的邏輯結構。理解這條鏈——LLM → Token → Context → Prompt → Tool → MCP → Agent → Agent Skill——揭示了現代 AI 系統實際如何運作。

一位B站創作者（馬克的技術工作坊）發布了一支32分鐘影片，從底層工程角度解釋這些概念。至今已獲得113萬次觀看和5.4萬個讚，成為中文最受歡迎的AI教程之一。

## 1. LLM：基礎

**什麼是 LLM？**

大語言模型——基於 Transformer 架構（2017年，Google），作為「文字接龍遊戲」運作。核心原理：根據機率預測下一個最可能的詞，一次輸出一個 Token。

| 組件 | 描述 |
|---|---|
| 架構 | 解碼器專用 Transformer |
| 關鍵論文 | 「Attention Is All You Need」（2017年） |
| 突破 | GPT-3.5（2022年）、GPT-4（2023年） |
| 目前領先 | GPT-5.4、Claude、Gemini |

**關鍵洞察：** 「大模型本質上就是一個文字接龍遊戲」——它們在持續循環中預測下一個最可能的詞，直到完成。

## 2. Token：基本單位

Token 是 LLM 處理的最小文字單位。重要的是，Token 不等於詞。

| 範例 | Token 數 | Token ID | 備註 |
|---|---|---|---|
| 馬克的視頻怎麼樣 | 4 個 Token | [馬克, 的, 視頻, 怎麼樣] | 中文字符 |
| Transformer | 1 個 Token | [Transformer] | 英文詞 |

**標記化過程：**
- **編碼：** 文字 → Token → Token ID
- **解碼：** Token ID → Token → 文字
- **演算法：** BPE、WordPiece、SentencePiece

**成本因素：** 輸入 Token 通常比輸出 Token 昂貴。

## 3. Context 與 Context Window

**Context** 是維持對話連續性的對話歷史和記憶。**Context Window** 是模型可處理的最大 Token 數。

| 方面 | 詳情 |
|---|---|
| 範圍 | 8K 到 2M Token（2026年） |
| 挑戰 | 二次注意力縮放 |
| 解決方案 | FlashAttention、KV 快取 |
| 權衡 | 成本 vs 效能 |

**警告：** 即使有大型 Context Window，模型仍面臨「中間遺失」現象——它們對長上下文中間部分的關注較少。

## 4. Prompt 工程

| 類型 | 目的 | 範例 |
|---|---|---|
| User Prompt | 動態、任務特定的指令 | 「解釋量子計算」 |
| System Prompt | 模型行為約束 | 定義 Agent 能力和限制 |

**進階技術：思維鏈**

思維鏈提示通過將複雜問題分解為逐步推理，顯著提升模型效能。在數學、邏輯和規劃任務中特別有效。

## 5. Tool 與 Function Calling

Tool 是擴展 LLM 超越文字生成的外部能力——API、資料庫、計算引擎。

**Function Calling 過程：**
1. 接收用戶查詢
2. 上下文組裝（Tool + 訊息）
3. LLM 決定是否需要 Tool
4. 開發者代碼執行 Tool
5. 結果返回給 LLM
6. 生成最終回應

## 6. MCP：模型上下文協議

MCP 是連接 AI 應用程式與外部系統的開放標準——可以將其視為「AI 應用程式的 USB-C」。由 Anthropic 創建，2024年11月開源。

**MCP 架構：**
- **協議：** 基於 JSON-RPC 2.0
- **組件：** Host、Client、Server
- **Host：** 容器/協調器
- **Client：** 1:1 伺服器連接
- **Server：** 提供能力

**MCP 採用（2026年）：**
- SDK 下載量：每月 9700 萬+（1年內從 10 萬增長）
- 公開伺服器：13,230+
- 公司伺服器：6個月內增長 232%
- 支援工具：Claude、ChatGPT、VS Code、Cursor 等 30+ 個

## 7. AI Agent：自主系統

**AI Agent = LLM + 規劃 + 記憶 + Tool**

AI Agent 不只是 LLM——它們是完整的系統，使用 LLM 作為推理引擎，並配備支援組件以進行推理、規劃和自主執行。

**Agent 組件：**
- LLM 大腦：推理的認知核心
- 規劃邏輯：問題分解
- 記憶：短期（上下文）+ 長期（向量資料庫）
- Tool：外部能力
- 觀察：環境感知

**Agent 循環（觀察-思考-行動-學習）：**
1. **觀察：** 感知環境（郵件、感測器、網路）
2. **思考：** 使用 LLM 推理決定行動
3. **行動：** 執行行動（發送郵件、更新 CRM）
4. **學習：** 記錄結果以改進
5. **重複：** 持續運作循環

## 8. Agent Skill：可擴展能力

Agent Skill 是擴展 Agent 能力的輕量級開放格式。由 Anthropic 創建，現為 30+ 個 AI 工具支援的開放標準。

**Skill 結構：**
- SKILL.md：必需的中繼資料 + 指令
- scripts/：可選的可執行代碼
- references/：可選的文件
- assets/：可選的範本、資源

**漸進式披露模式：**
- 發現（~100 Token）：啟動時載入名稱和描述
- 啟動（< 5000 Token）：任務匹配時載入完整 SKILL.md
- 執行（按需）：載入額外資源

**安全警告：** 安全研究人員已發現 341 個惡意 Agent Skill。安裝前務必驗證 Skill 來源。

## 實際應用

| 產業 | 使用場景 | 範例 |
|---|---|---|
| 客戶支援 | 26.5% 部署 | 一線機器人、情感分析 |
| 研究 | 24.4% 部署 | 數據分析、文獻回顧 |
| 內部自動化 | 18% 部署 | 工作流自動化、排程 |
| 教育 | 成長中領域 | 自適應導師、虛擬助教 |
| 金融 | 高價值應用 | 欺詐檢測、合規 |
| 醫療 | 受監管領域 | 就診前分診、醫療記錄 |

## 框架比較

| 框架 | 重點 | 最佳用途 |
|---|---|---|
| LangChain | 模組化、靈活 | 通用 Agent |
| CrewAI | 多 Agent 協作 | 複雜工作流 |
| AutoGen | 對話式 Agent | 企業應用 |
| LlamaIndex | 數據中心 | 知識密集型任務 |
| OpenAI Agents SDK | 直接 API 整合 | OpenAI 專注項目 |

## 關鍵要點

1. **AI Agent ≠ LLM** — Agent 是包含規劃、記憶和 Tool 的完整系統
2. **MCP 是標準** — 連接 AI 與外部系統的開放協議
3. **Tool 至關重要** — Function Calling 將 LLM 轉化為互動式 Agent
4. **Skill 實現可擴展性** — 添加能力的輕量級格式
5. **漸進式披露** — 按需載入 Skill 以優化 Token 使用
6. **安全很重要** — 驗證 Skill 來源以防止惡意代碼執行
7. **企業採用增長** — 57% 的組織已有 Agent 投入生產

---

*哪個 AI 概念在你第一次理解時最讓你驚訝？*

---

**References:**
- "Attention Is All You Need" — Vaswani et al. (2017)
- Anthropic MCP Specification (2024)
- Bilibili Video: Mark's Tech Workshop — "從 LLM 到 Agent Skill" (2026)
- LangChain, CrewAI, AutoGen, LlamaIndex Documentation
- OpenAI Function Calling API Reference
