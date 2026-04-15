# Week 1 笔记

## 本周目标

- MIT 6.S191 Lecture 1
- 3Blue1Brown 神经网络第 1-2 集
- PyTorch 环境搭建
- 跑通 MNIST MLP

## 学到的概念
1. Q, K, V are Query, Key and Value vectors, which no natural meaning. Each token asks what I want to look, Q: what I'm looking; K: What key I have, can others match me; V: if others notice me, what value can I provide.
2. E.g.: For a sentence "The animal didn't cross the street because it was too tired", process what "it" point out. 
   Q(it)
   key(animal), key(street)
   value(animal), value(street)
   **Q(it) mactch those keys, and read values to see which more match**
3. Matrices W^Q, W^K, W^V make embedding to diff things: q/k/v = xW^Q/K/V --> seems like what we "see" x by different "glasses".
4. q_i * k_j (QK^T) determines which token to look: i-th token should focus on j-th 
   V determines what take back from
   Attention(Q,K,V) = softmax(QK^T/sqrt{d_k})V
5. Transformer: divide by sqrt{d_k} --> avoid sofmax value extreme --> small graident, unstable training.
   sqrt{d_k} comes from normal distribution
   Counter example: d_k = 4, s_11 = 2, s_12 = 1, softmax([2, 1]) \approx [0.73, 0.27]
                    d_k = 64, s_11 = 16, s_12 = 8, softmax([16, 8]) \approx [0.9997, 0.0003] --> too sharp (one-shot) --> thinking only look itself --> maybe overfitting at early stage
6. Multihead attention: Try to parallely do attentions
   X --> for each head, Q/K/V_h = XW_h^Q/K/V --> attention Z_h = softmax(Q_hK_h^T/sqrt{d_k})V_h --> Z = Concat(Z_0, ..., Z_H-1)W^O
7. `nn.Linear` weight shape is `(out, in)`, not `(in, out)`. Three reasons:
   - **Math convention**: textbook form `y = Wx` requires `W` to be `(out, in)`. PyTorch stores `W` this way and computes `x @ W.T` internally (the transpose is a free flag in cuBLAS, no data movement).
   - **Memory layout**: tensors are row-major, so each row of `W` (length `in`) is contiguous. The inner loop of matmul scans `W[j, :]` contiguously → cache-friendly, SIMD-friendly.
   - **Slicing by output neuron**: `W[k]` directly gives the weights of the k-th output neuron (useful for pruning / inspection). Also aligns with `bias.shape = (out,)` on dim 0.
   - **Bias in LLMs**: original Transformer (2017) and BERT use `bias=True` for Q/K/V/O. Modern decoder-only LLMs (LLaMA, Mistral, Qwen) set `bias=False` — empirically no quality loss, saves params and tensor-parallel communication. GPT-2 / nanoGPT also default to `bias=False`.
8. **Causal mask** (`causal: bool`): controls whether a token can attend to future positions.
   - `causal=False`: attend to all positions (encoder, BERT, ViT, cross-attention).
   - `causal=True`: token `i` can only attend to `0..i` (decoder / GPT-style LM). Without this, the model cheats by copying the answer from the future during training.
   - Implementation: after `QK^T / sqrt(d_k)`, set future positions to `-inf` before softmax:
     ```python
     mask = torch.triu(torch.ones(T, T, dtype=torch.bool), diagonal=1)  # strict upper triangle
     scores = scores.masked_fill(mask, float('-inf'))
     ```
   - Details: `diagonal=1` keeps the diagonal (token attends to itself); use `float('-inf')` not `-1e9` (fp16 safe); cache the mask via `register_buffer`, don't rebuild each forward.
   - PyTorch 2.0+: `F.scaled_dot_product_attention(Q, K, V, is_causal=True)` uses FlashAttention, 2–4× faster. But write it by hand first for Week 1.


## 遇到的问题

## 一句话教给别人