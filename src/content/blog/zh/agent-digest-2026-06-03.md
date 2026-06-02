---
title: "AI Agent 架构日报 — 2026年6月3日"
description: "Perplexity 的 Search as Code 范式、状态外置化 Agent 框架、技能学习的元技能突破、OpenAI 后训练负责人的模型-vs-harness 论述，以及 7 篇技能学习新论文。"
pubDate: "2026-06-03"
lang: zh
tags: ["Agent架构", "大模型", "技能学习", "AI日报"]
---

## TL;DR — 今日要点

1. **Perplexity Search as Code**：用 Agent 直接写 Python 代码编排搜索流程，替代 function calling 的串行瓶颈 ([@yibie](https://x.com/yibie/status/2061633153325015067))
2. **Harness-1 状态外置化框架**：将环境可追踪的状态从模型策略中分离，20B 模型训练效果超过全上下文 RL ([@dair_ai](https://x.com/dair_ai/status/2061825437693841651))
3. **OpenAI Yann Dubois**："如果冻结当前模型，全力投入 harness 建设，人们已经在每个领域感受到 AGI。"持续学习仍是未解难题 ([@servasyy_ai](https://x.com/servasyy_ai/status/2061678793170059596))
4. **SkillEvolver**：技能作为元技能——Agent 从执行反馈中自我改进技能，无需重新训练模型（Zhang et al., 2026年5月）
5. **EvoAgent**：分层子 Agent 委托 + 进化式技能元数据——本月架构最完整的 Agent 论文（Zhang et al., 2026年4月）
6. **FastClaw 云原生 Agent 运行时**：存算分离架构，代码量仅为 OpenClaw 的 1/40，资源消耗 1/7，冷启动秒级 ([@idoubicc](https://x.com/idoubicc/status/2061405448356741364))
7. **MiniMax M3 开源模型**：59% SWE-Bench Pro，66% Terminal Bench 2.1，1M 上下文稀疏注意力 ([@MiniMax_AI](https://x.com/MiniMax_AI/status/2061266317815296322))
8. **MetaSkill 自组织技能协议**：自然语言描述目标，自动发现、组合甚至生成新技能，Apache 2.0 开源 ([@OpenSquilla](https://x.com/OpenSquilla/status/2061481500974157956))
9. **skillGenBench**：首个评估 LLM 自动生成 SKILL.md 能力的基准测试 ([@sanbuphy](https://x.com/sanbuphy/status/2061629549516083222))
10. **Pi Agent**：极简开源终端 coding agent，"核心保持小巧，复杂能力交给扩展" ([@teach_fireworks](https://x.com/teach_fireworks/status/2061804587833786629))

今日数据：**25 条深度解读 | 7 篇论文 | 6 条值得关注 | 200 条原始推文筛选到 100 条候选**

---

## 公司动态

### Perplexity Search as Code：function calling 的终结？

[@yibie](https://x.com/yibie/status/2061633153325015067) · 115 赞

Perplexity 发布全新架构 **Search as Code（SaC）**。核心思路：AI Agent 不再通过 function calling 一次一次地调用搜索 API，而是直接写 Python 代码编排整个搜索流水线。传统架构中，每次搜索操作都需要一次 LLM inference 往返——单个查询没问题，但当 Agent 需要完成涉及数百次检索的复杂任务时，这个串行瓶颈就无法忍受了。SaC 将其压缩为一次代码生成 + 代码执行。这不是性能优化，而是从声明式工具调用到命令式代码编排的范式转移。

### OpenAI 语音黑客松：语音 Agent 进入移动 OS

[@OpenAIDevs](https://x.com/OpenAIDevs/status/2061558243911155722) · 391 赞

冠军项目 **Agentic OS for a Phone**：语音优先的移动操作系统，用户说话，Agent 回答并在手机上执行操作。Wagner（多 Agent 虚拟会议室）等项目也入围。语音作为 Agent 的一等交互模态正在加速到来——移动 OS 层面的集成意味着 24/7 在线的环境式 Agent 交互，远超聊天机器人体验。

### Anthropic 扩大 Project Glasswing

[@AnthropicAI](https://x.com/AnthropicAI/status/2061796327986454883) · 2,172 赞

Claude Mythos Preview 现向 15+ 个国家的约 150 个组织开放。Glasswing 仍是 Anthropic 最神秘的前沿项目——此次扩展暗示对其成熟度的信心提升，也揭示了前沿模型访问控制的组织架构思路。

---

## 行业领袖与架构

### 你错过的最重要论文：状态外置化 Agent 框架

[@dair_ai](https://x.com/dair_ai/status/2061825437693841651) · 62 赞

**Harness-1** 提出全新的 Agent 训练范式：将策略（模型学什么）与 Harness（环境管理什么）分离。核心洞察：如果状态可以被环境可靠维护，它就不应该放在模型里，移到 Harness 去。

一个 20B 模型用状态外置化训练后，表现和泛化能力超过了全交互上下文 RL 训练的模型。RL 不再需要同时学习语义搜索和日常簿记——Harness 处理琐碎的状态管理，策略专注于推理。这对 Agent 训练效率有重大影响，尤其在上下文窗口不断增长的背景下。

### OpenAI 后训练负责人 Yann Dubois：瓶颈在工程，不在模型

[@servasyy_ai](https://x.com/servasyy_ai/status/2061678793170059596) · 仅 1 赞（严重被低估的洞见）

Yann Dubois 在 MAD Podcast 上分享了五个关键观点：

1. **AI 进步是连续的，感知是阶跃的。**可靠性在 2024 年 12 月前后跨过阈值——工具突然变得"可用"。在此之前，同样的增量进步不可见。
2. **三大驱动因素**：可靠性达标、模型自我加速（AI 写 AI）、RL 从可验证奖励扩展到真实世界用例。
3. **通用 harness 不会持久。**特定领域的短期 harness 才是方向，要做好不断重调的准备。
4. **"如果冻结当前模型，全力投入 harness 建设，人们已经在每个领域感受到 AGI。"** 这是迄今为止最明确的表态：瓶颈是工程，不是模型能力。
5. **持续学习仍是未解难题。**模型的学习曲线是平的——第一天就是专家，然后永远不进步。人类单调递增。这是 AI 最大的弱点。

### FastClaw：Agent 的"Serverless"架构

[@idoubicc](https://x.com/idoubicc/status/2061405448356741364) · 322 赞

一位托管服务商分享了迁移经历：K8s 集群上 500 个 Agent Pod，18 台服务器，月成本 $5K，MRR 不足 $8K。迁移到 **FastClaw**（存算分离的云原生 Agent 运行时）后，服务器降到 3 台，成本降至 1/6。

FastClaw 的架构与 OpenClaw 根本不同：Agent 不是有状态的长驻进程，而是短生命周期的。收到请求时动态挂载 sandbox 提供服务，完成后销毁。代码量：OpenClaw 的 1/40。资源占用：1/7。冷启动：秒级 vs 15 秒。单二进制分发，无环境依赖。

这复刻了 Serverless 对传统应用服务器的颠覆——也许会成为多租户 Agent 托管的默认模式。

### MiniMax M3：开源模型首次在 Agent 基准上具备竞争力

[@MiniMax_AI](https://x.com/MiniMax_AI/status/2061266317815296322) · 8,174 赞

宣称是首个同时具备三项前沿能力的开源模型：**59.0% SWE-Bench Pro**、**66.0% Terminal Bench 2.1**、**34.8% SWE-fficiency**，稀疏注意力将上下文扩展到 1M，训练之初即原生多模态。一个达到这些数字的开源模型从根本上改变了自托管 coding agent 的成本方程式。权重和技术报告预计 10 天内发布。

### JetBrains Mellum2-12B：2.5B 激活参数的 MoE 支持工具调用

[@vllm_project](https://x.com/vllm_project/status/2061621691995005301) · 322 赞

12B MoE 仅激活 2.5B 参数，配备推理解析器和 agentic 工作流工具调用，从 day 0 原生运行在 vLLM，128K 上下文。这种模型让"Agent 在你笔记本上本地运行"越来越可行——激活参数量足够小以适配消费级硬件，同时保持 agentic 任务所需的能力。

---

## 论文精选：技能学习复兴

本周出现了一波不寻常的 Agent 技能学习论文——研究界已将技能获取和进化确定为 Agent 架构的下一个前沿。

### SkillEvolver：技能学习作为一种元技能
Zhang, Zhu, Zhou 等 · 2026年5月 · [Paper](https://www.semanticscholar.org/paper/d54fb47cb1bc0643d6126e96506537f9297737fc)

本批论文中最新颖的概念：**会学习的技能。** 目前大多数 Agent 技能是静态的——创建一次，不变地消费。SkillEvolver 提出轻量级的即插即用解决方案，技能从执行反馈中自我改进，无需重新训练模型。将"学习技能的能力"本身视为一种元技能。如果规模化可行，它将闭合 Agent 部署和能力增长之间的循环。

### EvoAgent：分层委托 + 进化式技能
Zhang, Guo, Jia 等 · 2026年4月 · [Paper](https://www.semanticscholar.org/paper/b1e1398ec1f6edaa64ed5c56e1cf8fbfc804cf1a)

本批论文中架构最完整的作品：技能被建模为**多文件结构化能力单元**，具有触发机制和进化元数据。父 Agent 向子 Agent 委托任务，每个子 Agent 配备进化的技能。这直接映射了生产系统中使用的 Agent 委托模式（Codex、Hermes、Claude Code），但通过进化式跟踪将委托层级形式化了。

### Skill-R1：用 RL 进化技能（无需重训练）
Vishe, Surana, Jiang 等 · 2026年5月 · [Paper](https://www.semanticscholar.org/paper/7cb626df241b6f8f4d69e65e7013d05bd5987d46)

将技能视为一等 RL 可优化对象。与通过 prompt engineering 或模型微调来改进技能（成本高且依赖特定模型）不同，Skill-R1 直接将强化学习奖励应用于技能过程。这解耦了技能改进和模型改进——可以在不触及底层 LLM 的情况下进化技能。

### SkillLearnBench：你的 Agent 真的能学会技能吗？
Zhong, Lu, Ning 等 · 2026年4月 · [Paper](https://www.semanticscholar.org/paper/f73d5cb01fb23264ba211fb6305586ed7b31d61c)

首个针对**持续技能学习**的基准测试——Agent 的技能系统是否真的随时间起作用，还是遭受灾难性遗忘？评估技能获取、保持和跨任务迁移。如果技能是 Agent 可扩展性的未来，我们需要衡量它们的实际效果——这个基准提供了标尺。

### ARISE：分层 RL 与内源技能进化
Li, Miao, Qi 等 · 2026年3月 · [Paper](https://www.semanticscholar.org/paper/5ed5c5334488838e7e1267c8f35b7b4926eebde0) · 10 引用

Agent 在数学推理训练中进化可复用技能，而不是孤立处理每个问题。技能在问题实例间积累和迁移——RL 版的可迁移子程序学习。

### WebXSkill：弥合 Web Agent 的接地鸿沟
Wang, Wu, Zhang 等 · 2026年4月 · [Paper](https://www.semanticscholar.org/paper/f283b199100a8d976d1f3b6a8561f7597c6a068e) · 5 引用

Web Agent 技能面临"接地鸿沟"：文本工作流技能提供自然语言指导，但缺乏可靠浏览器自动化所需的 DOM 级接地。WebXSkill 直接解决这一瓶颈。

### 从历史到状态：隐私保护式技能压缩
Xie, Wang, Wang 等 · 2026年5月 · [Paper](https://www.semanticscholar.org/paper/a676d0f60a6d68da5b919c7dbbb0534bd395f13e)

将长 Agent 交互历史压缩为恒定上下文的技能表示。解决个人 Agent 的隐私-能力矛盾：云端模型强大但暴露敏感中间上下文。从历史到紧凑状态的技能蒸馏使隐私保护式执行成为可能，且不损失质量。

---

## 热门趋势

### CVPR 2026：Agent 空间智能
[@ChenSiyich](https://x.com/ChenSiyich/status/2061675029964992546) · 29 赞 · NVIDIA/UMich

VLM 自适应使用视觉工具，并将机器人视为空间推理和物理操作的工具。"机器人即工具"的抽象在架构上具有重大意义——将 agentic 工具使用模式应用于物理系统，统一虚拟和物理工具使用。海报：ExHall A 87，6月7日周日 2:30 PM PDT。

### 投研 Agent：将专业知识蒸馏为流水线
[@kaikaibtc](https://x.com/kaikaibtc/status/2061393702258667968) · 2,657 赞

详细拆解两位专业投资者的方法论如何蒸馏为自主投研 Agent。架构高度领域化：流水线设计、BOM 级供应链分析、人在回路决策。"AI 负责搬砖，人负责最后拍板"——一个务实的 Agent 架构模式，强调增强而非替代。

---

## 新星崛起

### Pi Agent：反单体 coding agent
[@teach_fireworks](https://x.com/teach_fireworks/status/2061804587833786629) · 仅 1 赞

开源极简终端 coding agent。核心理念明确：核心保持小巧，所有复杂性交给扩展、技能、提示和包。内置多模型支持。仅 1 赞——几乎可以确定是未被发现的宝藏，但其架构理念（模块化、可 hack、多模型）正是社区正在靠拢的方向。

### Coding Agent 横评：Codex App > Cursor > Claude Desktop
[@dotey](https://x.com/dotey/status/2061569125948760319) · 47 赞 · 96 回复

一手详细对比：**Cursor 的 multitask 模式**支持并行后台任务（Codex 和 Claude Code 做不到）。**Plan 模式 + multitask** 组合稳定。但 Cursor 仍缺少 /goal、手机版和 Codex 的 Chrome/Computer use 调试功能。结论："规划 + 并行执行"正在成为三大平台的制胜架构模式。

### 中文社区亮点：WorkBuddy 技能生态
[@gengdaJ](https://x.com/gengdaJ/status/2061427044571861476) · 925 赞

WorkBuddy 技能商店涵盖飞书、Google Workspace、Obsidian、QQ邮箱、腾讯文档、公众号自动发布、小红书助手、Twitter 内容抓取，甚至 12306 和网约车。这种深度平台整合模式与西方的 MCP 开放协议路径形成鲜明对比——两者各有优势。

### Grok CLI：为 Agent 提供 X 搜索能力
[@gkxspace](https://x.com/gkxspace/status/2061413377461596526) · 347 赞

新增 4 个 X 搜索工具：关键词搜索（完整支持高级操作符）、语义搜索、用户搜索、线程抓取。全部本地运行，Agent 随时调用。这是一个平台级布局——标准 Agent 能获取 X 数据层的一等访问权。

---

## 值得关注

- **Codex 语音求助** — Codex 被阻塞时主动用语音请求人类帮助。Agent 主动交互是未被充分探索的 UX 前沿。 [@steipete](https://x.com/steipete/status/2061574752574283858) · 975 赞
- **Hermes Agent 桌面版** — Mac/Windows/Linux 官方桌面端发布，支持 OpenClaw 迁移。 [@hisevenih](https://x.com/hisevenih/status/2061755140697411918) · 325 赞
- **CC Switch v3.16.1** — 用第三方模型（DeepSeek 等）运行 Codex，同时保留官方功能。 [@blueskylh1](https://x.com/blueskylh1/status/2061488980353536457) · 247 赞
- **Cursor Composer 2.5 反代** — 为任意 Agent 提供 Cursor 专有模型。 [@dingyi](https://x.com/dingyi/status/2061735326348169530) · 159 赞
- **longflow** — Codex 可视化工作流工具插件。 [@aronhouyu](https://x.com/aronhouyu/status/2061430368759353560) · 315 赞
