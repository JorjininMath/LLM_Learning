# 大规模训练系统 · 概念地图

> **为什么放这里**:单人无多卡算力,不值得主线实操。但要知道关键概念,将来做 infra 研究/面试/读 paper 不掉链子。

## 三种并行(核心)

| 并行方式 | 切什么 | 解决什么 |
|---|---|---|
| **Data Parallel (DP)** | 切 batch | 训练吞吐 |
| **Tensor Parallel (TP)** | 切单层权重矩阵 | 单层太大装不下 |
| **Pipeline Parallel (PP)** | 切不同层到不同卡 | 模型太深 |
| **Sequence / Context Parallel** | 切 seq 维 | 长上下文 |

真实大模型训练 = **3D/4D 并行组合**(如 Megatron-LM)。

## ZeRO / FSDP 家族(省显存)

核心直觉:**优化器状态 + 梯度 + 参数** 可以在 DP workers 间切分。
- ZeRO-1:切 optimizer states
- ZeRO-2:再切 gradients
- ZeRO-3 / FSDP:再切 parameters(用时 all-gather)

省显存代价:多一次通信。

## 必知工程技巧(Week 4 已接触)

- **Gradient checkpointing**:省激活值显存,代价重算一次 forward
- **Mixed precision (bf16/fp16)**:算得快、省显存
- **Gradient accumulation**:小 batch 模拟大 batch
- **Flash Attention**:融合 kernel,省显存 + 加速

## 参考资源

- HuggingFace: [Model parallelism](https://huggingface.co/docs/transformers/v4.15.0/parallelism)
- DeepSpeed ZeRO paper
- Megatron-LM paper
- PyTorch FSDP tutorial
- Stas Bekman《Machine Learning Engineering》开源书

## 什么时候回来深挖

以后真要做 LLM infra 研究 / 多卡训练时,再回来读 ZeRO 原论文、跑 FSDP 小实验。现在不花时间。
