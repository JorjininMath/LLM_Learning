# LLM 学习仓库

[English](README.md)

这是一个带有研究味道、强调亲手推导与实现的 LLM 学习仓库。目标不是只把现成流程跑通，而是尽可能从第一性原理理解大语言模型。

这个仓库围绕一个约束来设计：如果我不能自己重新推导一个核心概念，或者从零重写它的关键实现，我就不认为自己真的学会了。这里更关注机制、trade-off、失效模式和评估，而不是“demo 能不能跑”。

## 为什么做这个仓库

很多 LLM 学习材料都在优化速度：调一个库、微调一个小模型、看到结果就进入下一章。这当然有价值，但也会掩盖真正重要的问题：

- 为什么 multi-head attention 比单个更宽的 head 更有用？
- tokenization 改了以后，模型到底哪里变了？
- 模型行为有多少来自架构，有多少来自数据？
- post-training 到底改善了什么，又牺牲了什么？
- 除了 loss，我们还应该怎么评估模型？

这个仓库就是为了更严肃地研究这些问题。我会尽量把关键组件自己重建一遍，并在过程中留下带“审稿人视角”的笔记。

## 学习方法论

- Eval-first：每一次训练或对齐步骤，都应该有方法判断它是否真的有效。
- 学两遍：第一遍跟着高质量材料做，第二遍关掉参考从记忆里重写。
- 审稿人思维：每个方法都追问它依赖什么假设、会在哪里失效、还有什么替代设计。
- 深度优先：目标不是“都学过”，而是把少数重要系统真正理解透。
- Data matters：模型行为不只是架构问题，数据清洗、去重和任务 framing 同样重要。

## 学习路线

这个仓库目前沿着一条 text-first 的 LLM 路线推进：

1. Transformer 基础与手写 multi-head attention
2. GPT 风格模型实现与 scaling 直觉
3. Tokenizer 与 data-centric 分析
4. Pretraining 与 baseline evaluation
5. SFT 与手写 LoRA
6. DPO 与 post-training / alignment 机制
7. Evaluation、安全与 LLM-as-a-judge
8. RAG 与 inference system 的 trade-off

多模态目前不在主线内，只作为未来扩展单独跟踪。

## 仓库结构

| 路径 | 作用 |
| --- | --- |
| `00_Refreshers/` | 主线之外的背景补课与旁支笔记 |
| `02_Transformer/` | Attention 与 Transformer 基础 |
| `03_nanoGPT/` | GPT 风格实现与 scaling 实验 |
| `04_Tokenizer/` | BPE 与 tokenizer 相关实验 |
| `05_Pretraining/` | 训练循环、checkpoint 与评估基线 |
| `06_PostTraining_RLHF/` | SFT、LoRA、DPO 与 alignment 相关内容 |
| `07_RAG_Agent/` | Retrieval、prompting 与 agent 风格实验 |
| `notes/` | 每周笔记、反思与审稿人式批判 |
| `resources/` | 论文、链接、课程与辅助材料 |

很多模块都会有两次实现：

```text
0X_module/
├── foo_follow.py
└── foo_scratch.py
```

- `*_follow.py`：第一遍跟着书、课程或教程实现
- `*_scratch.py`：第二遍关掉参考，从记忆里重写，作为真正学会的证据

## 这个仓库不是什么

这不是一个打磨完成的框架，也不是标准 benchmark 套件。它更像一个公开的研究型学习笔记本：

- 有些模块会故意保持未完成状态
- 有些 notebook 只是脚手架，保留开放式 TODO
- 很多 notes 的目的不是给出最终答案，而是澄清思路
- 一些实现会优先可解释性，而不是工程上的完备性

如果你想找的是一个紧凑、可复现、一步跑通的 “LLM from scratch” 项目，别的仓库可能更合适。如果你想看一个人如何认真地学习这个领域、反复质疑假设、逐步建立直觉，这个仓库应该更有价值。

## 环境配置

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

如果主要用 notebook：

```bash
jupyter lab
```

## 建议阅读顺序

- 从 `02_Transformer/` 开始
- 配合 `notes/` 看每个主题对应的审稿人问题
- 用 `resources/links.md` 查论文、课程与补充材料

## 参考资料

核心参考与阅读清单见 `resources/links.md`。
