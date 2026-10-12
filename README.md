# Virtue Agent

### 从“知道什么是安全的”，走向“真正安全地行动”

**Virtue Agent** 是一组围绕 AI Agent 安全展开的研究工作。我们关注一个核心问题：

> 当大语言模型从“回答问题”走向“执行动作”之后，已有的安全知识、判断与约束，是否真的能够转化为可靠的安全行为？

当前项目围绕三条相互衔接的研究主线展开：**Measure → Understand → Govern**。

---

## 🧭 Research Roadmap

| 方向 | 工作 | 核心问题 |
| --- | --- | --- |
| **Measure** | [SameFact](./SameFact.md) | 同一条安全事实，在不同响应界面中是否会产生一致影响？ |
| **Understand** | [Says Block, Still Acts](./Says-Block-Still-Acts.md) | 为什么模型明明判断“应该阻止”，却仍然偏好执行？ |
| **Govern** | [OpenAgentFlow](./OpenAgentFlow.md) | 如何在动作真正执行之前建立统一、系统级的安全边界？ |

---

## 🧠 SameFact

### 同一安全事实，为何在不同响应界面下产生不同响应？

**SameFact: The Same Safety Facts Lead to Different Responses Across Interfaces**

SameFact 研究安全评测中的一个基础问题：**模型在显式安全判断中对风险高度敏感，是否意味着同样的安全事实也会稳定影响它的真实行动选择？**

<p align="center">
  <img src="./assets/samefact/overview.svg" width="96%" alt="SameFact overview">
</p>

通过 300 对严格匹配的 safe / unsafe 反事实样本，SameFact 在同一个候选动作边界上比较 **Judgment、Checkpoint Admission 与 Open First-Action Selection**，揭示安全事实从“判断”走向“行动”时并不会简单、稳定地保持其影响。

**[→ 查看 SameFact 完整项目页](./SameFact.md)** · **[arXiv](https://arxiv.org/abs/2609.35872)**

---

## ⚠️ Says Block, Still Acts

### 模型明明说“BLOCK”，为什么仍然会选择行动？

**Says Block, Still Acts: Why LLM Safety Judgments Fail to Govern Action in LLM Agents**

这项工作进一步追问 SameFact 背后的机制问题：如果模型能够正确识别风险，那么为什么安全判断仍然无法稳定约束行动？

<p align="center">
  <img src="./assets/says-block-still-acts/overview.svg" width="96%" alt="Says Block, Still Acts overview">
</p>

我们的分析区分了两个概念：**安全信息是否存在（information availability）**，以及这些信息是否真正拥有改变行动的**因果控制力（causal authority）**。结果显示，模型可以保留可读取的安全信息，同时让支持安全判断的控制方向对实际行动几乎不起作用。

**[→ 查看 Says Block, Still Acts 完整项目页](./Says-Block-Still-Acts.md)** · **[arXiv](https://arxiv.org/abs/2609.35870)**

---

## 🛡️ OpenAgentFlow

### 为异构 AI Agent 建立系统级、可执行的统一安全边界

**OpenAgentFlow: Enabling System-Wide Safety Boundaries for Heterogeneous AI Agent Fleets**

如果安全判断本身不能被默认视为行动保证，那么还需要一个系统层面的安全边界。OpenAgentFlow 将治理位置放在 Agent 真正改变外部环境之前的 **action-commit boundary**。

<p align="center">
  <img src="./assets/openagentflow/system_pipeline.svg" width="96%" alt="OpenAgentFlow system pipeline">
</p>

OpenAgentFlow 将 GUI、API、工具调用与 LLM-generated invocation 统一为可治理的 `AgentEvent`，并通过共享的 **Policy Enforcement Point (PEP)** 在执行前进行规则、provenance、语义与会话级检查。

**[→ 查看 OpenAgentFlow 完整项目页](./OpenAgentFlow.md)** · **[arXiv](https://arxiv.org/abs/2609.00015)**

---

## 🔗 Three Works, One Question

三项工作共同围绕同一个问题展开：

> **我们如何让 AI Agent 不只是“知道什么是安全的”，而是真正“安全地行动”？**

- **SameFact**：测量安全事实能否从判断迁移到行动；
- **Says Block, Still Acts**：解释为什么安全判断可能缺乏行动控制力；
- **OpenAgentFlow**：在系统执行层建立统一、可审计的安全治理边界。

---

## 📌 Repository Status

本仓库当前作为 **Virtue Agent** 系列工作的研究展示与项目主页，集中介绍论文问题、方法、关键发现与系统设计。

**当前仓库不包含研究代码。**

---

<p align="center">
  <b>Virtue Agent · From knowing what is safe to acting safely.</b>
</p>
