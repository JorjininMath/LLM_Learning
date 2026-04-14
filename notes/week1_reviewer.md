# Week 1 · 审稿人笔记

> 本周做完后填。空着的问题自己想答案,别看别人的。
> 目标不是“抄对结论”,而是把 `02_Transformer/week1_mha.ipynb` 里的实现、维度流和 reviewer 视角连起来。

## 0. 本周最小过关标准

- [ ] 我能不看参考,写出 single-head attention 前向
- [ ] 我能解释 `Q @ K^T / sqrt(d_k)` 的每个维度在表示什么
- [ ] 我能手推 multi-head 的 reshape / transpose 维度流
- [ ] 我能说明“独立多头”与“一次大投影再分头”的等价性
- [ ] 我能用自己的实现通过最基本的 shape / causal / 概率和 sanity check

## 1. 维度流(自己画一遍)

输入 `x: (B, T, d_model)` → 经过 MHA → 输出 `(B, T, d_model)`。

先写 single-head,再写 multi-head。不要只写结果,要写“这一维代表什么”。

### 1.1 Single-head

```
x: (B, T, d_model)
  → Q/K/V: ?
  → scores = Q @ K^T / sqrt(d_k): ?
  → mask 后 scores: ?
  → attn = softmax(scores): ?
  → out = attn @ V: ?
```

补一句:在 `scores[i, j]` 里,`i / j` 分别对应什么?

### 1.2 Multi-head

```
x: (B, T, d_model)
  → Q/K/V: ?
  → reshape: ?
  → transpose: ?
  → scores: ?
  → attn: ?
  → out (per-head): ?
  → transpose back: ?
  → concat / reshape: ?
  → W_o: (B, T, d_model)
```

补一句:为什么 reshape + transpose 后,就能用一次 batched matmul 同时算完所有 head?

## 2. Sanity Checks(先跑通,再谈理解)

把你在 notebook 里做过的最小验证写下来:

### Check 1: shape
- `single-head out.shape == (B, T, d_k)`
- `single-head attn.shape == (B, T, T)`
- `multi-head out.shape == (B, T, d_model)`
- `multi-head attn.shape == (B, h, T, T)`

### Check 2: causal mask
- 为什么 `attn.triu(1)` 应该接近 0?
- 如果不接近 0,你最先怀疑哪一步?

### Check 3: probability
- 为什么 `attn.sum(-1)` 应该约等于 1?
- 如果不是 1,最可能是哪两类 bug?

### Check 4: PyTorch 对数值
- 你有没有把自己的 `W_q / W_k / W_v / W_o` 对到 `nn.MultiheadAttention`?
- 如果数值对不上,先查权重布局、`batch_first` 还是先查 mask?

## 3. 等价性证明

**命题**:`h` 个独立 attention head 拼接 = 一次大投影后 reshape 分头算。

### 你的证明

至少包含这 4 步:

1. 写出“每个 head 独立投影”的数学形式
2. 写出“一次大矩阵投影再 reshape”的数学形式
3. 说明为什么把大投影按 head 切开后,与逐头线性层完全一致
4. 说明输出拼接后再过 `W_o` 为什么仍保持等价

### 最低答到

- 你能说清 `W_q, W_k, W_v` 的“大矩阵形式”和“逐头形式”之间的对应关系
- 你能解释这个等价式为什么是后续看 `FlashAttention / MQA / GQA` 的基础
- 你能说出参数量主要还是 `Q/K/V/O` 这四个投影在贡献

## 4. 审稿人自问

### Q1: 为什么除 `sqrt(d_k)`?不除会怎样?
最低答到:
- 点积的数值尺度会随维度变大
- 不缩放时 softmax 更容易饱和
- softmax 饱和会让梯度变差,训练更不稳定

(你的答案)

### Q2: Softmax 沿哪一维?另一维会怎样?
最低答到:
- softmax 应沿 key 维度做,让“每个 query 对所有 key 的权重和”为 1
- 如果沿错维度,归一化对象会变掉,语义也会变掉

(你的答案)

### Q3: Multi-head 跟 single-head with larger d 的区别?
最低答到:
- multi-head 不是简单“参数变多”
- 它允许不同 head 学不同子空间 / 不同关系模式
- 你要顺带讨论:参数量、表达力、并行性之间的关系

(你的答案)

### Q4: Causal mask 在 softmax 前还是后加?为什么?
最低答到:
- mask 应加在 softmax 前
- 典型做法是把非法位置设成 `-inf`
- 如果 softmax 后再乘 0,概率分布一般不再正规化

(你的答案)

### Q5: `.view()` vs `.reshape()`?什么情况 `.view()` 报错?
最低答到:
- `.view()` 依赖 contiguous 内存布局
- `transpose` 后 tensor 往往不再 contiguous
- `.reshape()` 可能返回 view,也可能隐式拷贝

(你的答案)

### Q6: 为什么 notebook 里先用 `bias=False`?
最低答到:
- 这是为了先减少变量,把注意力机制本身看清楚
- 你要能判断:加不加 bias 会影响什么,不会影响什么

(你的答案)

## 5. 最 sus 的实验细节

读完《Attention Is All You Need》后,不要只写“我不懂什么”,而要像 reviewer 一样挑:

- 哪些实现细节没交代清楚,但可能显著影响结果?
- 哪些结论今天看像是“规模 + 数据”共同作用,不一定纯是架构功劳?
- 论文里哪个 ablation 你觉得还不够?如果你是审稿人,会要求补什么?

(写在这里)

## 6. 更好的替代?

从 `FlashAttention / MQA / GQA / ALiBi` 里挑一个,固定回答这 3 个问题:

1. 它改动了原始 MHA 的哪一步?
2. 它主要想解决什么瓶颈?(显存 / 吞吐 / 长上下文 / KV cache 等)
3. 它换来了什么代价或限制?

(写在这里)
