---
title: "Control AI Presentation Styling with YAML Prompts"
date: 2026-06-03T08:30:00+08:00
draft: false
tags:
  - tech
  - openclaw
  - automation
summary: "Use structured YAML prompts to precisely control AI-generated slide designs."
description: "Learn how to use YAML syntax as a visual spec sheet for AI presentation tools, covering color palettes, typography, design systems, and output constraints."
---

## The Problem with AI-Generated Slides

Using AI to generate presentation slides is fast, but controlling visual style remains frustratingly difficult. When you feed AI vague descriptors like "modern" or "professional," it has no concrete reference. The result? Slides that look like generic templates, clash with your company brand colors, or are stuffed with unnecessary decorative elements.

The root cause is a communication gap. AI cannot interpret abstract aesthetic concepts the way a human designer can. It defaults to the closest built-in theme, which rarely matches your intent.

## The Solution: YAML as a Visual Spec Sheet

The fix is to change the syntax you use to communicate with AI. Instead of descriptive prose, use structured YAML syntax to specify exact parameters for color, typography, layout, and visual boundaries.

YAML (YAML Ain't Markup Language) is a plain-text format that requires no engineering background. Two rules to remember:

1. Separate keys and values with a colon followed by a space.
2. Respect indentation — do not arbitrarily delete leading spaces.

Think of YAML as an order form for AI. You specify what you want in precise terms, and AI follows the spec.

## The Four-Parameter Structure

Every complete YAML style spec consists of four sections:

### 1. Color Palette (`color_palette`)

Never tell AI "dark blue" — it will guess, and the guess will have a color cast. Provide exact hexadecimal color codes instead. You can extract these from your company website using a browser extension.

```yaml
color_palette:
  background: "#FFFFFF"
  primary: "#002060"
  accent: "#FFAB00"
```

| Field | Purpose |
|:---|:---|
| `background` | Base canvas color |
| `primary` | Main titles and key data points |
| `accent` | Highlights and callouts |

### 2. Typography (`typography`)

Fonts directly affect the tone of your presentation. Sans-serif fonts feel clean and are ideal for numbers and business reports. Serif fonts carry warmth and work well for storytelling.

```yaml
typography:
  font_family:
    primary_header: "Arial, bold"
    primary_body: "Inter, regular"
```

Specify header and body fonts separately. This gives AI clear boundaries for text hierarchy.

### 3. Design System (`design_system`)

Asking AI for "tech feel" is too vague. Give it a concrete scene description instead.

```yaml
design_system:
  visual_metaphor: "A clean digital dashboard with all information neatly arranged on a soft gray background"
  illustration_type: "App UI Mockups"
```

The `visual_metaphor` field anchors the overall look. The `illustration_type` field specifies what kind of graphics to use (or avoid — you can explicitly say "no people photos").

### 4. Output Constraints (`output_constraints`)

AI has a tendency to fill empty space with decorative elements. A forbidden list draws clear red lines in advance.

```yaml
output_constraints:
  visual_style:
    - forbidden: "Glowing neon circuit boards or cyberpunk style"
    - forbidden: "Crowded layouts — white space must be preserved"
```

## Three Ready-to-Use Business Templates

### Template 1: 2.5D Isometric Style

**Best for:** System architecture, logistics diagrams, smart city layouts

The entire slide uses a fixed 30-degree isometric perspective. Elements arrange like building blocks, making structural relationships immediately visible.

```yaml
design_system:
  philosophy:
    theme: "The Digital Sandbox"
    keywords: ["Isometric", "Modular", "Toy-like Precision"]
    visual_metaphor: "A clean, illuminated digital display platform where all data objects appear as精致 3D block components in perfect order at a 30-degree angle."
  color_palette:
    background: "#F0F4F8"
    primary_blue: "#2A60FF"
    success_green: "#00D084"
    alert_orange: "#FFAB00"
    soft_shadow: "#D1D9E6"
  visual_style:
    illustration_type: "3D Isometric Models & Voxel-style Components"
    image_generation_policy:
      rule_1: "Strict Isometric View: fixed 30-degree angle, no perspective vanishing points."
      rule_2: "Ambient Occlusion: uniform soft lighting with natural contact shadows."
typography:
  font_family:
    primary_header: "Noto Sans TC Rounded / Montserrat"
    primary_body: "Inter"
```

### Template 2: Executive Consultant Style

**Best for:** Business decisions, executive reports

Clean layouts with zero decoration. Each page communicates one point. Structure follows the Minto Pyramid Principle — conclusion first, details second.

```yaml
design_system:
  philosophy:
    theme: "The Minto Pyramid Principle"
    keywords: ["MECE", "Action Title", "Data Density"]
    visual_metaphor: "A million-dollar strategic consulting report — clean and surgically precise."
  color_palette:
    background: "#FFFFFF"
    primary_text: "#333333"
    corporate_navy: "#002060"
    insight_red: "#B00000"
  layout_structure:
    - "Header: Top 15% for Action Title"
    - "Body: Middle 80% for charts or logic trees"
    - "Footer: Bottom 5% for data sources"
typography:
  font_family:
    primary: "Arial / Helvetica / Noto Sans TC"
  text_rules:
    action_title: "Every page title must be a complete sentence stating the core point."
output_constraints:
  visual_style:
    - forbidden: "Cartoons, illustrations, gradient backgrounds, emotional colors"
    - forbidden: "Drop shadows or 3D effects"
  content_logic:
    - forbidden: "Center-aligned long text. Professional documents are always left-aligned."
```

### Template 3: SaaS Product Style

**Best for:** Product introductions, feature demonstrations, workflow diagrams

The layout resembles a well-organized app interface. Each content block sits inside an independent white card. Complex information looks simple.

```yaml
design_system:
  philosophy:
    theme: "The Seamless Interface"
    keywords: ["Card-Based", "Depth", "Soft UI"]
    visual_metaphor: "A carefully organized dashboard with all information floating quietly on a soft gray canvas."
  color_palette:
    background: "#F7F8FA"
    primary_text: "#1A1C1E"
    action_blue: "#0066FF"
    card_white: "#FFFFFF"
    border_light: "#E1E4E8"
  visual_style:
    illustration_type: "App UI Mockups & Clean Component Visualization"
    image_generation_policy:
      rule_1: "Floating Screenshots: product screens inside white rounded cards with subtle blur shadows."
      rule_2: "Component Focus: prioritize partial UI components over chaotic full-screen captures."
typography:
  font_family:
    primary: "Inter / San Francisco / Noto Sans TC"
output_constraints:
  visual_style:
    - forbidden: "Sharp corners and complex 3D backgrounds"
    - forbidden: "Real person photos (except as avatar headshots)"
  content_logic:
    - forbidden: "Dense text blocks. Too much text breaks interface秩序."
```

## How to Use These Templates

The workflow has two parts:

**Part 1: AI Planning (NotebookLM, ChatGPT, Claude)**

Paste your presentation outline together with the YAML spec. Add this instruction:

> "You are a professional presentation designer. Strictly follow the outline below and apply the YAML style specification above to generate visual slide drafts. Maintain structural consistency. Do not add external data or unnecessary decorations."

The AI will not output a .pptx file. Instead, it will list the exact color, font, and layout rules for each slide.

**Part 2: Visual Output (Gamma, Canva, PowerPoint)**

Copy those rules into your visual generation tool. Gamma can apply custom themes directly. Canva accepts style instructions. Or you can follow the rules manually in PowerPoint.

## The Real Time-Saving Workflow

YAML prompts solve only the final visual layer. The full productivity workflow looks like this:

| Phase | Who Handles It | What Happens |
|:---|:---|:---|
| Data collection | AI | Gather and organize source material |
| Outline structure | AI | Build logical slide hierarchy |
| Visual styling | You (via YAML) | Set color codes, fonts, constraints |
| Core观点 refinement | You | Edit key messages and decision points |

AI handles roughly 80% of the mechanical work. You reserve your energy for the 20% that requires judgment — the core arguments and strategic decisions that no template can replace.

---

*What other AI tools have you tried structuring with parameter-based prompts?*
