---
title: "Generate HK Passport Photo with AI Prompt"
date: 2026-06-23T23:04:00+08:00
draft: false
tags:
  - tech
  - ai-image
  - passport-photo
summary: "AI passport photo generator for HK"
description: "Create HK-compliant passport photos using AI prompts and post-processing."
---

## The Challenge

Renewing a Hong Kong passport requires a biometric photo that meets strict specifications set by the Immigration Department. Common rejection reasons include uneven lighting, shadows, incorrect head positioning, and background issues. Professional photo services charge a premium for this, and retakes are often needed.

After experimenting with AI image generation, I discovered a workflow that produces government-compliant passport photos from a casual gallery photo — no studio visit required.

## The AI Prompt

The key is a precise, detailed prompt that constrains the AI output to meet biometric standards. Here is the exact prompt I used:

```
Create a professional passport-size photograph using the uploaded reference image as the source for facial identity, ensuring the person's authentic face—including skin tone, head shape, eyes, nose, lips, hairstyle, and natural appearance—is preserved exactly without any filters or artistic effects, while keeping the original eye size, nose size, and mouth size unchanged. The image must have a pure, solid white background that is shadow-free, with even studio-quality lighting throughout. The composition should be cropped to exactly 40mm × 50mm, with the head positioned at 64-72% of the image height, eyes at approximately 60% height, and 5-10% space left below the chin, ensuring the head and shoulders are perfectly centered with a neutral, professional expression and formal attire. The final image must meet biometric standards with accurate facial features and no distortions or effects applied.
```

### Why This Prompt Works

| Element | Purpose |
|---|---|
| "uploaded reference image as the source for facial identity" | Anchors the output to the real person |
| "preserved exactly without any filters or artistic effects" | Prevents AI beautification that changes biometric features |
| "keeping the original eye size, nose size, and mouth size unchanged" | Explicit constraint against feature distortion |
| "pure, solid white background that is shadow-free" | Meets HK Immigration background requirement |
| "40mm × 50mm" | Official HK passport photo dimension |
| "head positioned at 64-72% of the image height" | Biometric head ratio compliance |
| "eyes at approximately 60% height" | Eye level standard for biometric photos |
| "5-10% space left below the chin" | Prevents chin cropping |
| "neutral, professional expression" | Avoids rejection due to facial expression |

## Post-Processing Pipeline

The AI-generated output is a strong starting point, but additional processing is required before submission.

### Step 1: Watermark Removal

AI-generated images typically contain visible watermarks (e.g., star patterns) and potentially invisible watermarks (e.g., SynthID). These must be removed:

- **Visible watermark removal**: Reverse Alpha Blending technique eliminates star-pattern overlays without damaging facial detail
- **Metadata cleanup**: Remove C2PA, EXIF, and XMP metadata that may flag the image as "Made with AI"

### Step 2: Background Verification

Even with a "white background" prompt, the AI may produce slightly off-white or uneven backgrounds. Use AI-powered background removal to:

- Strip any remaining background imperfections
- Replace with pure white (RGB 255, 255, 255)
- Ensure no shadows around shoulders or hair edges

### Step 3: Face Detection & Positioning

Verify the output meets positioning requirements using face detection:

| Metric | Target |
|---|---|
| Eye level | ~60% from top |
| Head height | 64-72% of photo height |
| Chin position | ~70% from top |
| Space below chin | >5% |

If adjustments are needed, apply scaling and canvas padding to reposition the face within spec.

### Step 4: Lighting Balance

This is where most rejections happen. The HK Immigration Department is strict about:

- **Uneven contrast** (光度不足/過度曝光)
- **Shadows** on face or background
- **Over/under exposure**

Apply Gaussian soft mask adjustments to balance brightness across facial zones:

- Forehead: slightly reduce if over-lit
- Chin: brighten if under-lit
- Left-right cheek symmetry: target <5% difference

### Step 5: Final Output

Save the result as JPEG with:

- Dimensions: at least 1200px (W) × 1600px (H)
- File size: below 5MB
- Background: pure white (>220 brightness)
- Format: JPEG

## Result

The final photo was submitted to the HK Immigration Department's online passport renewal system and **passed validation on the first attempt** — no rejection, no retake needed.

This workflow demonstrates that AI image generation, combined with targeted post-processing, can handle real-world government compliance requirements. The key is understanding the specific biometric standards and encoding them directly into the prompt.

## Key Takeaways

1. **Prompt precision matters**: Generic "passport photo" prompts produce inconsistent results. Encoding exact measurements and constraints into the prompt dramatically improves output quality.

2. **Post-processing is essential**: AI generation alone is rarely sufficient. Watermark removal, background cleanup, and lighting balance are critical steps that bridge the gap between "AI output" and "government compliant."

3. **Know the standards**: Reading the official photo requirements before writing the prompt allows you to encode compliance directly, reducing iteration cycles.

4. **Generate multiple versions**: Create several variations with different adjustment strengths. Not every output will pass — having options reduces pressure on any single generation.

---

*What other creative uses have you found for AI image generation in everyday administrative tasks?*
