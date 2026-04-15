---
layout: page
title: Compositional Generalization in Mathematical Reasoning
description: Benchmarking LSTM and Transformer architectures on controlled arithmetic generalization tasks
img: /assets/img/generalization_gap.png
importance: 1
category: Research
github: https://github.com/bcode0127-debug/Compositional-Reasoning-Arithmetic.git
related_publications: false
---

Neural networks fail at compositional generalization 
in ways that are not obvious from training metrics. 
A model achieving 95% training accuracy can collapse 
to under 2% on structurally harder examples not 
because of overfitting, but because it never learned 
the underlying algorithm.

This study tests that failure systematically. LSTM 
and Transformer seq2seq architectures were trained 
on arithmetic expression evaluation tasks with 
strict out-of-distribution test splits — one testing 
length generalization, one testing depth generalization.

Both architectures failed to generalize. The 
generalization gap exceeded 78% across all conditions. 
Neither model learned compositional structure
both memorized surface patterns within their 
training distribution.

---

## Experimental Setup

**Dataset**: 20,000 controlled arithmetic expressions
- Operand range: 1-20, result magnitude ≤ 1000
- Operations: +, -, *, / (balanced)
- Format: fully parenthesized infix notation

**Models**:
- LSTM encoder-decoder (2.1M parameters, 
  hidden dim 256)
- Transformer encoder-decoder (5.5M parameters, 
  8 attention heads, 3 layers)

Both trained with teacher forcing, Adam optimizer, 
early stopping (patience=25).

---

## Results

**LSTM**

| Study | Train Acc | Val Acc | OOD Acc | Gap |
| :--- | :---: | :---: | :---: | :---: |
| Length (2-3 → 4-7 ops) | 95.1% | 38.2% | 1.8% | 93.3% |
| Depth (d=2 → d=3) | 89.2% | 15.9% | 10.4% | 78.8% |

{: .table .table-sm .table-borderless}
##

**Transformer**

| Study | Train Acc | Val Acc | OOD Acc | Gap |
| :--- | :---: | :---: | :---: | :---: |
| Length (2-3 → 4-7 ops) | 56.7% | 12.1% | 0.4% | 56.3% |
| Depth (d=2 → d=3) | 51.3% | 5.7% | 2.3% | 49.0% |

{: .table .table-sm .table-borderless}

---

## Key Findings

**LSTM outperforms Transformer on training distribution** 
(95.1% vs 56.7%) but both 
architectures exhibit catastrophic OOD failure, 
with generalization gaps exceeding 78% across 
all conditions.

**Transformer autoregressive generation is unstable** 
validation accuracy (6-12%) is significantly 
lower than teacher-forced training accuracy 
(51-57%), indicating compounding error accumulation 
during inference.

**Neither architecture learns compositional structure.** 
OOD accuracy across both models 
and both studies remains below 11%, confirming 
pattern memorization over algorithmic generalization.

---

## Discussion

The generalization gap is not a hyperparameter 
problem. Both architectures were tuned independently 
with appropriate learning rates and early stopping. 
The failure is architectural — current seq2seq 
models lack inductive biases necessary for 
compositional generalization in symbolic 
reasoning tasks.

## Ongoing Work

Attention analysis is currently underway to 
investigate whether Transformer attention heads 
develop structured representations of arithmetic 
operations, or whether attention patterns reflect 
the same surface-level memorization observed in 
the behavioral results.

Findings from this analysis, combined with the 
baseline results above, will be compiled into 
a full research paper targeting arXiv publication.
