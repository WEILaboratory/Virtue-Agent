<p align="right"><strong>English</strong> | <a href="./Says-Block-Still-Acts.zh-CN.md">简体中文</a> · <a href="./README.md">Project Home</a></p>

# ⚠️ Says Block, Still Acts

### Why can a correct safety judgment fail to control the next action?

**Says Block, Still Acts: Why LLM Safety Judgments Fail to Govern Action in LLM Agents**

[📄 Read the paper on arXiv](https://arxiv.org/abs/2609.35870) · [🏠 Virtue Agent home](./README.md)

> **An agent can say “BLOCK” and still prefer the blocked action.**
>
> We investigate a mechanistic explanation: safety information may remain available, yet the computations supporting an explicit safety judgment may have limited **causal authority** over action selection.

---

<p align="center"><img src="./assets/says-block-still-acts/overview.svg" width="100%" alt="Safety judgment and agent action causal disconnect"></p>
<p align="center"><em>Figure 1 · The judgment–action gap, cross-interface readability, asymmetric causal control, and control-subspace structure.</em></p>

## Overview

It is tempting to assume that safe agent behavior follows naturally from good safety judgments:

**recognize danger → say BLOCK → do not execute the unsafe action**.

However, these stages are not necessarily governed by the same internal computation. A model may correctly identify a blocked-but-executable tool call when asked to classify it, yet still prefer that call when asked to choose the next action.

There are at least two possible explanations. Perhaps the relevant safety information **disappears** during action selection. Alternatively, the information may **remain present** but fail to exert meaningful causal control over the behavior. Distinguishing these possibilities matters for evaluating self-checks, safety probes, and proposed steering mechanisms.

## Research Scope

**Setting.** The work examines the mismatch between explicit safety judgments and action preferences in controlled **blocked-but-executable tool-use** scenarios. It uses open-weight models whose hidden states can be probed and intervened upon. The primary focus is the model's own non-adversarial decision process rather than an external attacker's prompt-injection strategy.

**What the study addresses.** It tests whether safety-predictive representations remain readable in action states and whether judgment-derived controllers transfer causally to action selection. It also analyzes the overlap between the internal subspaces controlling the two endpoints.

**Boundaries.** Conclusions concern the studied models, controlled settings, and intervention endpoints. They do not identify a single universal circuit for every LLM, establish a deployable safety guard, or prove the safety of complete agent trajectories.

---

## Experimental Setup

The same underlying policy-constrained candidate action appears under two conditions:

- **Judgment interface:** Should the candidate be **ALLOW**ed or **BLOCK**ed?
- **Action interface:** Which available tool call should the model select next?

“Blocked but executable” is essential: the action is syntactically and operationally available, but should not be selected under the given policy or safety condition. Thus, the study distinguishes **not being able to act** from **being able but not being authorized to act**.

The experiments cover repository, data, and access scenarios across multiple policy and authorization families. Hidden-state analyses are performed with **Qwen3-32B**, **Gemma3-27B-IT**, and **Mistral-Small-3.2-24B-Instruct**.

## Three Mechanistic Tests

### 1. Readability: is the safety signal still present?

A linear probe trained to identify safety information in **judgment hidden states** is transferred to **action hidden states** without refitting. Lexical shortcuts and held-out semantic families are considered so that the probe result is not simply driven by surface words such as ALLOW or BLOCK.

### 2. Causal control: does changing judgment also change action?

We compare **judgment-native controllers**, which strongly alter the judgment endpoint, against **action-native controllers**, which directly influence action preference. The key test is whether a controller effective for “say BLOCK” also makes the model less likely to choose the unsafe action.

### 3. Control subspaces: do the two endpoints share their governing directions?

Low-rank judgment-control and action-control subspaces are constructed and compared. This separates geometric overlap from the stronger question of whether useful action-control capability remains **outside** the judgment-control space.

## Key Findings

### 1. Safety information survives the transition to action

<p align="center"><img src="./assets/says-block-still-acts/information_survives.svg" width="96%" alt="Cross-interface safety information readability"></p>
<p align="center"><em>Figure 2 · A judgment-trained safety probe remains informative on action states.</em></p>

Judgment-trained probes applied directly to action states achieve leave-family-out J→A AUROC values as high as:

- **Qwen:** 0.875
- **Gemma:** 0.840
- **Mistral:** 0.756

These findings argue against a simple story in which safety information has vanished by the time the model selects an action. **The information is readable; its causal role is the question.**

### 2. Strong judgment control does not translate into strong action control

<p align="center"><img src="./assets/says-block-still-acts/judgment_action_control.svg" width="96%" alt="Judgment-control and action-control interventions"></p>
<p align="center"><em>Figure 3 · Judgment-derived and action-native controllers have markedly different effects on action preferences.</em></p>

Judgment-derived controllers can have a large and consistent effect on the explicit BLOCK–ALLOW judgment. Applied to action selection, however, their effect stays below **11%** of the action-native controller's effect throughout the tested intervention-strength sweep.

At a matched intervention strength, their relative action-control efficiencies are:

| Model | Judgment-derived effect relative to action-native effect |
| --- | ---: |
| Qwen | **2.1%** |
| Gemma | **3.6%** |
| Mistral | **1.0%** |

The central distinction is between **making a safety judgment more likely** and **making the corresponding safe behavior more likely**.

### 3. The gap survives joint judgment–action trajectories

The paper also places judgment and action inside a **single continuation trajectory**. Even when an explicit BLOCK verdict is fixed before the action endpoint, interventions that strongly move the upstream judgment computation have little effect on downstream action preference. In contrast, action-native interventions can substantially change that preference.

This indicates that the disconnect is not adequately explained only by presenting two different prompts.

### 4. Naturally induced state changes show the same separation

Matched policy counterfactuals produce changes in the model's hidden judgment state without relying solely on a constructed gradient direction. The downstream action effect remains small in the tested conditions, supporting the broader causal interpretation.

### 5. Action control is not confined to the judgment-control space

<p align="center"><img src="./assets/says-block-still-acts/shared_capacity.svg" width="96%" alt="Shared capability and distinct causal effects"></p>
<p align="center"><em>Figure 4 · Control capacity can be shared while causal effectiveness remains distinct.</em></p>

Judgment and action spaces show some relationship, but large parts of effective action control remain orthogonal to the judgment-control subspace. In one representative Qwen L32 analysis, the reported rank-16 overlap is **2.50%**, with a much larger measured action effect in the judgment-orthogonal component than in the judgment-parallel component.

**Information overlap is not equivalent to control equivalence.**

## Why It Matters

- **Better safety classification is not automatically better action safety.** A system that produces a reliable BLOCK verdict still needs evidence that the verdict constrains execution.
- **Self-reflection and safety prompts are not guarantees.** Their action-level impact must be measured.
- **Safety mechanisms should be evaluated causally.** Decodability, intervention effects, and actual action behavior are distinct research endpoints.

## Takeaway

> **Knowing is not governing.**
>
> In the studied models and settings, safety-relevant information survives into action states, but the mechanisms governing explicit safety judgment exert surprisingly little control over action selection.


---

## Paper & Citation

**Paper:** Says Block, Still Acts: Why LLM Safety Judgments Fail to Govern Action in LLM Agents

**Authors:** Dongsheng Chen, Xiangyu Zhao, Xin Yao, Xuetao Wei  
**arXiv:** [2609.35870](https://arxiv.org/abs/2609.35870)

```bibtex
@misc{chen2026saysblock,
  title        = {Says Block, Still Acts: Why LLM Safety Judgments Fail to Govern Action in LLM Agents},
  author       = {Dongsheng Chen and Xiangyu Zhao and Xin Yao and Xuetao Wei},
  year         = {2026},
  eprint       = {2609.35870},
  archivePrefix= {arXiv},
  primaryClass = {cs.CR}
}
```

---

## More Virtue Agent Projects

[“Allow” to Achieve](./Allow-to-Achieve.md) · [SameFact](./SameFact.md) · [Says Block, Still Acts](./Says-Block-Still-Acts.md) · [OpenAgentFlow](./OpenAgentFlow.md) · [Purpose IR](./Purpose-IR.md)

<p align="center"><a href="./README.md">← Back to Virtue Agent</a> · <a href="./Says-Block-Still-Acts.zh-CN.md">简体中文</a></p>
