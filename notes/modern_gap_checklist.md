# 现代 LLM Gap Checklist

> 从 *Attention Is All You Need* (2017) 到 2026 modern LLM 之间需要补的 9 个核心点。
> 用作每周收尾时的自检:这一项我能给别人讲清楚动机 + 实现细节吗?

## 优先级顺序

按"对理解现代 LLM 的边际收益"排序,前 6 项偏架构,后 3 项偏实践。

| # | 主题 | 一句话动机 | 关联周次 | 关键 paper |
|---|---|---|---|---|
| 1 | **Decoder-only 为什么主流** | encoder-decoder → decoder-only 简化训练目标,scaling 更干净 | Week 2 | GPT-2/3 |
| 2 | **RoPE** | 相对位置编码,推理时可外推到训练未见的长度 | Week 2 | RoFormer (2021) |
| 3 | **Pre-norm + RMSNorm** | pre-norm 稳定深层训练;RMSNorm 去掉均值更省算力 | Week 2 | Xiong et al. 2020;Zhang & Sennrich 2019 |
| 4 | **KV cache** | 推理时把过去 K/V 缓存,把 O(T²) 摊到 O(T) per token | Week 2 / Week 4 | (无单一 paper,看 nanoGPT/llama 实现) |
| 5 | **SwiGLU** | FFN 用门控变体,同等参数下质量更好 | Week 2 | GLU Variants (Shazeer 2020) |
| 6 | **MQA / GQA** | 多 query 头共享 K/V,推理 KV cache 显存大幅下降 | Week 2 | MQA (Shazeer 2019);GQA (Ainslie 2023) |
| 7 | **FlashAttention 的 I/O-aware 动机** | attention 瓶颈是 HBM 访存而非 FLOPs;tiling + 不存中间矩阵 | Week 1 末 / Week 2 | FlashAttention (Dao 2022) |
| 8 | **LoRA** | 冻结预训练权重,只学低秩增量,适配千倍参数高效化 | Week 5 | LoRA (Hu 2021) |
| 9 | **SFT / alignment / eval 概览** | next-token loss 不等于"有用",对齐 + 评估是另一套问题 | Week 6 / Week 7 | InstructGPT;DPO;LLM-as-judge |

## 自检方式

每完成一周,过一遍这张表,问自己:

1. **动机层**:为什么需要这个?不要它会怎么样?(能讲出一个具体 failure mode)
2. **实现层**:如果让我从空白写一遍,关键 5 行代码长什么样?
3. **trade-off 层**:这个设计的代价是什么?什么场景下不该用?

如果三个问题都能 1 分钟内回答,这一项就算通过。

## 与现有 plan 的对应

这些点已经在 8 周 plan 里:

- **Week 2 (nanoGPT)**:#1, #2, #3, #4, #5, #6 → block 重写时全部碰到
- **Week 1 末或 Week 2**:#7 → 数值对比 `F.scaled_dot_product_attention`
- **Week 5**:#8
- **Week 6 / 7**:#9

所以这份 checklist 不是"要新加任务",是"在现有任务里别只跑通,要回到这张表上自检"。
