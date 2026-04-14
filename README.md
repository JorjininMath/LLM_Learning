# LLM Learning Repo

[中文说明](README_zh.md)

A research-flavored, hands-on journey to understand large language models from first principles rather than only running prebuilt stacks.

This repository is designed around one constraint: if I cannot re-derive or re-implement a core idea myself, I do not count it as learned. The emphasis is on mechanism, trade-offs, failure modes, and evaluation, not just "getting a demo to work."

## Why This Repo Exists

Most LLM learning resources optimize for speed: call a library, fine-tune a small model, and move on. That is useful, but it can hide the actual questions that matter:

- Why does multi-head attention help beyond a wider single head?
- What exactly changes when tokenization changes?
- How much of model behavior comes from architecture, and how much from data?
- What does post-training improve, and what does it sacrifice?
- How should we evaluate models beyond loss alone?

This repo is my attempt to study those questions in a more rigorous way, with a bias toward rebuilding key components and writing down reviewer-style notes along the way.

## Learning Philosophy

- Eval-first. Every training or alignment step should come with a way to tell whether it actually helped.
- Rebuild twice. First follow a strong reference, then rewrite the core idea from memory.
- Reviewer mindset. For every method, ask what assumptions it makes, where it breaks, and what alternative design choices exist.
- Depth over breadth. The goal is not to "cover everything," but to deeply understand a few important systems.
- Data matters. Model behavior is not just architecture; curation, deduplication, and task framing matter too.

## Study Roadmap

The repository follows a text-first LLM path:

1. Transformer fundamentals and hand-written multi-head attention
2. GPT-style model building and scaling intuition
3. Tokenization and data-centric analysis
4. Pretraining with baseline evaluation
5. SFT and hand-written LoRA
6. DPO and post-training / alignment mechanics
7. Evaluation, safety, and LLM-as-a-judge workflows
8. RAG and inference-system trade-offs

Multimodal topics are intentionally out of scope for the main path and are tracked separately as future extensions.

## How The Repo Is Structured

| Path | Role |
| --- | --- |
| `00_Refreshers/` | Background notes for side topics and prerequisite gaps |
| `02_Transformer/` | Attention and transformer fundamentals |
| `03_nanoGPT/` | GPT-style implementation and scaling experiments |
| `04_Tokenizer/` | BPE and tokenizer-related experiments |
| `05_Pretraining/` | Training loops, checkpoints, and evaluation baselines |
| `06_PostTraining_RLHF/` | SFT, LoRA, DPO, and alignment-related work |
| `07_RAG_Agent/` | Retrieval, prompting, and agent-style experiments |
| `notes/` | Weekly notes, reflections, and reviewer-style critiques |
| `resources/` | Papers, links, lectures, and supporting references |

Many modules are intended to have two passes:

```text
0X_module/
├── foo_follow.py
└── foo_scratch.py
```

- `*_follow.py`: first pass while following a book, lecture, or tutorial
- `*_scratch.py`: clean reimplementation from memory as evidence of actual understanding

## What To Expect

This is not a polished framework or benchmark suite. It is closer to a public research notebook:

- some modules are incomplete by design
- some notebooks are scaffolds with open TODOs
- many notes are written to clarify thinking rather than present final answers
- implementations may prioritize interpretability over engineering completeness

If you are looking for a compact, reproducible "LLM from scratch" codebase, there are better repos. If you are interested in how one person studies the field seriously, questions assumptions, and incrementally builds intuition, this repo should be more useful.

## Setup

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

For notebook-based work:

```bash
jupyter lab
```

## Suggested Reading Path

- Start with `02_Transformer/`
- Use `notes/` to see the reviewer-style questions attached to each topic
- Use `resources/links.md` for papers, lectures, and supporting material

## References

Core references and reading lists live in `resources/links.md`.
