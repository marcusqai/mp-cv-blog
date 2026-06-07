---
title: "Self-Hosted Bilibili Video Analysis with Vosk and AI Assistant"
date: 2026-06-07T06:50:00+08:00
draft: false
tags:
  - tech
  - vosk
  - asr
  - ai
summary: "Local Chinese video analysis pipeline."
description: "A self-hosted pipeline: download, transcribe with Vosk cn-0.22, analyze with an AI assistant, output PDF reports."
---

## The Problem

Most AI transcription workflows today depend on cloud services. The cost is time, money, and control. Every minute of audio is metered, every transcript leaves your machine, and every API key is a dependency waiting to expire.

I wanted something different: a **fully self-hosted pipeline** that takes a Chinese video file from download to polished PDF report without sending a single byte to a third-party transcription provider. This article documents every stage, the gotchas, and the actual file sizes and timings on a 2026 laptop.

Three reasons this approach matters:

- **Auditability** — every byte stays on the local machine
- **Cost predictability** — no per-minute transcription fees
- **Domain control** — fine-tune prompts and CSS without platform constraints

---

## Pipeline Architecture

```
Bilibili URL
    ↓
[1] yt-dlp download
    ↓
[2] ffmpeg audio extraction (mp3)
    ↓
[3] ffmpeg resample (16 kHz mono WAV)
    ↓
[4] Vosk cn-0.22 ASR
    ↓
[5] Subtitle cleanup
    ↓
[6] AI assistant analysis (markdown)
    ↓
[7] Markdown → HTML (Python)
    ↓
[8] WeasyPrint → PDF
```

Each stage is independent and can be run separately. Total wall-clock time for an 18-minute Chinese video: roughly 22 minutes on a modern laptop.

| Stage | Output | Size |
|:---|:---|:---|
| 1. yt-dlp | `video.mp4` | ~120 MB |
| 2. ffmpeg | `audio.mp3` | 12 MB |
| 3. ffmpeg | `audio_16k.wav` | 35 MB |
| 4. Vosk | `transcript_raw` | 37 KB |
| 5. Cleanup | `transcript_clean.txt` | 17 KB |
| 6. AI analysis | `analysis.md` | 32 KB |
| 7. HTML | `analysis.html` | 48 KB |
| 8. PDF | `analysis.pdf` | 1.5 MB |

---

## Prerequisites

- `yt-dlp` 2026.x or later (with Node.js 18+ for `--js-runtimes node`)
- `ffmpeg` (any recent build)
- Python 3.10+ with `vosk`, `weasyprint`, and `Pillow`
- Vosk `cn-0.22` Mandarin model (1.3 GB download)
- An AI assistant capable of reading the cleaned transcript and producing structured analysis
- ~3 GB of free disk space for the working set

The AI assistant stage is intentionally decoupled from the rest of the pipeline. The transcript is a plain text file, so any tool that reads text can be plugged in here. The rest of the pipeline is fully offline.

---

## Stage 1: Download with yt-dlp

Bilibili returns HTTP 412 Precondition Failed to naive downloaders. The User-Agent header is a precondition for success, and many debugging hours have been lost to this single missing line.

```bash
yt-dlp \
  --js-runtimes node \
  --user-agent "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36" \
  --referer "https://www.bilibili.com" \
  -f "bv*[ext=mp4]+ba[ext=m4a]" \
  --merge-output-format mp4 \
  -o "video.%(ext)s" \
  "https://www.bilibili.com/video/BVXXXXXXXXX"
```

The `--js-runtimes node` flag helps bypass JavaScript challenges during manifest fetch. Without it, downloads frequently stall or return partial files. The `--referer` flag is required for Bilibili's CDN to serve the video segments.

A typical 30-minute video downloads in about 90 seconds on a 100 Mbps connection.

---

## Stage 2: Audio Extraction (MP3)

Vosk expects 16 kHz mono WAV, but I keep the intermediate MP3 for archival and re-processing. The two-step conversion avoids re-encoding the same audio twice.

```bash
ffmpeg -i video.mp4 -vn -ac 2 -ar 44100 -ab 192k audio.mp3
ffmpeg -i audio.mp3 -ac 1 -ar 16000 -acodec pcm_s16le audio_16k.wav
```

For an 18:41 video:

- `audio.mp3` ends up around 12 MB
- `audio_16k.wav` ends up around 35 MB

The WAV is small enough to fit in memory for the ASR stage on a typical laptop. Vosk's streaming recognizer reads 4000-sample chunks, so peak memory stays well under 1 GB.

---

## Stage 3: Vosk cn-0.22 Transcription

The Vosk `cn-0.22` model was trained on Mandarin with proper handling of tonal phonemes. Whisper can hallucinate on tonal languages more often than people expect, especially when the source is fast, accented, or low-quality audio. Vosk gives more predictable quality for short Mandarin clips.

```python
from vosk import Model, KaldiRecognizer
import wave
import json

model = Model("models/vosk-model-cn-0.22")
wf = wave.open("audio_16k.wav", "rb")
rec = KaldiRecognizer(model, wf.getframerate())
rec.SetWords(True)

results = []
while True:
    data = wf.readframes(4000)
    if len(data) == 0:
        break
    if rec.AcceptWaveform(data):
        results.append(json.loads(rec.Result()))
results.append(json.loads(rec.FinalResult()))
```

The output is a list of word-level timestamps. An 18-minute clip produces roughly 500 subtitle entries, around 5,300 characters after cleanup. Wall time on a modern CPU: about 10 minutes for 18 minutes of audio. A GPU build of Vosk would cut this to roughly 90 seconds, but the CPU version is good enough for occasional use.

---

## Stage 4: Subtitle Cleanup

Vosk's raw output contains ASR artifacts: repeated fragments, misrecognized numbers, punctuation drift, and occasional English code-mixing. A small cleanup pass merges consecutive segments and removes obvious errors.

```python
def merge_segments(entries, max_gap=3.0):
    merged = []
    for start, text in entries:
        if merged and start - merged[-1][0] < max_gap:
            merged[-1] = (merged[-1][0], merged[-1][1] + " " + text)
        else:
            merged.append((start, text))
    return merged
```

A 3-second gap threshold empirically produces readable paragraphs without losing sentence boundaries. Tighter gaps (1 second) fragment the text, wider gaps (5+ seconds) lose the conversational flow.

The cleanup also strips Vosk's internal formatting tags, normalizes whitespace, and drops segments shorter than two characters (usually noise).

---

## Stage 5: AI Assistant Analysis

The cleaned transcript is sent to an AI assistant with a structured prompt. The prompt template asks for:

- A short executive summary at the top
- Sectioned analysis (overview, chapter structure, key insights)
- Tables for comparisons
- A practical recommendations section
- Plain-text references rather than URLs

A typical prompt for an 18-minute video produces around 5,000 words of analysis. The AI assistant reads the cleaned transcript, applies the requested structure, and returns the analysis as markdown. The output is saved directly as `analysis.md`, which becomes the source for the next stage.

The analysis stage benefits from context window: an 18-minute Chinese video compresses to about 5,000 characters after cleanup, which fits comfortably in any modern AI context. For longer videos, the transcript can be split into chapters and analyzed in sequence, with the AI producing a final synthesis at the end.

This stage is the one place where the pipeline touches an external service, but the transcript has already been produced locally and the analysis output stays local. The cost is one round trip with the assistant, not a per-minute metered API.

---

## Stage 6: Markdown to HTML

A small Python script walks the markdown line by line, handling headings, tables, code blocks, and inline emphasis. The CSS is the hard part — getting the typography right for both Chinese and English is a constant tuning exercise.

```python
def md_to_html(md_content: str) -> str:
    lines = md_content.split('\n')
    sec_counter = 0
    in_table = False
    html_lines = []
    
    for line in lines:
        if line.startswith('# '):
            sec_counter += 1
            html_lines.append(f'<h1 id="sec{sec_counter}">{process_inline(line[2:])}</h1>')
        elif line.startswith('## '):
            html_lines.append(f'<h2>{process_inline(line[3:])}</h2>')
        elif '|' in line and line.strip().startswith('|'):
            # ... table detection and rendering
            pass
        # ... more rules
    return wrap_html('\n'.join(html_lines))
```

The output is a single self-contained HTML file. No external CSS, no JavaScript, no fonts hosted on third-party CDNs. The cover section uses a CSS gradient, the table cells use `word-break: keep-all` to prevent CJK character fragmentation, and the print stylesheet adds page-break controls.

---

## Stage 7: WeasyPrint to PDF

Without proper CSS for CJK fonts, the PDF bloats to hundreds of pages with each character on its own line. The fix is to declare the font family and use a font that ships with the system:

```css
body {
  font-family: "Noto Sans CJK TC", "Noto Sans CJK", "Noto Sans", sans-serif;
  word-break: break-word;
  overflow-wrap: break-word;
}

.md-table th,
.md-table td {
  word-break: keep-all;
  overflow-wrap: normal;
}
```

After that, `WeasyPrint` produces a clean PDF in about 4 seconds.

```bash
weasyprint analysis.html analysis.pdf
```

The print stylesheet also controls page breaks: `h1`, `h2`, `h3` use `page-break-after: avoid`, and `p`, `li`, `table` use `page-break-inside: avoid`. The result is a PDF that reads naturally on screen and on paper.

---

## Lessons Learned

1. **HTTP 412 is the silent killer on Bilibili.** Always set both `User-Agent` and `Referer`. The Referer header is easy to forget and impossible to debug from the error message alone.
2. **Whisper is not a panacea for tonal languages.** Local models like Vosk `cn-0.22` give more predictable quality for Mandarin without GPU requirements.
3. **CSS for CJK content is a separate discipline.** Word-break and overflow-wrap rules differ from English typography. Without them, a 20-page report becomes a 200-page disaster.
4. **Decentralized workflows avoid single points of failure.** Each stage of the pipeline is a separate file with a separate output. If stage 6 fails, stage 5's markdown is still usable on its own.
5. **The handoff between stages is the most fragile part.** Errors propagate. A robust pipeline validates each stage's output (file exists, expected size, valid format) before moving on.
6. **A self-hosted pipeline is only as good as its error messages.** When a stage fails silently, you lose hours. Logging the start, end, and size of each stage is non-negotiable.
7. **Keep the AI analysis stage decoupled.** The transcript is a plain text file. Any tool that reads text can be plugged in here. Avoid hard-coding a specific LLM endpoint in the pipeline; treat the assistant as a black box that takes text in and produces markdown out.

---

## Throughput Notes

| Stage | Wall time (18-min video) |
|:---|:---|
| 1. yt-dlp download | 90 s |
| 2. ffmpeg audio extract | 12 s |
| 3. ffmpeg resample | 8 s |
| 4. Vosk ASR | 600 s |
| 5. Subtitle cleanup | 5 s |
| 6. AI analysis | 90-360 s |
| 7. Markdown to HTML | 2 s |
| 8. WeasyPrint to PDF | 4 s |
| **Total** | **~811-1,081 s (~13-18 min)** |

The bottleneck is Vosk's CPU-bound ASR. A GPU build of Vosk would cut stage 4 from 600 s to roughly 90 s, dropping total pipeline time to about 8 minutes.

The second bottleneck is the AI analysis stage. A 5,000-character transcript takes about 90 seconds to analyze with a single round trip. For longer videos, splitting into chapters and running sequential analyses can add up to 6 minutes.

---

## When to Use This Pipeline

This approach is a good fit when:

- Audio is Mandarin or another language Vosk supports well
- Privacy or audit requirements prohibit cloud transcription
- The volume justifies a one-time setup cost
- You need structured analysis, not just a transcript

It is not a good fit when:

- You need real-time transcription (use streaming APIs)
- The content is mixed-language with heavy English code-switching (Whisper handles this better)
- You have less than 30 minutes of audio per month (the setup cost dominates)

For most personal and small-team use cases, this pipeline replaces a $20/month transcription subscription with a one-time afternoon of setup and an hour of occasional maintenance.

---

*What self-hosted pipelines have you built for content analysis?*
