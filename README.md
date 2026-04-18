# 🚀 PromptVision Loop（提示词视觉闭环优化引擎）

An advanced OpenClaw skill for optimizing AI-generated images through prompt alignment, logical analysis, and iterative refinement.

一个基于提示词对齐、逻辑分析与多轮迭代优化的 AI 图像优化 Skill。

## ✨ Features | 功能特点

- Prompt + image joint reasoning / Prompt + Image 联合分析
- Detect issues from prompt or generation / 自动识别问题来源（Prompt / 生成）
- Iterative optimization loop (max 3 rounds) / 多轮优化（最多 3 轮）
- Score-based stopping system / 评分系统控制停止
- Automatic prompt refinement / 自动优化 Prompt
- Multi-model support (OpenAI / Gemini / Doubao / SD / Imagen / FLUX / Wan) / 多模型支持（OpenAI / Gemini / 豆包 / SD / Imagen / FLUX / 万相）

## 🧩 Input Modes | 输入模式

### 1. Prompt Only（仅文本）

Input: `prompt`  
Output: optimized prompt + improved image result

输入：`prompt`  
输出：优化后的 prompt + 更合理的图像结果

### 2. Image Only（仅图片）

Input: `image`  
Output: issue analysis + inferred original prompt + repair suggestions

输入：`image`  
输出：问题分析 + 推测原始 prompt + 修复建议

### 3. Prompt + Image（完整模式）

Input: `prompt + image`  
Output: alignment analysis + prompt optimization + multi-round optimization result

输入：`prompt + image`  
输出：对齐分析 + Prompt 优化 + 多轮优化结果

## 🔁 Workflow | 工作流

Analyze -> Align -> Optimize Prompt -> Generate -> Evaluate -> Loop

分析 -> 对齐 -> 优化 Prompt -> 生成 -> 评估 -> 循环

## 🛠 Tech Stack | 技术栈

OpenClaw / GPT / Gemini / Doubao / Stable Diffusion / DALL-E / Imagen / FLUX

## 📦 Installation | 安装位置

`~/.openclaw/workspace/skills/`

## 🧪 Example | 示例

Input: `a penguin flying in the sky`  
Output: detect semantic issue -> optimize prompt -> generate a more realistic version

输入：`a penguin flying in the sky`  
输出：检测语义问题 -> 优化 Prompt -> 生成更合理版本

## 📄 License | 许可证

MIT License

Copyright (c) 2026 Clement-2333

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files...
