# Week 1 · 学习材料清单

> 按优先级排列。**不要一口气看完再动手**,看 2h 就开 notebook 写,边写边回查。

## 必看(核心 3 个)

### 1. Jay Alammar《The Illustrated Transformer》
- 链接:https://jalammar.github.io/illustrated-transformer/
- 约 1h · **先读这个**,建立 Q/K/V 和 multi-head 的可视化直觉
- 读它再读论文,论文会顺很多

### 2. MIT 6.S191 Lecture 2(RNN → Attention → Transformer)
- YouTube 搜 "MIT 6.S191 2024 Lecture 2"(或最新年份)
- 约 1h · 把直觉形式化
- 重点:self-attention 的动机,positional encoding,为什么要 multi-head

### 3. 《Attention Is All You Need》(Vaswani et al. 2017)
- arXiv: 1706.03762
- **对着 Figure 2 写代码**(scaled dot-product + multi-head)
- 精读:§3.2 Attention, §3.3 Position-wise FFN
- 扫读:§3.4 Positional Encoding, §5 Training(细节为主)
- 跳过:§4 "Why Self-Attention", §6 实验结果(先不纠结)

## 加分(有时间再看)

### 4. Phuong & Hutter《Formal Algorithms for Transformers》
- arXiv: 2207.09238
- 读 §2-4 的伪代码 · 用它**验证你的维度流**对不对
- 好处:把 transformer 写成严谨的算法,读完你能按伪代码独立复现

### 5. Karpathy《Let's build GPT from scratch》
- YouTube 搜标题 · 约 2h
- 其实是 Week 2 的主材料,但 **attention 那段(视频 30-60 min)** 可以提前看
- 填「数学 → PyTorch 代码」的 gap

## 建议顺序

```
Illustrated Transformer (1h, 建直觉)
  ↓
MIT Lec 2 (1h, 形式化)
  ↓
打开 week1_mha.ipynb 开始写 single-head
  ↓
卡住时精读《Attention Is All You Need》§3
  ↓
写 multi-head + 验证等价性
  ↓
读 Formal Algorithms §2-4(验证维度流)
  ↓
填 notes/week1_reviewer.md 的 6 个审稿人问题
```

## 过关标准(对应 reviewer note 第 0 节)

- [ ] 不看参考,写出 single-head forward
- [ ] 能解释 `Q @ K^T / sqrt(d_k)` 每一维在表示什么
- [ ] 能手推 multi-head 的 reshape / transpose 维度流
- [ ] 能说明「独立多头」与「一次大投影再分头」的等价性
- [ ] 最小 sanity check(shape / causal / 概率和)全部通过
- [ ] 你的实现和 `nn.MultiheadAttention` 数值差 < 1e-5
