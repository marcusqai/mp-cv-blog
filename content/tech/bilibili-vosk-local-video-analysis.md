---
title: "Bypassing YouTube PO Token: Local Chinese Video Analysis with Vosk and WeasyPrint"
date: 2026-06-06T22:42:26+08:00
draft: false
tags:
  - tech
  - automation
  - vosk
  - transcription
summary: "Bypass YouTube PO token with local Vosk"
description: "Build a fully local Bilibili video analysis pipeline: yt-dlp bypass 412, Vosk cn-0.22 transcription, WeasyPrint bilingual PDF reports."
---

## English Version

## The Problem: Transcribing Chinese Video Without Cloud APIs

Most AI transcription workflows today depend on cloud services: OpenAI Whisper API, AssemblyAI, Sonix, or Groq. These work well, but they come with three costs you cannot eliminate:

- Your audio leaves the machine.
- Your credit card leaves your wallet.
- Your control leaves your hands.

For Chinese audio specifically, the trade-off gets worse. Whisper hallucinates on tonal languages, cloud APIs charge per minute, and rate limits interrupt long videos. When a user sends a Bilibili link and asks for a full Chinese transcript with deep analysis, a different approach becomes necessary.

This article documents a fully local pipeline that handles the entire chain: download, transcribe, translate, analyse, and publish — without any data leaving the workstation.

## The Architecture: Four Stages, Zero Cloud

The pipeline has four stages, each with a clear handoff:

```
Stage 1: Acquisition   →  yt-dlp + custom headers
Stage 2: Transcription →  Vosk cn-0.22 (1.3 GB model)
Stage 3: Analysis      →  Local LLM with chapter structure
Stage 4: Publishing    →  WeasyPrint (HTML → PDF, Chinese-safe)
```

Each stage produces a structured artifact (SRT, Markdown, HTML, PDF) that the next stage consumes. Failures in one stage do not corrupt the others.

## Stage 1: Downloading Bilibili Audio Past the 412 Wall

Bilibili returns HTTP 412 (Precondition Failed) to naive downloaders. The server checks the `User-Agent` and `Referer` headers before issuing video segments. yt-dlp alone is not enough.

```bash
# Resolve the short URL first
curl -sI -A "Mozilla/5.0..." "https://b23.tv/XXXXXXX" | grep -i location

# Then download with the right headers
yt-dlp \
  --js-runtimes node \
  --user-agent "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36..." \
  --referer "https://www.bilibili.com/" \
  -f bestaudio -x --audio-format mp3 --audio-quality 5 \
  -o "audio.%(ext)s" "https://www.bilibili.com/video/BVXXXXXXXXX"
```

The `--js-runtimes node` flag runs the JavaScript challenge in Node.js, which is mandatory for bypassing modern anti-bot checks. The combination of UA + Referer + Node runtime clears the 412 in most cases.

## Stage 2: Vosk Large Model vs fast-whisper

The user explicitly requested Vosk instead of fast-whisper. This is not a downgrade. Vosk's cn-0.22 model (1.3 GB) was trained on Mandarin with proper handling of tonal phonemes, and it runs entirely offline on CPU.

```bash
# Convert to 16 kHz mono WAV (Vosk standard)
ffmpeg -y -i audio.mp3 -ar 16000 -ac 1 -c:a pcm_s16le audio_16k.wav

# Run Vosk
vosk-transcriber \
  --lang cn \
  --model-name vosk-model-cn-0.22 \
  --input audio_16k.wav \
  --output transcript_raw \
  --output-type srt
```

Performance on an 18:41 audio file:
- Cold model load + transcription: 22 minutes on CPU
- Memory footprint: 3.6 GB peak
- Output: 506 SRT segments, 5,345 Chinese characters
- Accuracy: 90%+ for clean audio, lower for music overlays

The cost is time. The benefit is privacy and zero API charges.

## Stage 3: Cleaning ASR Output

Vosk inserts a space between every Chinese character. The cleanup is one regex:

```python
import re
cleaned = re.sub(r'(?<=[\u4e00-\u9fff]) (?=[\u4e00-\u9fff])', '', text)
```

After cleanup, common ASR errors must be fixed by the LLM stage: misheard words, simplified/traditional confusion, and punctuation gaps. The model is told to translate ASR mistakes into standard written Chinese, not to leave them as-is.

## Stage 4: Bilingual PDF with WeasyPrint

WeasyPrint handles Chinese rendering reliably if you avoid three traps:

1. Use a CJK font in the CSS: `font-family: "Noto Sans CJK TC", ...`
2. Apply `word-break: keep-all` to table cells so Chinese does not break one character per line
3. Use a state variable for lists, not `html_lines[-1]` checks — consecutive `1.` items get wrongly nested

```css
* { word-break: break-word; overflow-wrap: break-word; }
.md-table th, .md-table td { word-break: keep-all; }
```

Without these, the PDF bloats to 372 pages with each character on its own line. With them, a 14,000-character report fits in 30 pages.

## Case Study: Tianya God Post Analysis

The pipeline processed a 31-minute Bilibili video titled "Tianya God Post: The Fastest Way to Improve Cognition." The full transcript revealed a structured social-Darwinist argument built around three core claims:

1. **The Elevator Law** — wealth is allocated by cognitive dimension, not effort
2. **Three Cognitive Locks** — moral purism, linear hard-work thinking, and certainty addiction
3. **The Cut Cable** — those inside the elevator sever it so outsiders can never reach the top

The argument mixes 30% valid insight with 40% emotional manipulation and 30% factual critique. The pipeline produced a 30-page bilingual PDF that maps these claims to the user's actual work in APAC quality management, identifying which "locks" apply to leadership decisions and which are manipulative content tropes.

## Why Local Pipelines Matter for Professional Use

Three reasons this approach is not just a privacy preference:

- **Auditability**: every step is reproducible from the same artifacts
- **Cost predictability**: zero per-minute charges, no rate limits
- **Domain control**: a quality manager can adapt the model to industry jargon (corrosion standards, ASTM specs, supplier names) without retraining

The 22-minute transcription time is the real cost. For videos under 10 minutes, the trade-off is favourable. For longer content, batch processing during off-hours makes it practical.

---

*What local pipelines have you built to replace cloud APIs in your workflow?*

## 中文版

## 問題：無需雲端 API 即可轉錄中文視頻

目前大多數 AI 轉錄流程都依賴雲端服務：OpenAI Whisper API、AssemblyAI、Sonix 或 Groq。這些服務效果不錯，但有三大無法消除的代價：

- 您的音訊會離開本機。
- 您的信用卡會離開錢包。
- 您的控制權會離開雙手。

對於中文音訊，這個取捨更加明顯。Whisper 在聲調語言上會產生幻覺，雲端 API 按分鐘計費，速率限制會打斷長視頻。當有人傳送嗶哩嗶哩連結並要求完整中文轉錄與深度分析時，就需要採用不同的方法。

本文記錄一個完全本地的處理流程，涵蓋整個鏈條：下載、轉錄、翻譯、分析、發布——所有資料都不離開工作站。

## 架構：四個階段，零雲端

處理流程分為四個階段，每個階段都有清晰的交接：

```
階段 1：擷取        →  yt-dlp + 自訂標頭
階段 2：轉錄        →  Vosk cn-0.22（1.3 GB 模型）
階段 3：分析        →  本地 LLM 配合章節結構
階段 4：發布        →  WeasyPrint（HTML → PDF，中文安全）
```

每個階段都產出結構化產物（SRT、Markdown、HTML、PDF），由下一階段接收。某個階段失敗不會損壞其他階段。

## 階段 1：繞過 412 下載嗶哩嗶哩音訊

嗶哩嗶哩對普通下載工具返回 HTTP 412（前置條件失敗）。伺服器在發放視頻片段前會檢查 `User-Agent` 與 `Referer` 標頭。光靠 yt-dlp 不夠。

```bash
# 先解析短網址
curl -sI -A "Mozilla/5.0..." "https://b23.tv/XXXXXXX" | grep -i location

# 然後用正確標頭下載
yt-dlp \
  --js-runtimes node \
  --user-agent "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36..." \
  --referer "https://www.bilibili.com/" \
  -f bestaudio -x --audio-format mp3 --audio-quality 5 \
  -o "audio.%(ext)s" "https://www.bilibili.com/video/BVXXXXXXXXX"
```

`--js-runtimes node` 參數在 Node.js 中執行 JavaScript 挑戰，這是繞過現代反機器人檢查的必需設定。UA + Referer + Node runtime 三者結合在大多數情況下能清除 412 錯誤。

## 階段 2：Vosk 大模型 vs fast-whisper

用戶明確要求使用 Vosk 而非 fast-whisper。這不是降級。Vosk 的 cn-0.22 模型（1.3 GB）以普通話訓練，能正確處理聲調音素，完全在 CPU 上離線運行。

```bash
# 轉為 16 kHz 單聲道 WAV（Vosk 標準格式）
ffmpeg -y -i audio.mp3 -ar 16000 -ac 1 -c:a pcm_s16le audio_16k.wav

# 執行 Vosk
vosk-transcriber \
  --lang cn \
  --model-name vosk-model-cn-0.22 \
  --input audio_16k.wav \
  --output transcript_raw \
  --output-type srt
```

針對 18:41 音訊檔的效能：
- 冷啟動模型載入 + 轉錄：CPU 上 22 分鐘
- 記憶體峰值：3.6 GB
- 輸出：506 條 SRT 段，5,345 個中文字符
- 準確率：清晰音訊 90% 以上，有背景音樂時較低

代價是時間。收益是隱私與零 API 費用。

## 階段 3：清理 ASR 輸出

Vosk 在每個中文字之間插入空格。清理只需一行正則表達式：

```python
import re
cleaned = re.sub(r'(?<=[\u4e00-\u9fff]) (?=[\u4e00-\u9fff])', '', text)
```

清理後，常見的 ASR 錯誤必須由 LLM 階段修正：誤聽的字、簡繁體混淆、標點缺失。模型收到的指示是將 ASR 錯誤翻譯為標準書面中文，而不是原樣保留。

## 階段 4：使用 WeasyPrint 生成雙語 PDF

WeasyPrint 只要避開三個陷阱，就能可靠地處理中文渲染：

1. 在 CSS 中指定 CJK 字體：`font-family: "Noto Sans CJK TC", ...`
2. 對表格儲存格套用 `word-break: keep-all`，避免中文逐字換行
3. 列表處理使用狀態變數，不要用 `html_lines[-1]` 檢查——連續的 `1.` 項目會被錯誤地嵌套

```css
* { word-break: break-word; overflow-wrap: break-word; }
.md-table th, .md-table td { word-break: keep-all; }
```

缺少這些設定，PDF 會膨脹到 372 頁，每個字各佔一行。有了這些設定，14,000 字的報告剛好 30 頁。

## 案例研究：天涯神貼分析

該流程處理了一段 31 分鐘的嗶哩嗶哩視頻，標題為「天涯神貼：提升認知最快的方式」。完整轉錄揭示了一個圍繞三個核心論點建構的社會達爾文主義論證：

1. **電梯法則**——財富按認知維度而非努力分配
2. **三大認知鎖死**——道德潔癖、勤勞線性思維、確定性成癮
3. **剪斷電纜**——電梯內的人切斷纜線，使外人永遠無法抵達頂層

這個論點混合了 30% 的有效洞察、40% 的情緒操控、30% 的事實批判。流程產出了一份 30 頁的雙語 PDF，將這些論點對應到用戶在亞太品質管理中的實際工作，識別出哪些「鎖死」適用於領導決策，哪些是操控內容的套路。

## 為何本地流程對專業用途至關重要

這個方法不僅是隱私偏好，有三個理由：

- **可審計性**：每個步驟都能從相同產物重現
- **成本可預測**：零按分鐘計費，無速率限制
- **領域控制**：品質經理可以讓模型適應行業術語（腐蝕標準、ASTM 規格、供應商名稱），無需重新訓練

22 分鐘的轉錄時間是真正的成本。10 分鐘以下的視頻，這個取捨是划算的。對於更長的內容，在離峰時段批次處理使其可行。

---

*您在工作流程中建立過哪些本地管道來取代雲端 API？*
