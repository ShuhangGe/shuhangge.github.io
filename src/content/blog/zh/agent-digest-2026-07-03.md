---
title: "Agent 架构每日速递 — 2026 年 7 月 3 日"
description: "MCP 迎来史上最重要一次升级：7·28 候选版去掉会话状态、引入 Extensions / Tasks / MCP Apps，从工具调用协议变成完整的 Agent 基础设施层；Claude Code、Cursor、Manus、Devin、SWE-Agent 的 harness 架构走向收敛；agent 目标完成循环成为平台原生原语；30 个子 Agent 生产级目录证明专精化已成规模；多 Agent 上下文污染被命名为 2026 年的核心难题；Karpathy 宣告 slopacolypse 阈值已过；OpenAI 报告 Codex 正在改造所有部门；X 上线托管式 MCP server；UC Berkeley Agents' Last Exam 显示顶级模型仍跑不通大部分真实工作流；OpenAgent 论文形式化了打破静态训练工具 Agent 的四条分布偏移轴。"
pubDate: "2026-07-03"
lang: zh
tags: ["Agent", "LLM", "AI 架构", "每日速递"]
---

## TL;DR — 今日概览

> 今天最值得关注的 10 件事：

1. **MCP 全面走向无状态——并叠加了一整套基础设施层。** 2026-07-28 规范候选版移除了 `Mcp-Session-Id`、`initialize` 握手和粘性路由要求，同时引入 Extensions（可组合的模块化协议特性）、Tasks（结构化的 agent 任务原语）、MCP Apps（应用级构造）、授权加固和正式弃用策略。这是 MCP 迄今最重要的一次演进：一个工具调用协议正在变成任务编排与应用集成的底层支撑，能够横向扩展、Serverless 部署。 — [MCP 博客](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/) · [byteiota](https://byteiota.com/mcp-goes-stateless-july-2026-breaking-changes/)

2. **Agent harness 正在收敛为一套共识架构。** Aaron Levie 观察到，目前出货能力最强的 agent 工具——Claude Code、Cursor、Manus、Devin、SWE-Agent——全部收敛到同一个核心架构：脚手架 + 工具循环 + 验证 harness，共同构成了力放大器。当独立开发的平台不约而同走向同一个模式时，这个模式正在成为事实标准。 — [@levie](https://x.com/levie/status/2028711992320835686)

3. **Agent 循环已经成为一等平台原语，不再是你要自己搭的东西。** Claude Code（2026 年 5 月原生 `/goal` 命令）和 Codex（2026 年 4 月）都把「设定目标 → 推理 → 行动 → 验证 → 重复」这个循环形式化为内置命令。agent 循环已经从开发者要自己实现的模式，毕业成了平台直接提供的基础设施。 — [@aiecosystemhq](https://x.com/aiecosystemhq/article/2069721070610039044)

4. **Karpathy 定调：2026 是「slopacolypse（烂代码末日）」之年。** 大量使用 Claude Code 之后，他表示 agent 能力（尤其是 Claude 和 Codex）已经跨过一道门槛——自主代码生成在整个 GitHub 上已成为常态。「slopacolypse」这个说法同时抓住了能力跃迁和海量 agent 生成代码带来的质量控制压力。 — [@karpathy](https://x.com/karpathy/status/2015883857489522876)

5. **OpenAI 报告：agent 正在改造所有部门的工作。** Codex 被用于更复杂、更长时、越来越多跨职能的任务。一家实验室用自己的 agent 平台做跨部门生产级部署，本身就是最佳案例：agentic 工作已从演示规模跨入部署规模。 — [@OpenAI](https://x.com/OpenAI)

6. **X 上线托管式 MCP server。** Cursor、Claude 等开发工具现在可以通过一个第一方维护的 MCP 接口，直接连接 X 的 API 和文档。一个大型平台自己托管 MCP server，是协议生产就绪的强烈信号——也让脆弱的自定义集成变得多余。 — [cybersecuritynews](https://cybersecuritynews.com/x-launches-hosted-mcp-servers/)

7. **2026 年多 Agent 的核心难题有了一个名字：上下文污染（context poisoning）。** 多 Agent 工作流的核心挑战，在于协调那些「一旦共享上下文就会互相污染」的 agent。把协调与隔离问题精确命名本身就是一种贡献——它告诉构建者真正困难的工程工作在哪里。 — [@Av1dlive](https://x.com/Av1dlive/article/2061386872321130782)

8. **子 Agent 专精化的规模实证：生产中跑着 30 个 Claude Code 子 Agent。** 一份真实世界的子 Agent 目录，每一个都是 `.claude/agents/` 下的一个 markdown 文件，定义一个专精角色。这证明「把工作分解成声明式规格定义的专精 agent」已是主流的、已部署的模式，而非理论。 — [@heynavtoor](https://x.com/heynavtoor/article/2050148589134045443)

9. **UC Berkeley Agents' Last Exam：GPT-5.5 超过 Fable 5——但两者都跑不通大部分任务。** ALE 测量的是真实、有经济价值的专业工作流的端到端执行，并抵抗基准污染。GPT-5.5 第一，Claude Fable 5 第二，但绝对通过率仍然很低：模型能可靠地开始 agentic 任务，却远不能可靠地完成它们。 — [opentools](https://opentools.ai/news/gpt-55-beats-claude-fable-5-agents-last-exam-benchmark-2026)

10. **OpenAgent 论文：静态训练的工具 Agent 在四个轴上都很脆弱。** 形式化了查询（query）、动作（action）、观测（observation）和领域（domain）四条分布偏移轴——精确解释了为什么在基准上表现优异的 agent 一上生产就崩。它命名了每个 agent 构建者必须测试的四个失败面。 — [arXiv:2607.01084](http://arxiv.org/abs/2607.01084v1)

📊 今日数据：**采集 X「For You」22 条 | 分析候选 84 条 | 已分析 44 条 | 详细条目 15 条 | 短讯 2 条 | 论文 8 篇** ——本期由 2026-07-28 MCP 规范候选版驱动，协议层话题密集。

---

## 本期主轴：协议成为 Agent 的脊柱

贯穿今日讨论的主线，是**协议层和 harness 层**的成熟——它们夹在模型和实际工作之间。四个信号汇聚到同一条线索上。

**信号一：MCP 不再只是工具调用协议，开始成为 agent 基础设施。** 2026-07-28 候选版是迄今最重大的 MCP 更新。它移除了会话状态——`Mcp-Session-Id`、`initialize` 握手和粘性路由——这消除了生产 MCP 部署中最大的运维复杂度，打开了横向扩展和 Serverless 部署的大门。随后叠加了 Extensions（可组合、模块化的协议特性）、Tasks（把编排推入确定性代码的结构化任务原语）、MCP Apps（应用级构造）和授权加固。同一周，X 上线了托管式 MCP server，WebKit 发布了 Safari MCP server。协议不再只是套在工具外面的壳——它正在变成 agent 与世界集成的脊柱。

**信号二：harness 趋同。** Aaron Levie 指出 Claude Code、Cursor、Manus、Devin、SWE-Agent 全部收敛到同一个 harness 架构（脚手架 + 工具循环 + 验证），这是业界最强的架构信号。独立团队殊途同归，恰恰验证了它就是共识模式。力放大器不是模型本身——而是围绕模型的那套纪律严明的循环。

**信号三：循环成为原语。** agent 目标完成循环（设定目标 → 推理 → 行动 → 验证 → 重复）现在已是两大平台的原生命令——Claude Code 的 `/goal` 和 Codex 的等价物。30 个子 Agent 的生产目录、并行研究 agent 的启动（`/ce:plan`）都在展示同一个理念向外扩散：循环是基础设施，开发者基于它构建，而不是重新实现它。

**信号四：极限被诚实地命名。** Karpathy 的「slopacolypse」框定了海量 agent 生成代码的质量压力。ALE 显示模型启动任务远好于完成任务。OpenAgent 论文形式化了打破静态训练 agent 的四条分布偏移轴。而多 Agent 上下文污染问题现在被精确地框定出来。先给失败模式命名，才有解决的可能。

综合来看：今天真正的进展发生在模型层**之下**。模型越来越像可替换的商品；持久的价值在协议（MCP）、harness、循环原语，以及对 agent 仍在何处崩塌的诚实地图里。

---

## X / Twitter 精选

### 公司动态

[**@AnthropicAI**](https://x.com/AnthropicAI/status/1949898502688903593) 宣布 **Claude Code 将从 2026 年 8 月 28 日起启用新的每周用量上限。** 这次调整重新计量了 agent 编码工作负载——从按会话计费改为按周封顶模型。对于跑长时自主编码会话的团队来说，这重塑了 agent 容量规划的经济模型：每周上限直接约束了一个订阅能承载多少持续的、无人值守的 agent 工作。

[**@AnthropicAI**](https://x.com/AnthropicAI/status/1903128670081888756) 还向 **Claude Code 推送了扩展思考（extended thinking）和改进的工具使用。** 扩展思考在行动前加入多步推理；工具使用改进让 agent 更可靠地规划和执行复杂工作流。这是对 harness「先推理后行动」阶段的直接升级——整个循环中最容易把自己逼进死角的环节。

[**@OpenAI**](https://x.com/OpenAI) 报告 **agent（尤其是 Codex）正在改造所有部门的内部工作。** 任务更复杂、更长时、越来越多跨职能。一家实验室用自己的 agent 平台做跨部门生产级案例研究，是 agentic 工作从演示跨入部署规模的具体证据。值得追踪的信号：agent 的价值现在以完成的跨职能工作来衡量，而不是单次准确率。

[**MCP 博客**](https://blog.modelcontextprotocol.io/posts/2026-07-28-release-candidate/) 发布了 **2026-07-28 MCP 规范候选版。** 除了无状态核心（见「行业领袖」小节），候选版还引入了 Extensions 框架（模块化、可组合的协议特性）、Tasks（结构化 agent 任务原语）、MCP Apps（应用级 MCP 构造）、授权加固和正式弃用策略。累积效果：MCP 从简单的工具调用协议，毕业为具备任务编排、应用级集成和生产级授权的完整 agent 基础设施层。每个使用 MCP 的 agent 工具开发者都需要为这些破坏性变更做准备。

[**DeepReinforce AI / lushbinary**](https://lushbinary.com/blog/ornith-1-0-developer-guide-benchmarks-agentic-coding/) 发布了 **Ornith 1.0**——一组开源的自改进（self-improving）模型，专为 agentic 编码打造，附带覆盖基准和编码 agent harness 集成的开发者指南。开源的自改进编码模型降低了本地跑自主编码 agent 的门槛，为 Claude Code、Codex 等闭源系统提供了一个替代底座。注意：「自改进」是一个很强的声明，缺乏独立验证——把它当作值得关注的 emerging 工具，而非已验证的系统。

### 行业领袖

[**@levie**](https://x.com/levie/status/2028711992320835686)（Aaron Levie）指出**领先 agent 平台之间的架构趋同。** 「现在 agent harness 的力放大器非常疯狂。今天出货能力最强的 AI agent 公司——Claude Code、Cursor、Manus、Devin、SWE-Agent——全部收敛到同一个架构。」当独立开发的工具不约而同走向同一个模式时，它就验证了这个模式是事实标准：脚手架 + 工具循环 + 验证就是共识的 agent 设计。

[**@karpathy**](https://x.com/karpathy/status/2015883857489522876)（Andrej Karpathy）宣告 **2026 是「slopacolypse（烂代码末日）」之年。** 近几周大量使用 Claude Code 之后，他观察到 agent 能力（尤其是 Claude 和 Codex）已跨过阈值——自主代码生成在整个 GitHub 上已成常态。这个说法同时抓住了能力边界的跨越和由此产生的质量控制压力。对任何构建会产出代码的 agent 系统的人来说，这是必不可少的背景。

[**byteiota**](https://byteiota.com/mcp-goes-stateless-july-2026-breaking-changes/) 拆解了 **7 月 28 日 MCP 规范到底破坏了什么。** 候选版移除了 `Mcp-Session-Id`、`initialize` 握手和粘性路由要求——把 MCP 从有状态协议（需要会话追踪和粘性路由）变成无状态协议。这是生产 MCP 部署中最大的运维简化：无状态服务器可以横向扩展、Serverless 部署、无需会话亲和性的负载均衡。每个使用 MCP 的 agent 工具开发者都必须为这次破坏性变更做准备。

[**cybersecuritynews**](https://cybersecuritynews.com/x-launches-hosted-mcp-servers/) 报道了 **X 上线托管式 MCP server。** Cursor、Claude 等 AI 开发工具现在可以通过一个标准化、第一方维护的接口，直接连接 X 的 API 和文档。一个大平台提供托管式 MCP server 是重要的采纳信号——agent 构建者拿到的是维护中的接口，而不是去搭脆弱的自定义集成，验证了协议的生产就绪性。

### 热议

[**@heynavtoor**](https://x.com/heynavtoor/article/2050148589134045443) 发布了**《我在 2026 年实际在用的 30 个 Claude Code 子 Agent》**。每个子 Agent 都是 `.claude/agents/` 文件夹下的一个 markdown 文件，定义一个专精角色。这是子 Agent 专精化模式在生产规模上的真实落地——把工作分解成由声明式规格定义的聚焦 agent，而不是一个大而全的 prompt。这份目录是「子 Agent 架构是主流的、已出货的模式」的实证。

[**@mvanhorn**](https://x.com/mvanhorn/article/2035857346602340637) 分享了**《我所知道的每一个 Claude Code 技巧（2026 年 3 月）》**，其中 `/ce:plan` 可以并行启动多个研究 agent。并行 agent 启动改变了 agent 工作的吞吐模型：不再是顺序的子任务，而是规划器扇出并发的研究 agent。这里的编排技巧对跑多 agent 工作流的构建者直接有用。

[**@nateherk**](https://x.com/nateherk/article/2059377638896971985) 做了一场**Claude Code 对 Codex 的 100 小时正面较量**，横跨真实编码任务。对两大领先 agent 平台的长时间真实世界对比测试，为架构决策提供了实证依据——比较 harness 数据稀缺且珍贵，对选平台的团队尤其如此。耐力赛式方法（持续使用，而非玩具任务）才是评估 agent harness 的正确方式。

[**@aiecosystemhq**](https://x.com/aiecosystemhq/article/2069721070610039044) 发布了**《Claude Code 与 Codex 中 AI Agent 循环的完整指南》**。Claude Code 于 2026 年 5 月 11 日上线了原生 `/goal` 命令；Codex 于 4 月 30 日上线了等价物。两者都把目标完成循环——设定目标 → 推理 → 行动 → 验证 → 重复——形式化为一等平台原语。循环不再是要开发者自己搭的东西，而是平台提供的基础设施。

[**@Av1dlive**](https://x.com/Av1dlive/article/2061386872321130782) 探讨了多 Agent 工作流，并命名了 2026 年的核心挑战：**上下文污染。** 「2026 年的多 Agent 系统有一个决定性问题：你如何协调那些一旦共享上下文就会互相污染的 agent。」把协调与隔离问题精确命名本身就是贡献——它告诉构建者真正困难的工程工作在哪里（上下文隔离和协调协议，而不仅仅是堆更多 agent）。

[**opentools**](https://opentools.ai/news/gpt-55-beats-claude-fable-5-agents-last-exam-benchmark-2026) 报道了 **UC Berkeley 的 Agents' Last Exam（ALE）**。GPT-5.5 第一，Claude Fable 5 第二——但两者都跑不通大部分任务。ALE 是最早一批围绕真正的 agentic 工作流完成度（真实、有经济价值的专业任务）而非问答式题目构建的基准之一，并抵抗基准污染。标题是：顶级模型与可靠自主执行之间的差距依然很大。模型启动 agentic 任务远比完成它们可靠——这恰好框定了 agent-harness 研究下一步该去哪。

---

## 论文与研究

### [Can Agents Generalize to the Open World? Unveiling the Fragility of Static Training in Tool Use](http://arxiv.org/abs/2607.01084v1)
**Song-Lin Lv, Weiming Wu, Rui Zhu**（arXiv，2026 年 7 月）

形式化了 **OpenAgent**——一个问题设定，揭示在静态基准上训练的工具 Agent 在开放世界的分布偏移下会崩溃。这项工作识别出四条偏移轴——查询（query）、动作（action）、观测（observation）和领域（domain）——它们导致 agent 在生产中失败。在基准上看起来熟练的 agent，当查询、工具集和交互动态发生变化时就会变脆。对构建者来说，它命名了需要测试的确切失败面，并提供了一个衡量基准到部署之间差距的问题设定。**为何重要：** 泛化是当前 agent harness 的核心弱点；这篇论文把它精确地图绘了出来。

### [An Organization-Scoped LLM Agent Runtime Architecture for Regulated Cybersecurity Operations](https://www.semanticscholar.org/paper/ec6f95469704c8e2ec223dd32e4deee0099346ef)
**George Fatouros, Georgios Makridis, George Kousiouris**（Semantic Scholar，2026 年 5 月）

定义了一种**模型无关的 agent 运行时**，用于受监管的网络安全场景：在每个入口点创建一个类型化 *Security Context*，在一个组织级契约下治理检索、工具调用、记忆、发现、报告和审计。这个贡献超越了网安领域：它把 scoping/多租户当作一个一等运行时关注点，贯穿*每一个* agent 子系统，而不是事后打补丁——对构建多租户或合规约束 agent 直接相关。

### [Registry-Governed Agent Lifecycle: Completing EDDOps with Evaluation-Driven Registration, Promotion, and Retirement on AWS AgentCore](http://arxiv.org/abs/2607.00345v1)
**Richard Kang, Vincent Wang**（arXiv，2026 年 7 月）

提出 **Evaluation-Driven Development and Operations（EDDOps）**：一种注册表治理的生命周期，基于跨质量、可靠性、安全、延迟和成本的持续评估，处理 agent 的注册、晋升和退役。核心思想——把评估当作贯穿 agent 整个生命周期的*持续治理函数*，而不是部署前的一道门——是一个团队可以直接采用的实操模式。生产级 agent 治理是一个被忽视的领域；这是一份可用的蓝图。

### [Identity as Attractor: Geometric Evidence for Persistent Agent Architecture in LLM Activation Space](https://www.semanticscholar.org/paper/84fbdc2b5599bef396f2be594596830914fdff3d)
**V. Vasilenko**（Semantic Scholar，2026 年 4 月）

在 Llama 3.1 8B 上用实验表明，持久 agent 的身份文档——它的 `cognitive_core`——会在 LLM 激活空间中诱导出**吸引子般的几何结构**。共享同一身份的改写 prompt 会收敛成比对照组更紧凑的隐状态簇。这提供了机制层面的证据：agent 身份/持久性不仅仅是 prompt 工程的小技巧，而在模型内部有可测量的几何印记——对构建带稳定人格或记忆的长时间运行 agent 的人直接相关。

### [Managed Autonomy at Runtime: Gear-Based Safety and Governance for Single- and Multi-Agent Cyber-Physical Systems](http://arxiv.org/abs/2607.00334v1)
**Srini Ramaswamy, Wang Miaosheng**（arXiv，2026 年 7 月）

引入了一种**「受管自主」运行时模式**，带齿轮式的安全与治理。「齿轮」这个比喻——逐级提升的自主级别配以安全检查点——解决 agent 在无人持续监督下运行时出现的安全违规、行为不稳定和连续性丢失问题。它是一种可迁移的「优雅降级」模式，适用于任何必须安全失败的 agent——而这正是大多数当前 agent stack 的真实缺口。

### [Autonomous Scientific Discovery via Iterative Meta-Reflection](http://arxiv.org/abs/2607.01131v1)
**Bingchen Zhao, Sara Beery, Oisin Mac Aodha**（arXiv，2026 年 7 月）

提出一个由**迭代元反思（iterative meta-reflection）**驱动的自主科学发现系统——面向开放式假设生成与验证，而非带预定义问题的受限搜索空间。可复用的洞见是它的反思循环架构：一个会精炼自身目标的 agent，其适用性远超科学发现，延伸到任何自改进 agent。

### [Agentic-Ideation: Sample Efficient Agentic Trajectories Synthesis for Scientific Ideation Agents](http://arxiv.org/abs/2606.31229v1)
**Keyu Zhao, Lingyan Kong, Fengli Xu**（arXiv，2026 年 6 月）

**Agentic-Ideation** 为科学创意合成样本高效的 agentic 轨迹，从固定的预定义工作流转向灵活的轨迹生成。可迁移的贡献是「trajectory-as-data」理念：合成可复用的 agent 轨迹，而不是手工编写工作流——一种对构建灵活 agent pipeline 的人都相关的技术。

### [COOPA: A Modular LLM Agent Architecture for Operations Research Problems](https://www.semanticscholar.org/paper/a0e51871ef04f31021c5aa9438cb7d4842dd24cf)
**Chuanhao Li, Xiaoan Xu, Dirk Bergemann**（Semantic Scholar，2026 年 6 月）

**COOPA** 是一个模块化 LLM agent，把运筹学决策分解为专精子模块——领域抽象、数学建模、求解器编排——自动化通常需要专家手动完成的端到端 OR 建模。它证明模块化 agent 模式（规划器 / 工具调用器 / 求解器的拆分）能从编码泛化到高风险的数学优化。

---

## 值得关注

- **[Top 60 Claude Skills, Workflows, and GitHub Repos](https://x.com/PrajwalTomar_/status/2038654002611769529)**（[@PrajwalTomar_](https://x.com/PrajwalTomar_)）——60 个 Claude 技能、工作流和仓库的策展目录。作为构建者参考有用，但更像链接合集，架构洞见不深。
- **[Safari MCP server 进入 Technology Preview 247](https://webkit.org/blog/18136/introducing-the-safari-mcp-server-for-web-developers/)**（WebKit）——一个第一方浏览器 MCP server，给 agent 提供 Safari 的程序化访问，用于网页自动化、测试和数据提取。MCP 生态继续向浏览器自动化扩展。

---

## 中文社区

- **[【2026 深度指南】AI 智能体 (Agent) 完整工作流全景解析](https://zhuanlan.zhihu.com/p/1996954141231190461)**（知乎）——对 agent「感知 → 规划 → 记忆 → 行动」闭环的深度拆解，融合 Gartner 和麦肯锡数据，形成一套开发者可部署的架构指南。
- **[深度解析｜AI Agent 自动化工作流：从架构设计到落地实践（2026年最新）](https://zhuanlan.zhihu.com/p/2009947555816038451)**（知乎）——基于半年实操经验的完整 agent 部署框架。核心论点：2026 年的竞争优势不在于你用*哪款* AI，而在于你把它运营化得*有多深*。
- **[2026年AI Agent智能体元年：技术突破与产业变革](https://zhuanlan.zhihu.com/p/2024948884221306611)**（知乎）——论证 2026 是公认的「AI agent 元年」，追踪从阿里云超级 agent 计划到 Meta 开放 Llama 4 商用，再到开源项目突破 13.6 万 GitHub 星标的信号。

---

*由 Agent 架构每日速递流水线采集与筛选。原始候选与分析存档于 `/Users/shuhangge/Desktop/agent-digest/`。关注 [@shuhangge](https://x.com/shuhangge) 获取每日更新。*
