<h1 align="center">Virtue Agent</h1>
<p align="center"><strong>理解、评测与约束 AI Agent 的不安全行为</strong></p>
<p align="center"><em>Understanding, Evaluating, and Enforcing AI Agent Safety</em></p>

<p align="center"><a href="./README.md">English</a> · <strong>简体中文</strong></p>

---

## 关于 Virtue Agent

**Virtue Agent** 是一个长期的 AI Agent Safety 研究项目，关注能够调用工具、访问数据、操作环境和自主完成任务的智能体，在**决策、执行与信息处理**过程中可能出现的不安全行为。

我们不仅关心 Agent 能否识别安全风险，也关心这些判断能否影响真实行动，以及能否在执行和信息释放的关键边界实施有效约束。

目前的五篇研究工作归纳为三个相互衔接的方向：**Evaluate（评测） → Understand（机制） → Enforce（约束）**。这是组织当前研究的主线，而不是对未来工作或整个 AI Safety 领域的穷尽分类。每篇论文的具体研究对象、适用场景与结论边界，见独立项目页中的 **Research Scope**。

## 三个研究方向 | Research Directions

| 方向 | 研究问题 | 对应论文 |
| --- | --- | --- |
| **01 · Evaluate — 安全行为评测** | 如何衡量真实 Agent 的授权决策与行动安全，而不仅是任务成功率或口头安全判断？ | [“Allow” to Achieve](./Allow-to-Achieve.zh-CN.md) · [SameFact](./SameFact.zh-CN.md) |
| **02 · Understand — 安全失效机制** | Agent 为什么能够识别风险，却仍然可能偏好执行不安全动作？ | [Says Block, Still Acts](./Says-Block-Still-Acts.zh-CN.md) |
| **03 · Enforce — 安全约束与治理** | 如何在动作提交和信息释放等关键边界实施可执行的约束？ | [OpenAgentFlow](./OpenAgentFlow.zh-CN.md) · [Purpose IR](./Purpose-IR.zh-CN.md) |

---

## 研究项目 | Research Projects

### 01 · Evaluate — 安全行为评测

#### “Allow” to Achieve — 移动 GUI Agent 的权限弹窗决策

> **任务完成，不意味着授权合理。**

研究手机 GUI Agent 是否会为推进正常用户任务而授予不必要的应用权限，关注 Permission Literacy、App-Trust Bias、Task-Prior Override，以及提示式缓解方案在安全和可用性之间的权衡。

<p align="center"><img src="./assets/allow-to-achieve/permission_example.png" width="92%" alt="移动 GUI 权限误授案例"></p>

[**中文项目页 →**](./Allow-to-Achieve.zh-CN.md) · [论文 / arXiv](https://arxiv.org/abs/2608.04755)

#### SameFact — 同一安全事实，不同响应界面

> **会判断风险，不意味着同一事实能够同样影响行动。**

使用 300 对匹配的 safe/unsafe 反事实场景，对照安全判断、执行前候选动作检查与开放式首动作选择，探究安全敏感度如何跨界面变化。

<p align="center"><img src="./assets/samefact/overview.svg" width="92%" alt="SameFact benchmark overview"></p>

[**中文项目页 →**](./SameFact.zh-CN.md) · [论文 / arXiv](https://arxiv.org/abs/2609.35872)

### 02 · Understand — 安全失效机制

#### Says Block, Still Acts — 安全判断为何无法控制行动

> **模型可以回答 BLOCK，却仍偏好执行目标动作。**

通过跨界面 probe、内部因果干预与控制子空间分析，区分“安全信息仍可读取”与“该信息能否真正控制行动”这两个不同的问题。

<p align="center"><img src="./assets/says-block-still-acts/overview.svg" width="92%" alt="Judgment-action disconnect"></p>

[**中文项目页 →**](./Says-Block-Still-Acts.zh-CN.md) · [论文 / arXiv](https://arxiv.org/abs/2609.35870)

### 03 · Enforce — 安全约束与治理

#### OpenAgentFlow — 异构 Agent 的执行前安全边界

> **Govern the action, not just the agent.**

通过统一 AgentEvent 与共享的 Policy Enforcement Point（PEP），在 GUI、API、Tool 等不同执行通道真正改变外部状态之前进行策略检查，支持 session-level provenance、规则更新与审计。

<p align="center"><img src="./assets/openagentflow/system_pipeline.svg" width="92%" alt="OpenAgentFlow architecture"></p>

[**中文项目页 →**](./OpenAgentFlow.zh-CN.md) · [论文 / arXiv](https://arxiv.org/abs/2609.00015)

#### Purpose IR — 用户目的约束下的信息释放与状态留存

> **允许 Agent 读取证据，不代表允许其保留全部证据。**

将用户目的编译为有类型的语义依赖图，通过受控的 sink 输出重建 Agent 可复用状态，减少任务观测中非必要信息向计划、摘要、记忆与 handoff 的传播。

<p align="center"><img src="./assets/purpose-ir/architecture.svg" width="92%" alt="Purpose IR architecture"></p>

[**中文项目页 →**](./Purpose-IR.zh-CN.md) · [论文 / arXiv](https://arxiv.org/abs/2610.16414)

---

## 论文列表 | Publications

| 工作 | arXiv | 中文项目页 |
| --- | --- | --- |
| “Allow” to Achieve | [2608.04755](https://arxiv.org/abs/2608.04755) | [查看](./Allow-to-Achieve.zh-CN.md) |
| SameFact | [2609.35872](https://arxiv.org/abs/2609.35872) | [查看](./SameFact.zh-CN.md) |
| Says Block, Still Acts | [2609.35870](https://arxiv.org/abs/2609.35870) | [查看](./Says-Block-Still-Acts.zh-CN.md) |
| OpenAgentFlow | [2609.00015](https://arxiv.org/abs/2609.00015) | [查看](./OpenAgentFlow.zh-CN.md) |
| Purpose IR | [2610.16414](https://arxiv.org/abs/2610.16414) | [查看](./Purpose-IR.zh-CN.md) |

## 仓库说明

这是**研究工作宣传与介绍仓库**，展示论文、研究问题、主要方法、结果及相关图示。**本仓库暂不公开研究代码或实验数据**。各项性能指标仅对应各自论文中的具体评测条件，不代表五项工作之间可以直接进行分数比较。

<p align="center"><a href="./README.md">Read in English</a> · Virtue Agent</p>
