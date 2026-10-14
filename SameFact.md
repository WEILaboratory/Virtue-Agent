<p align="right"><strong>English</strong> | <a href="./SameFact.zh-CN.md">简体中文</a> · <a href="./README.md">Project Home</a></p>

# 🧠 SameFact

### The same safety-relevant fact can lead to different responses across interfaces

**SameFact: The Same Safety Facts Lead to Different Responses Across Interfaces**

[📄 Read the paper on arXiv](https://arxiv.org/abs/2609.35872) · [🏠 Virtue Agent home](./README.md)

> **A model can recognize a safety-relevant fact without letting it influence action selection to the same degree.**
>
> SameFact directly measures whether the effect of an identical, controlled safety-fact change is preserved from explicit safety judgment to agent action.

---

<p align="center"><img src="./assets/samefact/overview.svg" width="100%" alt="SameFact benchmark overview"></p>
<p align="center"><em>Figure 1 · Hold the task, candidate action, and other facts fixed; change one safety fact; compare three response interfaces.</em></p>

## Overview

Safety benchmarks often ask whether a model can identify an unsafe action. Agent evaluation, however, often asks what that model will **do next**. These endpoints sound closely related, but using the former to predict the latter requires an empirical assumption: the same safety information must meaningfully shape both kinds of response.

SameFact examines that assumption without comparing unrelated tasks or unrelated benchmarks. Each matched pair describes the same task state and exact candidate action, with **one safety-relevant fact** changed between its safe and unsafe versions. The study then measures the effect of that change under three response interfaces at the same candidate-action boundary.

The result is not simply that action is “harder” than classification. Changes in interface can alter **the strength, relative ordering, and model dependence** of measured safety sensitivity.

## Research Scope

**Setting.** SameFact studies **candidate-anchored first responses** to matched safe/unsafe states. It compares explicit judgment, binary candidate admission, and open first-action selection; actions are recorded prior to tool execution.

**What the study addresses.** It measures whether one controlled factual intervention produces comparable changes across these interfaces, how the relative sensitivity of models and safety factors shifts, and which protocol features contribute to the difference.

**Boundaries.** The benchmark measures a specific response boundary, **not full multi-step agent trajectories**, downstream environmental harm, or the effectiveness of a deployed guardrail. Findings about measurement transfer should not be interpreted as universal safety guarantees.

---

## Benchmark: 300 Matched Counterfactual Pairs

SameFact contains **300 safe/unsafe pairs**. Each pair preserves the user task, prior observations, candidate action and tool call, entity identifiers, and non-target facts. Exactly one state-grounded safety fact is changed.

Four classes of safety factors are balanced across repository, data, and access-control contexts:

| Factor | Question tested |
| --- | --- |
| **Authorization** | Is the actor entitled to perform this action? |
| **Constraint** | Does a rule or policy permit or prohibit the action? |
| **Effect** | Does the object's condition or the action's consequence change the safety status? |
| **Scope / Consent** | Is the requested use, audience, access, or destination within the permitted boundary? |

The matched construction makes the **response to a single meaningful fact** the measurement target, rather than overall correctness on arbitrarily different scenarios.

## Three Response Interfaces

### J — Explicit safety judgment

The model directly answers whether the exact candidate should be **ALLOW**ed or **BLOCK**ed. This resembles many conventional safety-classification tests.

### D — Checkpoint candidate admission

The model uses an agent-like action format but chooses only between admitting the **exact candidate** and issuing **STOP**. This creates an intermediate checkpoint setting.

### E — Open first-action selection

The model may select the candidate, a different valid action, or STOP. This more closely resembles selecting a next action in an agent workflow. The measurement remains the model's **first response at the specified boundary**, not a completed trajectory.

**Safety sensitivity** is the change in the appropriate response probability between matched safe and unsafe states, oriented so larger positive values indicate a stronger response in the expected safety direction.

## Main Findings

### 1. Safety sensitivity attenuates from judgment to open action

<p align="center"><img src="./assets/samefact/channel_profiles.svg" width="96%" alt="Sensitivity under judgment checkpoint and open action by model"></p>
<p align="center"><em>Figure 2 · Aggregate sensitivity across J, D, and E for six LLM backbones.</em></p>

Across six evaluated backbones, aggregate sensitivity is lower under open first-action selection than under explicit judgment. For example:

- **Qwen:** J **73.2 pp** → D **68.0 pp** → E **42.0 pp**.
- **GLM:** J **58.8 pp** → D **36.1 pp** → E **1.7 pp**.

The same factual contrast can substantially alter the model's stated verdict while exerting far less influence over candidate action selection.

### 2. The change is structured, not a uniform score reduction

Across **24 model × factor cells**, Spearman agreement is **0.817** between J and D but only **0.470** between J and E. Pairwise ordering disagreement rises from **18.5%** to **32.6%**.

This matters because comparing models under different safety interfaces can change which model appears safer on which factor. A single scalar safety score can obscure these changes in the model–factor profile.

<p align="center"><img src="./assets/samefact/attenuation_locus.svg" width="90%" alt="Attenuation does not have one universal locus"></p>
<p align="center"><em>Additional analysis · Where sensitivity attenuates differs across backbones.</em></p>

### 3. Different safety factors behave differently across models

For the **Constraint** factor, open-action sensitivity remains relatively high for Qwen (**79.6 pp**), Mistral (**78.7 pp**), Gemma (**54.7 pp**), and MiniMax (**73.8 pp**), but falls to **5.8 pp** for DeepSeek and **4.4 pp** for GLM, despite high judgment sensitivity in those models.

This is not evidence that models universally ignore rules. It demonstrates that **the persistence of a safety factor's influence depends on both the factor and the backbone**.

### 4. The response protocol is itself part of what is measured

A follow-up **2 × 2 experiment** crosses checkpoint-style versus natural continuation framing with binary versus open action spaces.

<p align="center"><img src="./assets/samefact/factorial_interactions.svg" width="94%" alt="Two by two response profiles across backbones"></p>
<p align="center"><em>Figure 3 · The factorial response profile varies by model.</em></p>

Checkpoint-style framing increases measured sensitivity in **all six** backbones by **8.4–29.3 percentage points**. Effects of the available action set and its interaction with framing, however, vary by model in magnitude and direction.

<p align="center"><img src="./assets/samefact/factorial_effects.svg" width="94%" alt="Estimated framing choice-set and interaction effects"></p>
<p align="center"><em>Additional analysis · Estimated framing, choice-set, and interaction effects with confidence intervals.</em></p>

## Why It Matters

- An agent's **ability to say an action is unsafe** should not be treated as interchangeable with how much that same safety fact influences **what it chooses to do**.
- Evaluation protocols are not merely formatting details. They form part of the operational definition of the measured safety effect.
- Evaluating acting agents calls for **action-facing endpoints**, not only explicit risk judgments.

## Takeaway

> **Same safety fact. Different interface. Different response.**
>
> Shared safety information across interfaces does not guarantee equivalent measurements of safety-relevant behavior.


---

## Paper & Citation

**Paper:** SameFact: The Same Safety Facts Lead to Different Responses Across Interfaces

**Authors:** Dongsheng Chen, Jiaxin Zhang, Lei Ma, Xin Yao, Xuetao Wei  
**arXiv:** [2609.35872](https://arxiv.org/abs/2609.35872)

```bibtex
@misc{chen2026samefact,
  title        = {SameFact: The Same Safety Facts Lead to Different Responses Across Interfaces},
  author       = {Dongsheng Chen and Jiaxin Zhang and Lei Ma and Xin Yao and Xuetao Wei},
  year         = {2026},
  eprint       = {2609.35872},
  archivePrefix= {arXiv},
  primaryClass = {cs.CR}
}
```

---

## More Virtue Agent Projects

[“Allow” to Achieve](./Allow-to-Achieve.md) · [SameFact](./SameFact.md) · [Says Block, Still Acts](./Says-Block-Still-Acts.md) · [OpenAgentFlow](./OpenAgentFlow.md) · [Purpose IR](./Purpose-IR.md)

<p align="center"><a href="./README.md">← Back to Virtue Agent</a> · <a href="./SameFact.zh-CN.md">简体中文</a></p>
