---
title: "Agent 架构每日摘要 — 2026年6月22日"
description: "Databricks 开源 Omnigent 元框架，MCP 移交 Linux Foundation，/goal 验证器循环成为标准原语，SING 用意图图解决规模化工具发现，组合式技能路由正式化分解-检索-组合，PreAct 为 CUA 缓存重复任务，VISUALSKILL 引入多模态技能，PRPO 追求帕累托最优工具效率"
pubDate: "2026-06-22"
lang: zh
tags: ["Agent", "LLM", "AI架构", "每日摘要"]
---

## TL;DR / 今日概览

> 今天最值得关注的 10 件事：

1. **Databricks 开源 Omnigent —— 凌驾于所有编程 Agent 之上的元框架**：你不再需要在 Claude Code、Codex、Cursor 或自定义 Agent 之间二选一。Omnigent 位于它们之上，统一编排、治理、沙箱化和切换，策略不再因模型更换而作废。—— [@Dinosn](https://x.com/Dinosn/status/2067549695732293775)

2. **SING：意图图解决规模化工具发现瓶颈**：当 Agent 工具库增长到数百个 API 时，找到正确工具本身就成了瓶颈。SING 构建意图→工具图，按任务状态动态检索，召回率提升 59.8%，同时将工具 Schema 上下文暴露减少 99.8%。—— [arXiv:2606.16591](https://arxiv.org/abs/2606.16591v2)

3. **组合式技能路由正式化「分解-检索-组合」范式**：真实任务需要多个技能的组合，而非单一技能选择。本文将这一问题类正式化，并定义可扩展 Agent 架构所必需的执行计划组合模式。—— [arXiv:2606.18051](https://arxiv.org/abs/2606.18051v1)

4. **MCP 移交 Linux Foundation —— 走向厂商中立治理**：Anthropic 将模型上下文协议移交至 Agentic AI Foundation（AAIF），与 Block 共同在 Linux Foundation 下创立。MCP 工具和服务器将跨所有厂商互通，不再锁定于 Anthropic 路线图。—— [@AnthropicAI](https://x.com/AnthropicAI/status/1998437922849350141)

5. **`/goal` 验证器循环已成为标准 Agentic 编程原语**：Codex 和 Claude Code 均已发布 `/goal` 命令，运行自主循环直到小型验证器通过。由验证门控的自主迭代不再是实验——它已被产品化。—— [@sachinrekhi](https://x.com/sachinrekhi/status/2064013928892645786)

6. **PreAct：计算机使用 Agent 在重复任务上更快**：当前 CUA 即使对做过多次的任务也要重新读取屏幕、重新推理。PreAct 缓存交互轨迹并在相似任务重现时回放，大幅摊薄重复工作流的成本。—— [arXiv:2606.17929](https://arxiv.org/abs/2606.17929v1)

7. **PRPO：追求帕累托最优的工具使用 Agent**：现有 RL 只最大化任务准确率而忽视工具效率。PRPO 用基于支配关系的优势估计同时在两个维度上对齐 Agent——直接解决每个生产级 Agent 都会遇到的准确率与成本权衡。—— [arXiv:2606.16111](https://arxiv.org/abs/2606.16111v1)

8. **VISUALSKILL：纯文本技能不足以支撑 GUI Agent**：计算机使用 Agent 在纯文本技能下会丢失信息。VISUALSKILL 用多模态制品（截图、视觉标注）表示技能，在未见过的软件上提升长程任务表现。—— [arXiv:2606.18448](https://arxiv.org/abs/2606.18448v1)

9. **将搜索与推理解耦 —— 厂商中立的 Grounding 架构**：原生搜索 Grounding 将检索策略、提供商、成本和延迟捆绑在一个模型边界之后。本文将它们分离，使 Grounding 可审计、可跨厂商移植。—— [arXiv:2606.18947](https://arxiv.org/abs/2606.18947v1)

10. **Hermes 发布 MCP 工具搜索功能**：Hermes Agent 不再将所有 MCP 工具堆入上下文，而是按任务按需发现相关工具——减少上下文膨胀，提升选择准确性。工具库规模化时的关键模式。—— [@AgenticAIFdn](https://x.com/AgenticAIFdn/article/2061490253559689322)

📊 今日数据：**X 精选 16 条 | 详细论文 10 篇 | 值得关注 27 条 | 中文社区 8 条 | 共计 61 条**

---

## 今日趋势：Agent 栈正在分层堆肥

今天全量采集中最强的信号不是单个发布——而是 Agent 生态系统正在快速分层为不同架构层级，每层都有自己的设计问题：

- **元框架层**（Omnigent）位于各个编程 Agent 之上，使「选 Claude Code 还是 Codex 还是 Cursor」成为可替换的实现细节，而非架构层面的承诺。
- **技能/工具层**（SING、组合式技能路由、VISUALSKILL、Hermes 工具搜索）正在正式化 Agent 如何发现、选择、组合和表示工具——从「把所有东西塞进上下文」转向意图图驱动的检索。
- **协议层**（MCP 纳入 Linux Foundation 治理）正在成为厂商中立基础设施，就像 HTTP 之于 Web。
- **效率层**（PRPO、PreAct）正在浮现，因为仅靠准确率已经不够——工具调用效率、延迟和重复任务成本现在是一等优化目标。

这就是 Agent 操作系统在逐步成型。模型仍然是引擎，但架构上有意思的工作已经上移到了栈的更高处。

---

## 公司动态

### Databricks：Omnigent —— 面向所有编程 Agent 的元框架

本周架构意义上最重要的发布。[Omnigent](https://x.com/Dinosn/status/2067549695732293775) 是一个开源元框架，在单一治理层下编排 Claude Code、Codex、Cursor、Pi 和自定义 Agent。你可以不重写编排代码就切换底层框架，统一执行策略和沙箱化，跨团队共享 Agent 配置。Databricks 指出的关键权衡：在不同模型后端之间移交时上下文缓存会丢失——所以跨框架组合并非没有成本。但价值主张是真实的：Agent 锁定是部署风险，位于各个框架之上的抽象层意味着你的策略、评估和可观测性能在模型切换中存活。([Databricks 博客, 6月13日](https://www.databricks.com/blog))

### Anthropic：MCP 移交 Linux Foundation

Anthropic [将模型上下文协议移交](https://x.com/AnthropicAI/status/1998437922849350141)至 Agentic AI Foundation（AAIF）——Linux Foundation 下由 Block 共同创立的定向基金。仅一年时间，MCP 就从 Anthropic 内部规范成长为 Agent 生态的默认工具访问协议。Linux Foundation 治理意味着 MCP 服务器和工具将跨所有模型厂商（Google、OpenAI、Mistral 以及中国实验室）互通，而非锁定于 Anthropic 的路线图。据报道 Q3 2026 有 MCP/A2A 联合规范工作推进中。

### Anthropic：Agent SDK 计费拆分（6月15日）

[Anthropic 将](https://x.com/unicity_labs/article/2066447178516611244) Agent SDK 计费与交互式 Claude.ai 使用分离。编程式 Agent 使用现在按标准 API 费率计费，而非订阅费率。这改变了在 Claude 上构建 Agent 的单位经济学：大规模运行 Agent 时，成本建模从「订阅席位」转向「按 Token 定价」。对于决定是基于 Agent SDK 还是直接调用 API 的团队，这是关键参考。

### Linear：由 Claude Code 和 Codex 驱动的 Linear Agent

[Linear 发布了 Linear Agent](https://x.com/linear/status/2065143120468017326)，将 Agentic 编程集成到项目管理流程中。Claude Code 和 Codex 是初始后端，后续会有更多。作为信号这很重要：主流项目管理工具正在嵌入编程 Agent，将 Agent 从开发者专用工具推向日常工作流。当你的 issue 追踪器可以直接编写和审查代码时，规划与执行之间的反馈循环会显著收紧。

### Nous Research：Hermes Agent 发布 MCP 工具搜索

[Nous Research 为 Hermes Agent 添加了工具搜索](https://x.com/AgenticAIFdn/article/2061490253559689322)——Agent 不再预先将所有可用 MCP 工具加载到上下文中，而是按任务按需发现相关工具。这正是下文 SING 论文在研究中正式化的相同架构模式：随着工具库规模化，动态工具检索优于静态上下文加载。减少上下文膨胀同时提升工具选择准确性和推理质量。

### Supabase：MCP Server + Agent 技能插件

[Supabase 发布了一个插件](https://x.com/supabase/article/2063245852026777754)，将其 MCP 服务器与 Agent 技能捆绑——让 Agent 直接查询数据库、管理迁移和部署 Edge Functions。数据库即服务公司优先推出 MCP 集成，进一步确认 MCP 是基础设施的默认工具接口层，而非仅是 Claude 的便利功能。

---

## 行业领袖

### Sachin Rekhi：Agentic 编程的四个阶段

[Sachin Rekhi 将](https://x.com/sachinrekhi/status/2064013928892645786) Agentic 编程的演进映射为四个阶段，最终于一个关键产品化里程碑：2026 年春季，Codex 和 Claude Code 均发布了 `/goal` 命令，运行自主「ralph loop」直到小型验证器通过。验证器门控的自主循环正在成为标准原语——不是实验，而是两大领先编程 Agent 中已发布的功能。这意味着「迭代直到验证通过」的模式现在对使用这些工具的每个开发者都可访问，而非仅限构建自定义框架的团队。

### Matt Van Horn：Agentic 工程实战技巧全集（2026年6月）

一份[实用的、经过验证的合集](https://x.com/mvanhorn/article/2061877533885473181)，优化日常 Agent 辅助开发的工作流——包括让每个新终端标签页直接打开进 Claude Code、上下文管理模式和工具权限策略。对于日常生活在编程 Agent 中的构建者直接可操作。元洞察：Agentic 工程正在发展出自己独特的工艺知识，有别于传统软件工程实践。

### Aaron Levie：企业级 Agent 部署是下一个前沿

[Aaron Levie 指出](https://x.com/levie/status/2051344780328858040)，Anthropic 和 OpenAI 都在启动企业级 AI Agent 部署计划，称其为「一个还早但会很快变大的趋势」。高层观察，缺乏技术细节，但信号很重要：企业级 Agent 部署——治理、安全、审计追踪、与现有 IT 集成——是商业前沿正在转移的方向。解决企业部署约束（合规、数据驻留、可观测性）的构建者将赢得这个市场。

---

## 热门话题

### Hermes Desktop：第一个围绕持久上下文构建的 Agent

[Hermes Desktop 于](https://x.com/PrajwalTomar_/status/2066195642997969255) 2026 年 6 月初发布，定位为第一个围绕持久上下文构建的 AI Agent——构建者不再需要每天早上重新解释代码库和目标。这直击核心痛点：每次新 Agent 会话都会丢失累积上下文的「冷启动」问题。持久上下文究竟是功能还是产品品类还有待观察，但痛点是真实的——上下文重建是 Agent 辅助工作流的日常税。

### Colin Hacks：哪个编程 Agent 能动态派发子 Agent？

[Colin Hacks 提了一个尖锐的问题](https://x.com/colinhacks/status/2067004040689647720)：哪个编程 Agent 能以真正动态的方式派发子 Agent？他的回答：「我不知道答案，但不是 Claude、Codex 或 opencode。」这凸显了一个真实的能力差距——当前编程 Agent 只以有限的、预定义方式处理子 Agent 派发，但真正动态的子 Agent 编排（父 Agent 在运行时根据任务结构决定如何分解和委派）仍是主流工具未解决的问题。从「针对已知任务类型的并行子 Agent」到「针对新任务的动态分解」之间的差距，正是下一次架构跃迁需要发生的地方。

---

## 新星

### Andriy Burkov 推荐论文「Dive into Claude Code」

[Andriy Burkov 推荐](https://x.com/burkov/status/2048233381305942381)论文「Dive into Claude Code: The Design Space of Today's and Future AI Agent Systems」（arXiv:2604.14228）——对 Claude Code 架构的系统性分析，识别出 5 个激励性人类价值观（能力、透明度、用户控制、效率、信任），并贯穿 13 条设计原则。这是对生产级 Agentic 编程系统最透彻的学术分析。开源仓库：[github.com/VILA-Lab/Dive-into-Claude-Code](https://github.com/VILA-Lab/Dive-into-Claude-Code)。

### Claude Architect 认证：将最佳实践编纂成文

[Claude Architect 认证考试](https://x.com/sharyph_/status/2037393353478959336)蓝图定义了 5 个领域：27% 关于 Agentic 架构（stop_reason 循环、隔离子 Agent 上下文），18% 关于工具设计和 MCP（保持每个 Agent 4-5 个有作用域的工具，而非 18 个）。「4-5 个有作用域工具」的规则是从 Anthropic 对 Agent 架构知识的正式化中浮现出的具体设计指南——暗示最佳实践共识正在成熟。

### Antigravity SDK 驱动的 Agentic PR 审查 + Gemini CLI 关停

[用 Antigravity SDK 构建 Agentic PR 审查器](https://x.com/RemikSamborski/article/2067690122703765510)，恰逢 Gemini CLI 和 Gemini Code Assist IDE 扩展宣布关停。Google 放弃其 CLI 编程 Agent（2026年6月18日宣布）标志着战略转向——可能围绕不同界面或提供商整合。与此同时，Antigravity SDK 作为在主要平台生态之外构建自定义审查 Agent 的新选项浮现。

### Hermes MCP 服务器默认关闭 —— 安全优先设计

[一篇每日回顾指出](https://x.com/NeoAIForecast/article/2068632723674263864)，Hermes MCP 服务器默认保持关闭，除非通过 `hermes tools` 或 `hermes setup agent` 命令手动重新启用。这是一个深思熟虑的安全优先设计选择——Agent 不会在未经明确选择的情况下自动连接外部工具。随着 Agent 获得对数据库、文件系统和 API 的工具访问权，默认关闭成为安全基线。构建者应将工具启用视为一个明确的、可审计的决策。

### Nous Research × NVIDIA × Stripe：商业 Agent 黑客马拉松

[Nous Research 联合](https://x.com/NousResearch/status/2066921443548348436) NVIDIA 和 Stripe 启动了 Hermes Agent 加速商业黑客马拉松——挑战构建者创造能够赚钱、花钱和运营真实业务的 Agent。主题（赚/花/运营业务）定义了下一个前沿：Agent 处理真实经济交易，而不仅仅是演示。当 Agent 有了钱包并可以交易时，架构问题从「它能完成任务吗？」转向「能信任它处理金钱吗？」

---

## 论文

### SING：面向可扩展主动工具发现的合成意图图

**作者：** Qiao Xiao, Haochen Shi, Yisen Gao 等 | [arXiv:2606.16591](https://arxiv.org/abs/2606.16591v2) | 2026年6月15日

SING 构建了一个意图感知的主动工具发现框架，使用合成意图图连接用户意图、工具能力和协作模式。它根据不断演变的任务状态动态检索工具，而非预先加载所有工具 Schema。结果：Global Recall@5 提升达 59.8%，同时将工具 Schema 上下文暴露减少 99.8%。这与 Agent OS / 工具图谱的论点直接相关——当框架连接到数百乃至数千个 API 时，发现正确工具成为关键瓶颈，而意图图驱动的检索是一种根本性的新方法。

### 组合式技能路由：分解、检索与组合

**作者：** Xueping Gao 等 | [arXiv:2606.18051](https://arxiv.org/abs/2606.18051v1) | 2026年6月16日

正式化了组合式技能路由问题：给定复杂查询和大型技能库，将查询分解为子任务，按子任务检索相关技能，并将它们组合成连贯的执行计划。这填补了一个核心空白——真实任务需要多个技能组合，而非单一技能选择。分解-检索-组合模式对可扩展 Agent 架构至关重要，也与 Hermes Agent 自身技能系统的工作方式相呼应。

### PreAct：在重复任务上更快的计算机使用 Agent

**作者：** Bojie Li | [arXiv:2606.17929](https://arxiv.org/abs/2606.17929v1) | 2026年6月16日

PreAct 让计算机使用 Agent 缓存并回放之前见过的任务的交互模式——避免在重复任务上重新读取屏幕和重新推理每个动作。通过缓存交互轨迹并在相似任务重现时回放，PreAct 显著降低延迟和成本，同时保持准确率。对于生产级 CUA 来说是必备的优化模式，因为实际使用中重复工作流（日报、常规数据录入、重复 UI 序列）占主导地位。

### VISUALSKILL：面向计算机使用 Agent 的多模态技能

**作者：** Ziyan Jiang, Li An, Yujian Liu 等 | [arXiv:2606.18448](https://arxiv.org/abs/2606.18448v1) | 2026年6月16日

将计算机使用 Agent 的技能库扩展到包含多模态（视觉）表示，因为 GUI 交互本质上是视觉的，纯文本技能会丢失信息。VISUALSKILL 用截图、视觉标注和 UI 元素布局表示技能——在未见过的软件上提升长程任务表现。核心洞察：纯文本技能描述不足以支撑视觉 GUI 任务，多模态制品能捕获文本遗漏的上下文。

### PRPO：帕累托最优的工具集成 Agent

**作者：** Junyi Li, Xiaowei Qian, Yingyi Zhang 等 | [arXiv:2606.16111](https://arxiv.org/abs/2606.16111v1) | 2026年6月15日

提出帕累托排序策略优化（PRPO）——一种两阶段方法（SFT 预热后基于帕累托排序的 RL），同时在任务准确率和工具使用效率两个维度上对齐工具使用 Agent，而非仅准确率。使用基于支配关系的优势估计来直接针对准确率与效率的权衡。工具使用效率（更少调用、更低延迟/成本）是核心的生产部署约束，而今天的 RL 方法仅最大化准确率而忽视了这一点。

### 将搜索与推理解耦：厂商中立的 Grounding

**作者：** Emmanuel Aboah Boateng, Kyle MacDonald, Amardeep Kumar 等 | [arXiv:2606.18947](https://arxiv.org/abs/2606.18947v1) | 2026年6月17日

提出一种厂商中立的 Grounding 架构，将检索策略、提供商选择、证据注入、成本和延迟从生成中解耦——使搜索 Grounding 可审计、可移植。核心论点：原生搜索 Grounding 将一切捆绑在单一模型-提供商边界之后，阻碍了独立调优。解耦使团队能在不更换模型的情况下切换检索后端、审计证据注入并控制成本。

### ProvenanceGuard：面向 MCP Agent 的来源感知事实性

**作者：** Ander Alvarez, Santhiya Rajan, Samuel Mugel 等 | [arXiv:2606.18037](https://arxiv.org/abs/2606.18037v1) | 2026年6月16日

提出面向 MCP Agent 的来源感知事实性验证，将答案追溯至特定证据来源（搜索、API、数据库、临床记录），而非仅汇总证据。标准事实性指标测试答案是否被汇总证据支持，但不验证特定来源的溯源。随着 MCP 成为 Agent 工具使用的标准接口，来源感知事实性对可信 Agent 至关重要——尤其是在临床记录等高风险领域。

### 通过交互轨迹挖掘自动生成 SKILL.md

**作者：** Yuexing Hao, Xiaomin Li 等 | [arXiv:2606.20363](https://arxiv.org/abs/2606.20363v1) | 2026年6月18日

一个三阶段管线：分割 GUI 交互轨迹、聚类，并为计算机使用 Agent 自动生成可复用技能库。直接回答 Agent 技能库能否从交互数据中自动挖掘而非手工编写这一核心可扩展性问题。如果技能可以从观察到的交互中自动生成，技能库将随使用有机增长，而非需要人工策划。

### OpenClaw-Skill：集体技能树搜索

**作者：** Tianyi Lin, Chuanyu Sun, Jingyi Zhang 等 | [arXiv:2606.16774](https://arxiv.org/abs/2606.16774v1) | 2026年6月15日

一个通过集体技能树搜索自动为 LLM Agent 构建可复用技能的框架——增强工具使用、多步推理和动态环境中的表现。Agent 不是手工编写技能，而是通过搜索构建自己的技能树。这切中了 Agent 架构中「可训练技能」论点的核心：技能成为可学习、可进化的对象，而非静态配置文件。

### VisualClaw：面向物理世界的实时个性化 Agent

**作者：** Haoqin Tu, Jianwen Chen, Zijun Wang 等 | [arXiv:2606.16295](https://arxiv.org/abs/2606.16295v1) | 2026年6月15日

一个基于级联的 VLM Agent，将 1 小时直播从约 3,600 次 API 上传减少到仅 5-20 次调用，同时在部署后自我进化其脚手架以实现个性化。直接解决两个部署瓶颈：密集视频处理的成本和静态的部署后脚手架。级联成本削减模式（廉价过滤，仅在需要时升级）对文本/工具中心的 Agent 也具有广泛可迁移性。

---

## 值得关注

**工具与技能架构**
- **ToolPro**：将 Agent 工具意图表示为可执行程序（含循环、条件、连接、重试），而非静态 API 端点——一种面向 Agentic Web 的「程序即工具接口」模式。([arXiv:2606.19992](https://arxiv.org/abs/2606.19992v1))
- **大型语言模型并不总是需要可读语言**：研究 LLM 是否能使用紧凑的、非人类可读编码进行模型间通信，可能减少多 Agent 管线中的 Token 开销。([arXiv:2606.19857](https://arxiv.org/abs/2606.19857v1))
- **SafeClawBench**：分离工具使用 Agent 中的三个危害阶段——语义（不安全文本）、审计证据（溯源）和沙箱（实际工具效果如写内存或修改数据库）。([arXiv:2606.18356](https://arxiv.org/abs/2606.18356v1))

**多 Agent 系统**
- **传染网络**：衡量评估者偏差如何在多 Agent LLM 系统中传播的形式化框架——有偏差的评估者污染整个推理链。([arXiv:2606.20493](https://arxiv.org/abs/2606.20493v1))
- **论 AI Agent 网络的可靠性**：应用密度演化和停止集分析（来自编码理论）来理解不完美 Agent 网络何时能成功组合解决方案。([arXiv:2606.18121](https://arxiv.org/abs/2606.18121v1))
- **AdaSTORM**：自适应时空多 Agent 协作，克服动态图上 LLM 推理的扩展瓶颈。([arXiv:2606.16328](https://arxiv.org/abs/2606.16328v1))
- **多 Agent 多目标优化**：一个在动态环境中使用 RL 实现性能约束下成本最小化的多 Agent 系统。([arXiv:2606.20236](https://arxiv.org/abs/2606.20236v1))
- **多 Agent 博弈中的分层控制**：LLM 规划结合 RL 执行，用于复杂多 Agent 博弈环境——规划者/执行者分离模式。([arXiv:2606.20014](https://arxiv.org/abs/2606.20014v1))

**评估与基准**
- **超越静态排行榜**：汇总了一项基于 MCP 的工业 Agent 基准的 14 项平行实施研究——发现没有任何单一基准能覆盖超过 4-5 个部署维度。基准间的预测效度有限。([arXiv:2606.19704](https://arxiv.org/abs/2606.19704v1))
- **推理计算如何塑造前沿 LLM 评估**：表明评估结果越来越敏感于推理计算分配，但许多评估未加控制——使跨系统比较不可靠。([arXiv:2606.17930](https://arxiv.org/abs/2606.17930v1))
- **LLM 的黑盒不确定性估计**：系统评估仅通过 API 可访问的不确定性估计方法——对实际黑盒设置下可信 Agent 系统至关重要。([arXiv:2606.19868](https://arxiv.org/abs/2606.19868v1))
- **LabOSBench**：基于 Web 的科学仪器模拟器基准，评估计算机使用 Agent在具有硬性程序排序和反馈驱动参数调整的任务上的表现。([arXiv:2606.16802](https://arxiv.org/abs/2606.16802v1))

**领域特定 Agent 应用**
- **AgentFinVQA**：可部署的多 Agent 管线，用于可审计的金融图表问答，内置审计追踪和本地部署模式。([arXiv:2606.19782](https://arxiv.org/abs/2606.19782v1))
- **Guava**：基于 Harness 的具身操作方法，结合 LLM 推理与外部感知/动作模块——端到端 VLA 的替代方案。([arXiv:2606.18363](https://arxiv.org/abs/2606.18363v1))
- **零样本灵巧操作**：使用 VLM 推理配合多视角 RGB 生成 3D 任务计划，无需训练端到端策略。([arXiv:2606.19340](https://arxiv.org/abs/2606.19340v1))
- **AI 辅助科学工作流管理**：使用 LLM 自动化科学 WMS 中的工作流设计、实施和调试。([arXiv:2606.18425](https://arxiv.org/abs/2606.18425v1))
- **LectūraAgents**：用于自适应个性化 AI 辅助学习的多 Agent 框架。([arXiv:2606.16428](https://arxiv.org/abs/2606.16428v1))
- **PowerAgentBench-SS**：基准测试 LLM Agent 能否在电力系统中执行工程工作流。([arXiv:2606.18789](https://arxiv.org/abs/2606.18789v1))
- **GeoDisaster**：基准测试编排 Agent 在工具驱动的空间推理上执行灾害地理情报任务。([arXiv:2606.17246](https://arxiv.org/abs/2606.17246v1))

**治理与安全**
- **架构智慧**：一个框架，论证 AI 系统需要架构机制来质疑一个目标是否应该被优化——而不仅仅是向着定义不足的目标进行优化。([arXiv:2606.16319](https://arxiv.org/abs/2606.16319v1))
- **库感知替身与迭代修复**：用于固件中 LLM 生成测试的自动化单元测试编写工作流——库感知的测试生成与迭代修复。([arXiv:2606.19725](https://arxiv.org/abs/2606.19725v1))

---

## 中文社区

> 今日中文内容来自知乎网页搜索，涵盖 Agent 架构趋势与框架分析。

- **国产模型登上编程 Agent 榜单**：DeepSeek V4 Pro、Kimi K2.6、GLM-5.1 全部进入实测排行榜，以 Claude Code 为框架底座——模型可换，但 harness 才是护城河。

- **2026 年 AI Agent 技术全景**：12 大主流框架深度解析，现代 Agent 标准架构包含感知→决策→行动→记忆完整闭环。([知乎专栏](https://zhuanlan.zhihu.com/p/2026254728342905724))

- **AI Agent 完整工作流全景解析**：深度拆解 Agent 的感知、规划、记忆与行动闭环，结合 Gartner 与 McKinsey 数据，提供可落地的架构指南。([知乎专栏](https://zhuanlan.zhihu.com/p/1996954141231190461))

- **15 篇 AI Agent 研报**：架构向「云原生+Agent 协同」重构，支持多 Agent 协同与微秒级推理。([知乎专栏](https://zhuanlan.zhihu.com/p/1996902325206405568))

- **2026：Agent 之年**：智能系统实现真正的多模态理解与执行，Zero-trust 安全架构强调每一步操作的验证与监控。([知乎专栏](https://zhuanlan.zhihu.com/p/2005591914448193177))

- **LangGraph 成为生产级 Agent 运行时事实标准**：LangChain 发布 Deep Agents（基于 LangGraph 的超级 Agent Harness），与 NVIDIA 合作。发现 LangChain/LangGraph 路径遍历和 SQL 注入漏洞。([知乎专栏](https://zhuanlan.zhihu.com/p/2021064941650679235))

- **Hermes 智能体研究报告**：Hermes Agent 由 Nous Research 开发的开源自主 AI 智能体，2026年2月正式发布，与 OpenClaw 对比分析。([知乎专栏](https://zhuanlan.zhihu.com/p/2026622473097978502))

- **Agentic AI 十大关键趋势**：系统架构从单体应用向分布式智能体网络演进，IBM 预测将出现 Agent 控制平面和多 Agent 仪表盘。([知乎专栏](https://zhuanlan.zhihu.com/p/1991451643544355292))

---

*通过 X 网页搜索、arXiv API 和知乎网页搜索采集。X「For You」信息流不可用（会话认证缺口）——X 条目来自关键词和公司账号网页搜索。完整候选档案已本地保存。*
