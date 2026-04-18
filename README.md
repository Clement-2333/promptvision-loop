# Prompt Image Optimizer Agent

This skill integrates multiple AI models to iteratively improve images with prompt alignment and logic validation.

## Core Feature Upgrade

The skill now supports automatic input routing:
- Text input -> generate image, then validate against prompt and logic, then regenerate if needed.
- Image input -> analyze and confirm edit intent with user first, then modify image iteratively.

In both modes, the system reports concrete mismatch errors and corrects them in the next iteration.

## Mainstream Image Models (Domestic and Global)

Global:
- OpenAI: `gpt-image-1.5`, `gpt-image-1`, `gpt-image-1-mini`
- Google Vertex Imagen: `imagen-4.0-generate-001`, `imagen-4.0-ultra-generate-001`, `imagen-4.0-fast-generate-001`, `imagen-3.0-generate-002`
- Black Forest Labs FLUX: `flux-pro-1.1`, `flux-pro-1.1-ultra`
- Stability AI: `stable-diffusion-3.5-large`, `stable-diffusion-3.5-large-turbo`, `stable-diffusion-3.5-medium`
- Ideogram: `ideogram-v3`
- Midjourney: `v7`, `v6.1` (via proxy/automation bridge)

China:
- Alibaba DashScope Wan: `wan2.7-image-pro`, `wan2.7-image`, `wan2.6-t2i`, `wan2.2-t2i-plus`, `wan2.2-t2i-flash`
- ByteDance Seedream: `doubao-seedream-4.5`, `doubao-seedream-4.0`, `doubao-seedream-3.0-t2i`
- Tencent Hunyuan Image: `SubmitTextToImageJob`, `TextToImageRapid`, `TextToImageLite`
- Baidu Qianfan Image: `musesteamer-air-image`, `flux.1-schnell`, `qwen-image`
- Zhipu Image: `glm-image`, `cogview-4-250304`, `cogview-4`, `cogview-3-flash`

## Workflow

### Prompt-to-Image
1. Parse prompt requirements.
2. Generate image.
3. Evaluate prompt alignment and logic correctness.
4. If errors exist, output error list and regenerate.
5. Stop at target score or max iterations (3).

### Image Edit
1. Analyze uploaded image.
2. Build edit plan.
3. Confirm summary and edit intent with user.
4. Generate edited image.
5. Evaluate and iterate if needed.

## Integration Notes

Adapters:
- `image_generation_api`: generation or edit execution
- `llm_analysis_api`: requirement extraction, error diagnosis, scoring

Important runtime rules:
- `image_edit_requires_confirmation = true`
- `check_prompt_alignment = true`
- `check_logical_consistency = true`
- Always return machine-readable `detected_errors` when mismatch is found.
