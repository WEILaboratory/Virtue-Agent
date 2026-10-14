<p align="right"><strong>English</strong> | <a href="./Purpose-IR.zh-CN.md">简体中文</a> · <a href="./README.md">Project Home</a></p>

# 🔒 Purpose IR

### Compiling user purpose into enforceable boundaries for reusable agent state

**Purpose IR: Compiling User Purpose for Privacy-Preserving Agent Workflow State**

[📄 Read the paper on arXiv](https://arxiv.org/abs/2610.16414) · [🏠 Virtue Agent home](./README.md)

> **Evidence authority is not release authority.**
>
> Being permitted to *read* information while solving a task does not mean an agent is authorized to *retain or propagate* that information through summaries, memories, or handoffs.

---

<p align="center"><img src="./assets/purpose-ir/privacy_gap.png" width="100%" alt="Purpose-to-state semantic gap in agent workflows"></p>
<p align="center"><em>Figure 1 · The user requests a Boolean reminder answer, but observation details can accidentally accumulate in reusable workflow state.</em></p>

## Overview

An agent that answers a user question may legitimately need rich task evidence. A notification, for example, may contain a person's name, phone number, location, time, and message body. If the user asks only **“Do I have a meeting reminder?”**, the authorized result may be no more than `meeting_reminder = true`.

But multi-step workflows often generate intermediate plans, summaries, handoffs, and persistent or session memories. These artifacts can preserve details that were useful as **evidence** but were never intended to become the user-authorized **result**. The issue is not necessarily an external attacker; ordinary workflow-state construction can inadvertently expand the lifetime and audience of sensitive information.

The paper calls this the **purpose-to-state semantic gap**: information the agent was permitted to observe is not automatically information it should release into reusable state.

## Research Scope

**Setting.** Purpose IR studies the transition from **task evidence** to **reusable workflow state** in agent pipelines, including plans, summaries, memory, and handoff artifacts. The primary risk is unintended semantic retention under ordinary task execution.

**What it addresses.** The work compiles user purpose into typed semantic dependencies and explicit output sinks, allowing only authorized results to populate downstream state. It evaluates privacy preservation together with task success and tests how compiler and runtime mechanisms contribute to the result.

**Boundaries.** Purpose IR focuses on a specified **semantic release boundary** in instrumented workflows. It does not claim to prevent all privacy harms, erase every existing copy of information, defend against every attacker, or address model-training data exposure. The result depends on accurately defining the authorized task purpose and routing state release through the proposed mechanism.

---

## Problem: Task-Relevant Evidence Can Exceed Task-Authorized Output

A useful distinction is:

- **Observation authority:** what information the workflow may inspect to solve the task.
- **Computation authority:** which derived values the task requires.
- **Release authority:** which outputs may enter reusable state or trigger a downstream action.

Traditional filtering approaches may redact sensitive strings, while task-relevance checks may preserve too much because the information was “related” to the task. Neither, on its own, specifies exactly **which semantic outputs the user authorized**.

Purpose IR instead treats the user purpose as the source of a **structured, executable release contract**.

## How Purpose IR Works

<p align="center"><img src="./assets/purpose-ir/architecture.svg" width="100%" alt="Purpose IR compile execute and release architecture"></p>
<p align="center"><em>Figure 2 · Compile user purpose, execute a typed dependency graph, and reconstruct state from authorized sinks only.</em></p>

### Stage 1 — Compile purpose

A task-only frontend examines the user's natural-language request and proposes required semantics **without reading the rich observation**. A deterministic compiler lowers these semantics into a typed acyclic **Purpose Program**:

```text
P = (V, E, S)
```

Here `V` denotes typed semantic nodes, `E` allowed dependencies, and `S` explicit release sinks. An LLM's proposed semantic requirements are **not themselves authorization**: the compiled program determines the permissible output structure.

### Stage 2 — Execute typed semantic dependencies

The graph uses three types of nodes:

| Node | Responsibility | Example |
| --- | --- | --- |
| **Leaf** | Bind the evidence specifically required by the purpose | `meeting_link`, `meeting_time` |
| **Derived** | Compute only from approved predecessor outputs | Compose `{link, time}` |
| **Sink** | Declare the value or action authorized for release | Send the record to the designated assistant |

A key data-flow invariant prevents derived and sink nodes from reopening raw observations. They consume only their typed predecessors. This narrows what task evidence can flow to later computations.

### Stage 3 — Release authorized state

The runtime validates sink types and provenance, discards non-sink intermediates, and builds **fresh reusable state from permitted sink outputs only**.

That distinction matters because deleting sensitive-looking tokens after generating a rich memory is not equivalent to **never releasing unauthorized semantics into that memory**.

## Main Experimental Findings

### 1. High privacy compliance with practical task completion

<p align="center"><img src="./assets/purpose-ir/privacy_task_frontier.svg" width="85%" alt="Privacy versus task success frontier"></p>
<p align="center"><em>Figure 3 · Main evaluation: the privacy–task-success trade-off across approaches.</em></p>

The reported evaluation covers **1,200 workflow cases** spanning LangGraph, CrewAI, and MobileRun.

| Metric | Purpose IR |
| --- | ---: |
| Privacy pass | **99.6%** |
| Task success | **83.3%** |
| Privacy-compliant success (PCS) | **83.3%** |

The strongest high-privacy baseline reported in the project page, **Fides**, has **55.8% PCS**. Purpose IR is **27.5 percentage points higher** in PCS in that comparison, while maintaining a high privacy pass rate.

Crucially, privacy protection need not consist of suppressing all useful data. It can instead mean releasing **exactly the semantics justified by the task**.

### 2. Evidence recovery and sink reconstruction are both necessary

<p align="center"><img src="./assets/purpose-ir/mechanism_attribution.svg" width="96%" alt="Purpose IR runtime and compiler ablations"></p>
<p align="center"><em>Figure 4 · Runtime mechanism ablations and semantic-contract construction experiments.</em></p>

| Runtime variant | Privacy | Task success | PCS |
| --- | ---: | ---: | ---: |
| **Full Purpose IR** | **99.6%** | **83.3%** | **83.3%** |
| Remove recovery binding | 99.8% | 13.5% | 13.5% |
| Remove sink-constrained reconstruction | 34.9% | 71.2% | 24.2% |

If the system cannot recover genuinely needed task evidence, privacy may remain high but utility collapses. If it does not constrain release to sink outputs, privacy can collapse instead. **Recovery of necessary evidence and minimization of released state are complementary mechanisms.**

The paper also examines the frontend and compiler: a stronger proposal plus deterministic lowering approach reaches **96.0%** semantic-contract correctness in the reported 300-instance setting, compared with **100%** for gold IR.

### 3. External transfer on PrivacyLens

<p align="center"><img src="./assets/purpose-ir/privacylens_transfer.svg" width="85%" alt="Purpose IR external transfer to PrivacyLens"></p>
<p align="center"><em>Figure 5 · Safe-action performance and helpfulness in an external evaluation.</em></p>

Across **493 PrivacyLens trajectories**, Purpose IR achieves a reported **77.3% Safe Action Rate**, **10.4 percentage points** above the second-best method in that comparison.

This metric concerns external safe-action behavior and helpfulness; it is **not the same metric** as reusable-state privacy pass or PCS in the principal workflow evaluation.

## Why It Matters

- **Memory is part of the privacy boundary.** Plans, summaries, memories, and handoffs may carry information into later contexts, agents, or audiences.
- **Task relevance is not equivalent to authorized retention.** A piece of information can be necessary to compute an answer without belonging in the answer or stored state.
- **Explicit, typed release contracts** create a more precise target than generic redaction or unconstrained summarization.
- **Agent safety includes information-flow behavior.** Preventing unintended persistence is a meaningful safety objective even in non-adversarial workflows.

## Takeaway

> **The authority to observe is not the authority to retain.**
>
> Purpose IR studies how to turn the user's intended result into a checkable boundary on which semantics may persist beyond the immediate task.


---

## Paper & Citation

**Paper:** Purpose IR: Compiling User Purpose for Privacy-Preserving Agent Workflow State

**Authors:** Dongsheng Chen, Xiangyu Zhao, Lei Ma, Xin Yao, Xuetao Wei  
**arXiv:** [2610.16414](https://arxiv.org/abs/2610.16414)

```bibtex
@misc{chen2026purposeir,
  title         = {Purpose IR: Compiling User Purpose for Privacy-Preserving Agent Workflow State},
  author        = {Dongsheng Chen and Xiangyu Zhao and Lei Ma and Xin Yao and Xuetao Wei},
  year          = {2026},
  eprint        = {2610.16414},
  archivePrefix = {arXiv},
  primaryClass  = {cs.CR}
}
```

---

## More Virtue Agent Projects

[“Allow” to Achieve](./Allow-to-Achieve.md) · [SameFact](./SameFact.md) · [Says Block, Still Acts](./Says-Block-Still-Acts.md) · [OpenAgentFlow](./OpenAgentFlow.md) · [Purpose IR](./Purpose-IR.md)

<p align="center"><a href="./README.md">← Back to Virtue Agent</a> · <a href="./Purpose-IR.zh-CN.md">简体中文</a></p>
