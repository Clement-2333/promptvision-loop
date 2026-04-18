---
name: prompt-image-optimizer-agent
description: Multimodal optimization agent with external AI model integration
---

# Prompt Image Optimizer Agent

This skill supports two user input modes:
- `prompt_to_image`: user sends text prompt, then system generates and validates image.
- `image_edit`: user sends image, then system confirms edit intent before modifying.

## External APIs

- `image_generation_api`
- `llm_analysis_api`

## Tools (Abstract APIs)

The system can call:

### 1. analyze_image(image)
Returns:
- issues
- scores

### 2. optimize_prompt(prompt, issues)
Returns:
- improved_prompt
- negative_prompt

### 3. generate_image(prompt)
Uses external model:
- OpenAI / Google Imagen / FLUX / Stability / Wan / Seedream / Hunyuan / Qianfan / Zhipu

Returns:
- new_image

### 4. evaluate_image(image)
Returns:
- score
- remaining_issues

## Input Routing

1. If user input is text-only, treat it as `prompt_to_image`.
2. If user input includes an image, treat it as `image_edit`.
3. For `image_edit`, the system must confirm recognized content and requested edits with user before generation.

## Validation Dimensions

For each generated result, validate:
- prompt alignment: required entities, attributes, style, composition, text elements.
- logical consistency: spatial relations, count consistency, temporal or causal conflicts.
- safety and obvious rendering failures: unreadable text, broken anatomy, impossible object relations.

## Workflow

### A. Prompt-to-Image Mode

Mandatory loop:
`analyze -> optimize -> generate -> evaluate -> loop`

Detailed steps:
1. Parse prompt into structured requirements.
2. Generate image using selected provider/model.
3. Analyze output against prompt requirements.
4. Evaluate logical correctness and visual quality.
5. If errors exist, list errors and regenerate with corrected prompt.
6. Stop when:
   - score >= target_score
   - AND logic_score >= min_logic_score
   - OR max_iterations reached

### B. Image Edit Mode

1. Analyze uploaded image and extract current content summary.
2. Build an edit plan from user instruction.
3. Ask for user confirmation before applying edits:
   - recognized image summary
   - planned modifications
   - protected elements to keep unchanged
4. After confirmation, run iterative edit loop (max 3 iterations):
   - optimize_prompt
   - generate_image (image-to-image or inpaint adapter)
   - evaluate_image
5. If mismatch remains, report specific errors and continue within max iterations.

## Output Format

```json
{
  "mode": "prompt_to_image",
  "iteration": 1,
  "optimized_prompt": "cinematic portrait, soft rim light, high-detail skin texture",
  "image_url": "https://example.com/generated/1.png",
  "provider": "openai",
  "model": "gpt-image-1.5",
  "score": 86,
  "logic_score": 82,
  "detected_errors": [
    {
      "type": "missing_element",
      "message": "The prompt requested a red umbrella, but none is visible.",
      "severity": "high"
    }
  ],
  "status": "continue"
}
```

Valid values:
- `mode`: `prompt_to_image` or `image_edit`
- `iteration`: integer from 1 to 3
- `optimized_prompt`: minimally edited prompt preserving original intent
- `image_url`: URL or storage reference of generated output
- `provider`: selected image provider
- `model`: selected image model
- `score`: numeric quality score (0-100)
- `logic_score`: numeric logic consistency score (0-100)
- `detected_errors`: machine-readable mismatch list
- `status`: `continue`, `need_user_confirmation`, or `done`

## Constraints

- Preserve original intent
- Do not over-modify prompt
- Limit to 3 iterations
- If input is image, confirmation is mandatory before edit

## Integration Contract

Tool to API mapping:
- `analyze_image(image)` -> `llm_analysis_api.analyze`
- `optimize_prompt(prompt, issues)` -> `llm_analysis_api.rewrite_prompt`
- `generate_image(prompt)` -> `image_generation_api.generate`
- `evaluate_image(image)` -> `llm_analysis_api.score_image`

Image generation request contract:

```json
{
  "provider": "openai",
  "model": "gpt-image-1.5",
  "prompt": "string",
  "negative_prompt": "string",
  "size": "1024x1024",
  "seed": 42,
  "response_format": "url",
  "edit_mode": false,
  "source_image_url": null,
  "mask_url": null
}
```

Image generation response contract:

```json
{
  "image_url": "https://...",
  "provider": "openai",
  "model": "gpt-image-1.5",
  "metadata": {}
}
```

LLM analysis/evaluation response contract:

```json
{
  "issues": ["text rendering unclear", "subject framing is off-center"],
  "score": 84,
  "logic_score": 80,
  "explanations": ["..."]
}
```

## Model Pools (For Adapter Routing)

Global:
- OpenAI: `gpt-image-1.5`, `gpt-image-1`, `gpt-image-1-mini`
- Google Vertex Imagen: `imagen-4.0-generate-001`, `imagen-4.0-ultra-generate-001`, `imagen-4.0-fast-generate-001`, `imagen-3.0-generate-002`
- Black Forest Labs: `flux-pro-1.1`, `flux-pro-1.1-ultra`
- Stability: `stable-diffusion-3.5-large`, `stable-diffusion-3.5-large-turbo`, `stable-diffusion-3.5-medium`
- Ideogram: `ideogram-v3`
- Midjourney: `v7`, `v6.1` (requires proxy/automation bridge; no direct public API contract in this skill)

China:
- Alibaba DashScope Wan: `wan2.7-image-pro`, `wan2.7-image`, `wan2.6-t2i`, `wan2.2-t2i-plus`, `wan2.2-t2i-flash`
- ByteDance Seedream: `doubao-seedream-4.5`, `doubao-seedream-4.0`, `doubao-seedream-3.0-t2i`
- Tencent Hunyuan: `SubmitTextToImageJob`, `TextToImageRapid`, `TextToImageLite`
- Baidu Qianfan: `musesteamer-air-image`, `flux.1-schnell`, `qwen-image`
- Zhipu Image: `glm-image`, `cogview-4-250304`, `cogview-4`, `cogview-3-flash`

Routing recommendations:
- Prefer region-local providers first.
- Use one fallback provider on timeout/error.
- Keep prompt semantics stable across providers.
- Return the final `provider` and `model` in output for traceability.
