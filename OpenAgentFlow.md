<p align="right"><strong>English</strong> | <a href="./OpenAgentFlow.zh-CN.md">简体中文</a> · <a href="./README.md">Project Home</a></p>

# 🛡️ OpenAgentFlow

### System-wide safety boundaries for heterogeneous AI agent fleets

**OpenAgentFlow: Enabling System-Wide Safety Boundaries for Heterogeneous AI Agent Fleets**

[📄 Read the paper on arXiv](https://arxiv.org/abs/2609.00015) · [🏠 Virtue Agent home](./README.md)

> **Govern the action, not just the agent.**
>
> OpenAgentFlow proposes a shared **action-commit safety boundary** for GUI interactions, API operations, tool calls, and model-generated invocations, so policies can be checked before pending actions change the external world.

---

<p align="center"><img src="./assets/openagentflow/action_flow_risk.svg" width="100%" alt="Cross-agent and cross-application data-flow risks"></p>
<p align="center"><em>Figure 1 · Individually ordinary steps can form a policy-violating information flow when composed across agents or applications.</em></p>

## Overview

Practical AI deployments combine planners, agents, tools, GUI controllers, API wrappers, and execution backends. One agent may retrieve sensitive information, another may analyze it, and a third may transmit a derived report. A safety check confined to one prompt or tool can miss a risk that exists only after several apparently legitimate actions are combined.

For example, one agent reads private payroll data, another incorporates it into a budget artifact, and another sends that artifact to an external party. A similar issue appears when a single agent transfers sensitive information among Contacts, Calendar, and Mail. The crucial security property is not merely whether each individual step looked reasonable, but **whether the entire information flow complies with the relevant policy**.

OpenAgentFlow approaches this from the system boundary rather than the model-internals perspective. It asks: **what can we enforce when an intended action is about to become a real state change?**

## Research Scope

**Setting.** The system targets heterogeneous agent channels that are **instrumented to route pending actions** through a shared enforcement boundary: GUI, API, tools, and LLM-generated calls. It considers session-level provenance and policy constraints, including unintended violations and selected adversarial or malicious-input scenarios.

**What the system addresses.** It standardizes actions into policy-evaluable events, runs rule and semantic checks prior to execution, maintains observed provenance across a session, supports dynamic policy updates, and makes enforcement decisions auditable.

**Boundaries.** Guarantees depend on **which execution channels have been intercepted, which policies have been specified, and which provenance can be reliably observed**. Uninstrumented actions, missing rules, and arbitrary previously unknown attacks are not automatically covered. The paper evaluates specific suites and execution settings rather than proving universal agent safety.

---

## The Core Idea: Enforce at Action Commit

<p align="center"><img src="./assets/openagentflow/system_pipeline.svg" width="100%" alt="OpenAgentFlow control plane and action plane architecture"></p>
<p align="center"><em>Figure 2 · Shared pre-execution PEP across heterogeneous channels, with a separate control plane for policy, provenance, rules, and auditing.</em></p>

An agent may reason and plan however it chooses, but its intended tool or GUI operation still reaches a point where it must be **committed to an executor**. OpenAgentFlow introduces a shared decision path at that point:

```text
Agent / Planner / Controller
             ↓
       Pending Action
             ↓
         AgentEvent
             ↓
    Policy Enforcement Point
             ↓
  Allow / Deny / Rewrite / Escalate
             ↓
       Actual Execution
```

The action-commit boundary separates **an agent proposing a state change** from **the system authorizing the state change**.

## Components

### 1. AgentEvent: a shared representation for heterogeneous actions

GUI actions, API calls, tool invocations, and model-produced commands differ in syntax. OpenAgentFlow normalizes their essential properties into an **AgentEvent** containing fields such as session and agent identity, channel, action type, target, payload, provenance, context, and time.

This allows policies to be written against **the meaning and context of a pending action**, not against one model's prompt format or one tool wrapper's implementation.

### 2. Policy Enforcement Point (PEP): the pre-execution decision

Pending events pass through a common PEP, where the decision can use:

- agent role and action type;
- target and destination;
- payload structure and sensitive values;
- active authorization and flow rules;
- provenance and earlier events in the session;
- additional semantic analysis when deterministic checks are insufficient.

The PEP then allows, denies, rewrites, or escalates a pending action under the relevant policy.

### 3. Control plane and action plane

The **action plane** sits on the execution path. It normalizes events and applies checks to pending actions. The **control plane**, managed separately from protected agents and executors, stores policies, session provenance, flow rules, audit records, and updates.

Separating these roles makes it possible to revise enforcement policies **without retraining a model or rewriting every agent prompt and executor**.

### 4. Enforcement-observed provenance

A single outgoing message or calendar write may look benign until the system knows **where its content came from**. OpenAgentFlow therefore maintains source-to-value bindings derived from events observed at the enforcement boundary, rather than trusting an agent's unverified claim about a value's origin.

This enables policies over information crossing from protected sources through intermediate artifacts into unauthorized destinations.

### 5. Staged enforcement

The T1–T4 path combines fast, structured checks with slower semantic review and escalation. The goal is to avoid sending every trivial policy decision to a general-purpose LLM while still supporting ambiguous, context-dependent cases.

## Evaluation and Results

The paper evaluates OpenAgentFlow through a **300-case controlled suite**, an independent **200-case threat suite**, a **1,220-case AgentDojo-Traj split of TS-Bench**, dynamic-policy tests, provenance checks, and traced Android executions.

### Controlled suite: action-policy enforcement

| Metric | Reported result |
| --- | ---: |
| Accuracy (300 cases) | **94.00%** |
| Attack Block Rate | **95.35%** |
| Cases resolved by T1–T2 fast path | **280 / 300 (93.33%)** |

In the evaluated implementation, T1 and T2 fast paths achieve reported P99 latencies of **0.0992 ms** and **0.4725 ms**, respectively. These numbers characterize the measured structured checks; they are not a universal end-to-end latency bound for every action.

### External benchmark: AgentDojo-Traj

On the **1,220-case** external evaluation:

| Metric | OpenAgentFlow |
| --- | ---: |
| Accuracy | **97.62%** |
| F1 | **95.91%** |
| Unsafe-action recall | **96.59%** |
| Safe false-intervention rate | **1.96%** |

A direct same-backbone use of Qwen3.8-27B, as reported in the source project page, yields **91.48%** accuracy and **11.41%** false intervention in this setting. The comparison highlights the practical value of organizing a model with deterministic rules, session state, and policy routing rather than relying on an unstructured model decision alone.

### Threat-oriented and system tests

- **200-case threat suite:** **191/200** pass; **95.50%** accuracy, **96.08%** attack block, **6.38%** false block.
- **Dynamic policy updates:** **27/30** cases behave as expected; the six core rule insertions take effect at T1 without modifying the protected agents.
- **Provenance trust boundary:** **19/20** cases pass, testing observed provenance against self-reported origin.
- **Real Android execution:** **92.86%** trace-adjusted accuracy across **98** tracked execution cases.

These experiments test complementary aspects of the proposed boundary. Their percentages use **different test suites and denominators**, and should not be conflated into one global safety rate.

## Why It Matters

The research reframes one part of agent safety as an **execution-system property**. Stronger model judgment, safety prompting, and alignment remain valuable, but heterogeneous fleets need a way to check actions that cross tool, application, and information-flow boundaries.

A shared action-commit path offers a place to implement independent policy checks, maintain cross-step context, update rules, and retain audit evidence. Such a boundary does not eliminate every agent risk; its value lies in making **specified constraints enforceable where instrumented actions become real effects**.

## Takeaway

> **Safety should not depend on every agent independently making the right decision.**
>
> OpenAgentFlow explores how to make action-level policy enforcement a shared infrastructure capability for heterogeneous agent systems.


---

## Paper & Citation

**Paper:** OpenAgentFlow: Enabling System-Wide Safety Boundaries for Heterogeneous AI Agent Fleets

**Authors:** Dongsheng Chen, Xiangyu Zhao, Xin Yao, Xuetao Wei  
**arXiv:** [2609.00015](https://arxiv.org/abs/2609.00015)

```bibtex
@misc{chen2026openagentflow,
  title        = {OpenAgentFlow: Enabling System-Wide Safety Boundaries for Heterogeneous AI Agent Fleets},
  author       = {Dongsheng Chen and Xiangyu Zhao and Xin Yao and Xuetao Wei},
  year         = {2026},
  eprint       = {2609.00015},
  archivePrefix= {arXiv},
  primaryClass = {cs.AI}
}
```

---

## More Virtue Agent Projects

[“Allow” to Achieve](./Allow-to-Achieve.md) · [SameFact](./SameFact.md) · [Says Block, Still Acts](./Says-Block-Still-Acts.md) · [OpenAgentFlow](./OpenAgentFlow.md) · [Purpose IR](./Purpose-IR.md)

<p align="center"><a href="./README.md">← Back to Virtue Agent</a> · <a href="./OpenAgentFlow.zh-CN.md">简体中文</a></p>
