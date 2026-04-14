# 多模态扩展 · 概念入口

> **为什么放这里**:主线 roadmap 是 text-first,多模态不在 8 周范围内。这里放一张概念地图,以后要扩 foundation model 视角时有入口。

## 核心思路

多模态 LLM 的统一范式:**把非文本模态投影到 LLM 的 token 空间**。

```
image → vision encoder → projector → LLM token space
audio → audio encoder → projector → LLM token space
```

## 三种典型架构

| 架构 | 代表 | 特点 |
|---|---|---|
| **Cross-attention** | Flamingo | LLM 冻结,插入 cross-attn 层 |
| **Projector (MLP)** | LLaVA, MiniGPT | 简单 MLP 映射 vision features → LLM |
| **Native multimodal** | GPT-4o, Gemini | 端到端一起训,无明显边界 |

## 关键概念

- **Vision encoder**:通常是 CLIP ViT 或 SigLIP
- **Projector**:最小可以就是一个 Linear / MLP
- **Multimodal instruction tuning**:LLaVA 的核心贡献
- **Tool use + perception**:agent 调用视觉工具,而非端到端

## 入门论文(未来读)

1. CLIP(对比学习的图文对齐)
2. Flamingo(cross-attention 范式)
3. LLaVA(projector 范式,开源友好)
4. Qwen-VL / InternVL(中文社区强 baseline)

## 什么时候回来

主线 8 周完成后,如果要扩展到 foundation model,用 LLaVA 作为入口自己复现一个 vision-LLM 就行。架构不难,难的是数据。
