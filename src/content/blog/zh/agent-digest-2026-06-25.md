---
title: "智能体架构每日摘要 — 2026年6月25日"
description: "循环工程（Loop Engineering）正在取代提示工程，成为智能体构建者的核心优化单元；记忆层让循环在运行之间不遗忘。Sakana Fugu 把多智能体编排产品化为单一计费 API，用 RL 训练的路由器跨模型调度。Claude Code 发布一等公民的 Agent Swarms；Claude Code Routines 让「智能体即定时任务」模式正式化。一篇新论文逆向工程 Claude Code 的 TypeScript 源码，产出智能体外壳设计分类法。计算机使用研究集群（MACU、Agent Alpha、SHERLOC、SAFARI）收敛到多智能体任务分解与树搜索。"
pubDate: "2026-06-25"
lang: zh
tags: ["Agent", "LLM", "AI Architecture", "Daily Digest"]
---

## TL;DR / 今日概览

> 今天最值得关注的 10 件事：

1. **循环工程是今天的主导模式——循环取代提示，成为构建者优化的单元。** 至少六个独立帖子收敛到同一个观点：设计驱动智能体的循环，而不是直接提示智能体。Evy Borov 给出了「让循环不失控的四层架构」；mem0 把循环和记忆层直接绑定——「循环在运行之间永不遗忘」；HadrianVeidt0 重新定义了成本模型——「AI 编码里最贵的不再是写代码，而是管理智能体循环」。— [Evy Borov](https://x.com/evyborov/status/2066905466853118376)、[mem0](https://x.com/mem0ai/article/2067305118891163833)

2. **Sakana Fugu 把编排产品化为单一 API。** Fugu 通过一个 OpenAI 兼容端点，用 RL 训练的 7B 参数路由器在 GPT-5、Claude、Gemini 之间调度。Fugu Ultra 据报告匹配或超越 GPT-5.5、Gemini 3.1 Pro、Opus 4.8——但它不是基座模型，而是一个协调式多智能体系统，按所使用的顶级模型计费。编排本身成为了产品。— [Sakana AI](https://sakana.ai/fugu-release/)

3. **Claude Code 发布一等公民的 Agent Swarms（「Teams」）。** 主导智能体现在可以委派给多个并行队友，这些队友各自研究、调试、构建，同时彼此协调。主导/队友委派模式把编码智能体从单智能体推向并行协调智能体——多智能体编排不再是外挂，而是工具内的一等原语。— [@bcherny](https://x.com/bcherny/status/2019472394696683904)

4. **Claude Code Routines 让「智能体即定时任务」模式正式化。** 通过 GitHub 事件或 API 触发模板化智能体；Anthropic 内部用它维护文档和待办事项。事件驱动的模板化智能体把定时、可恢复的智能体工作流正式化为受支持的原语。— [@noahzweben](https://x.com/noahzweben/status/2044093913376706655)

5. **一篇论文逆向工程 Claude Code 外壳，产出设计分类法。** 《Dive into Claude Code》（arXiv:2604.14228）分析 TypeScript 源码，与 OpenClaw 和 Hermes-Agent 交叉对比权限系统、上下文管理和可扩展性，归纳出十三条智能体外壳设计原则。今天内容中架构最严谨的一条。— [@burkov](https://x.com/burkov/status/2048233381305942381)

6. **终于超越扁平 AGENTS.md 的三层记忆架构。** 一个已部署的系统用热记忆宪法、19 个专业领域智能体和冷记忆知识库替代单一 AGENTS.md——把 CPU 缓存层级映射到智能体记忆上。对任何 AGENTS.md 已停止扩展的人直接可操作。— [@omarsar0](https://x.com/omarsar0/status/2027770787659464812)

7. **Agent Arena：用真实工具做真实智能体评测。** 数百万场实时会话，真实用户完成真实任务，模型被赋予网页搜索、文件系统和终端工具。这是缺失的基准层——用实时会话中的实际工具调用来衡量智能体能力，而非静态问答。— [@arena](https://x.com/arena/status/2062566749418233981)

8. **Cua Driver 让后台计算机使用登上 Windows。** 任何智能体（Claude Code、Codex、自定义循环）都可以通过 CLI 或 MCP 驱动真实的 Windows 应用。又一个应用面成为智能体可寻址的——而 CLI/MCP 集成意味着循环（见第 1 条）现在可以操作 Windows 软件。— [@trycua](https://x.com/trycua/status/2059688960838828391)

9. **智能体技能正在形成新的软件供应链——以及新的攻击面。** 新研究把模块化的智能体技能重新定义为供应链，揭示了构建者尚未为其构建工具的风险（投毒技能、依赖注入、信任边界）。安全正随循环一起沿栈上移。— [@FeiziSoheil](https://x.com/FeiziSoheil)

10. **多智能体计算机使用（MACU）：会修订 DAG 的 CUA 管理器。** 来自 CMU（Koh、Salakhutdinov、Fried），一个管理 LLM 将任务分解为子任务 DAG，调度并行计算机使用子智能体，并随着结果到来持续修订 DAG。DAG 修订循环是对静态任务分解的实质性推进。— [Semantic Scholar](https://www.semanticscholar.org/paper/ff065ba9442e7dfd4c77e8d3d752b5697175875e)

📊 今日数据：**6 条公司动态 | 8 条行业领袖 | 5 条热门 | 1 颗新星 | 10 篇论文（5 篇详解）| 36 条短讯 | 共 66 条**

---

## 当日模式：从提示到循环——以及编排成为产品

把今天的合集当作一个信号加一组佐证来读。信号是 **循环工程**：至少六个独立帖子达成共识——循环，而非提示，才是智能体构建者现在优化的单元。

Evy Borov 直接命名了这门学科：[Master loop engineering](https://x.com/evyborov/status/2066905466853118376)——「让循环不失控的四层架构」。[mem0](https://x.com/mem0ai/article/2067305118891163833) 把循环连到记忆层：「你不该再手动提示编码智能体了，你该设计提示它们的循环」——而循环的状态在迭代之间持久化。[cyrilXBT](https://x.com/cyrilXBT/article/2068850474384609543) 和 [sairahul1](https://x.com/sairahul1/article/2064277888216555684) 都把循环定义为「每一个真正能扩展的 AI 系统背后那个安静的核心技能」，循环运行在你自建的框架内且「在运行之间永不遗忘」。[HadrianVeidt0](https://x.com/HadrianVeidt0) 重塑了成本模型：「AI 编码里最贵的不再是写代码，而是管理智能体循环。」而 [titus_k](https://x.com/titus_k) 捕捉了元趋势：2026 年智能体构建者的关键问题不再是*调用哪个模型*，而是*如何围绕模型来架构系统*。

**串联它们的逻辑：提示工程优化的是单次调用；循环工程优化的是产生并从多次调用中学习的结构。** 由此衍生出三个具体转变：

- **循环需要护栏，不只是好的提示。** Borov 的「四层」和技能的供应链框架指向同一个生产失效模式：无界循环是破坏性的。安全已从「提示是否安全？」移到「循环是否受限？」
- **循环需要记忆。** mem0 的全部主张就是：没有持久记忆的循环工程意味着每天早上都要重新解释一切——这恰恰是 [PrajwalTomar](https://x.com/PrajwalTomar_/status/2066195642997969255) 为基于会话的智能体指出的痛点。三层记忆架构（热/温/冷）是设计上的答案。
- **循环正在成为原语，而非手搓脚本。** Claude Code Routines（事件触发的模板化智能体）和 Claude Code Teams（内置多智能体委派）都把编排从临时 shell 脚本搬进了一等工具功能。

今天的第二条线索是 **编排成为产品**。Sakana Fugu 是最清晰的例子——一个多智能体系统作为单一模型 API 出售，路由器是新颖组件，你按所用顶级模型计费。当编排本身成为产品而非自己搭建的层，竞争前沿就从「哪个基座模型」转移到「哪种编排质量」，各模型之上的智能体 OS 抽象层开始变得真实。

第三条线索——更安静但值得追踪——是 **计算机使用研究集群**。MACU（会修订 DAG 的多智能体 CUA）、Agent Alpha（复用部分成功的树搜索）、SHERLOC（把智能体预算中被浪费约 50% 的定位故障步骤结构化）、SAFARI（面向长时程运行的主动调查式故障归因）都在把 CUA 架构推向多智能体分解、测试时搜索和更好的调试。盯住这个集群——下一次 CUA 的实用收益将从这里来。

---

## 公司动态

### Sakana Fugu：编排即单一计费 API

[**Fugu**](https://sakana.ai/fugu-release/) 通过一个 OpenAI 兼容 API 暴露一整套多智能体系统。一个用强化学习训练的 7B 参数路由器决定为每个子任务调用哪个基座模型（GPT-5、Claude、Gemini）。Fugu Ultra 据报告在基准上匹配或超越 GPT-5.5、Gemini 3.1 Pro、Opus 4.8——但关键洞察是 Fugu Ultra *不是*基座模型，而是一个协调式多智能体系统。Sakana 按协调期间使用的顶级模型收费。[早期 Beta 申请](https://sakana.ai/fugu-beta/)已开放。

**为何重要：** 这是「各模型之上的智能体 OS」论点的商业化实现。路由模型本身——一个 RL 训练的选择器——是新颖的架构组件。撇开自报基准不谈，真正的赌注是：对于智能体产品，*编排质量可能比原始模型分数更重要*。（1000+ 赞）

### Claude Code Teams：一等公民的智能体集群

Claude Code 现在支持 [「Teams」，即 Agent Swarms](https://x.com/bcherny/status/2019472394696683904)：主导智能体委派给多个并行队友，这些队友各自研究、调试、构建，同时彼此协调。这把多智能体编排从外挂模式变成编码工具内的一等原语。

**为何重要：** 主导/队友委派模式是软件工程领域一个具体的集群架构——从单智能体到并行协调智能体的转变发生在*工具内部*，而非外部编排器中。

### Claude Code Routines：「智能体即定时任务」模式正式化

[Claude Code Routines](https://x.com/noahzweben/status/2044093913376706655) 让你通过 GitHub 事件或 API 触发模板化智能体。Anthropic 内部用它维护文档和待办事项。这把事件驱动、定时、可恢复的智能体工作流正式化为受支持的原语。

**为何重要：** 「智能体即定时任务」模式——定时的、模板化的、事件触发的——一年来一直是 DIY 黑科技。Routines 让它名正言顺，而 GitHub 事件触发把智能体循环直接绑到开发工作流上。

### Agent Arena：用真实工具做智能体评测

[Agent Arena](https://x.com/arena/status/2062566749418233981) 通过数百万场实时真实用户会话来衡量智能体表现，真实用户完成真实任务。模型被赋予网页搜索、文件系统和终端工具，从而评估实际的智能体任务完成能力，而非静态基准。

**为何重要：** 静态问答排行榜不会告诉你一个智能体能否*做事*。用实时会话中的真实工具调用来做的智能体评测，是缺失的基准层——聊天机器人分数和真实智能体效用之间的鸿沟。

### Cua Driver：后台计算机使用登上 Windows

[Cua Driver](https://x.com/trycua/status/2059688960838828391) 把后台计算机使用带到 Windows：任何智能体（Claude Code、Codex 或自定义循环）都可以通过 CLI 或 MCP 集成驱动真实的 Windows 应用。

**为何重要：** 又一个应用面成为智能体可寻址的——值得注意的是通过 CLI/MCP，这意味着循环（见当日模式）现在可以编程式地操作 Windows 软件，而非仅通过屏幕交互。

### mem0：作用于记忆的循环工程

[mem0](https://x.com/mem0ai/article/2067305118891163833) 引入了明确作用于智能体记忆的「循环工程」：设计驱动智能体的循环而非手动提示，且循环迭代间有持久记忆。

**为何重要：** 把循环工程直接连到记忆层，展示了编排循环和状态持久之间的架构关联——循环在运行之间永不遗忘，这恰恰是基于会话的智能体留下的缺口。

---

## 行业领袖

### Omar Khattab：投资技能而非提示，才能最大化 Claude Code

[Omar Khattab](https://x.com/omarsar0/status/2006390906371629222)：要最大化 Claude Code，投入时间构建子智能体、技能、命令、规划、MCP 工具和上下文工程模式——它们会产生巨大差异，而且所有工作流都可以迁移到 Codex。

**为何重要：** 来自一位知名从业者的明确信息——智能体编码的杠杆来自可复用的技能/子智能体和上下文工程，而非单次提示——而且模式是跨工具可移植的。

### 终于超越 AGENTS.md 的三层记忆架构

[Omar Khattab](https://x.com/omarsar0/status/2027770787659464812)：扁平的 AGENTS.md 文件在中等规模代码库之外无法扩展。替代方案——来自一个已部署的生产系统（论文《A Three-Tier Memory Architecture for Persistent AI Assistants》）——是热记忆宪法、19 个专业领域智能体和冷记忆知识库。热/温/冷的划分直接映射到 CPU 缓存层级。

**为何重要：** 今天内容中架构最具体的一条——一个真实的部署，展示如何从扁平指令文件走向分层记忆加专业智能体。直接应对长时程助手中的上下文窗口和会话压缩失效问题。

### 《Dive into Claude Code》：外壳的学术分类法

[Andriy Burkov](https://x.com/burkov/status/2048233381305942381) 引出了 [arXiv:2604.14228](https://arxiv.org/abs/2604.14228)，该论文逆向工程 Claude Code 的 TypeScript 源码，并与 OpenClaw 和 Hermes-Agent 交叉对比。论文识别出五个核心价值和十三条设计原则，审视了权限系统、上下文管理和可扩展性机制。配套仓库（VILA-Lab/Dive-into-Claude-Code）映射了更广的智能体设计空间，包括记忆系统、外壳扩展、MCP 生态和专业智能体。

**为何重要：** 今天最深的架构内容——对智能体外壳设计（智能体循环、子智能体上下文隔离、钩子系统、工具/MCP 作用域）的严谨分类法，对任何设计自己外壳的人直接有用。

### Sam Bhagowalia：使用 Claude Code 和 Codex 的上千小时

[calcsam](https://x.com/calcsam/status/2065222716609970680) 提炼了上千小时使用 Claude Code 和 Codex 构建的经验。关键教训：在不降低质量的情况下运行长会话，并通过 MCP 服务器、技能和插件来扩展你的智能体。

**为何重要：** 从业者对会话耐久性和长时程质量的强调，加上插件/MCP/技能组合——真实智能体编码的实际运营关切。

### 三种行动模式：工具、Bash、代码生成

[ethanolivertroy](https://x.com/ethanolivertroy/status/2030654868906459480) 记录了用 Claude Agent SDK 构建 GRC（治理、风险、合规）智能体的过程，使用了三种行动模式：**工具**（带类型化 schema 的原子操作）、**Bash**（可组合、灵活、上下文成本最小）和**代码生成**（动态逻辑生成）。

**为何重要：** 工具/Bash/代码生成的三模式分类法是选择智能体执行策略时一个真正有用的设计模式。尤其代码生成——动态逻辑作为一种行动类型——在固定工具之外仍未被充分探索。

### Evy Borov：让智能体循环不失控的四层架构

[Evy Borov](https://x.com/evyborov/status/2066905466853118376)：「掌握循环工程——设计智能体循环的学科，包括让循环不失控的四层架构」（变得无界或具破坏性）。

**为何重要：** 针对生产安全的具体架构指导。防止失控循环是一个大多数构建者尚未解决的真实生产失效模式——命名四层约束是立即可用的。

### 智能体技能作为新的软件供应链

[Soheil Feizi](https://x.com/FeiziSoheil) 引出新研究：随着 AI 智能体变得模块化，它们的「技能」正在形成一条新的软件供应链——对智能体框架提出了供应链安全和依赖管理问题。

**为何重要：** 把智能体技能重新定义为供应链，揭示了一整类构建者尚未为其构建工具的风险（投毒技能、依赖注入、信任边界）。一个新颖的安全架构视角。

### Priya Vergadia：完整的 Hermes Agent 配置指南

[Priya Vergadia](https://x.com/pvergadia/article/2066962737427841383) 发布了一份完整的 Hermes Agent 配置指南——一个通过基于 Markdown 的技能和 cron 调度自主行动的 AI 智能体，所以「在你喝咖啡之前，你的 AI 智能体已经行动了。」

**为何重要：** Hermes Agent 的技能/cron/定时任务模型是「你睡觉时也在行动的智能体」模式的一个具体实现——正是 Claude Code Routines 现在正式化的同一个原语（见公司动态）。

---

## 热门

### Sakana Fugu「一个模型统领一切」

[1000+ 赞热门](https://sakana.ai/fugu-release/)：Fugu 是一个作为单一 OpenAI 兼容 API 交付的多智能体编排系统，动态编排前沿模型。Fugu Ultra 据报告匹配/超越 GPT-5.5、Gemini 3.1 Pro、Opus 4.8。评论指出，对于智能体产品，编排质量可能比原始基准分数更重要——而且 Fugu Ultra 不是基座模型，而是协调式多智能体系统。今天最重要的发布。

### 我知道的所有智能体工程技巧（2026 年 6 月）—— 9130 赞

[mvanhorn](https://x.com/mvanhorn/status/2061978364391592110) 在一篇 91.3 万浏览量的帖子之后推出了 2026 年 6 月版：一份驾驭 Claude Code 和 Codex 的高速工作流指南。最亮眼的细节是每个会话都可以从 Claude 手机 App 访问——在办公桌前启动实时运行，在手机上恢复同一个运行。合集中互动最高的一条；记录了许多构建者正在迈向的那种鲜活智能体编码工作流。

### Claude Code 配置圣经（Anthropic 黑客松获奖作品）

[aiwithjainam](https://x.com/aiwithjainam/status/2028436830404944129)：一位 Anthropic 黑客松获奖者发布了一份完整的 Claude Code 配置圣经，覆盖智能体、技能、钩子、命令、规则和 MCP——经过 10 个多月实战检验，包括用 PM2 管理的多智能体编排。

**为何重要：** 一份经过实战检验的配置参考，覆盖 Claude Code 完整的可扩展面——对构建者直接可操作。

### 托管运行器上的多步智能体工作流

[DavidWells](https://x.com/DavidWells/status/2062338819178029306)：一个在 Netlify Agent 运行器上的多步智能体工作流，通过编排 Claude Code、Codex 和 Gemini 来生成功能创意，并合成可操作的下一步。

**为何重要：** 异构模型编排在托管智能体运行器上产生结构化输出的具体示例——编排正在搬到托管基础设施上。

### 编码智能体的会话记忆缺口

[PrajwalTomar](https://x.com/PrajwalTomar_/status/2066195642997969255)：基于会话的智能体（Claude Code、Cursor、Codex、OpenClaw）迫使构建者每天早上重新解释上下文；跨会话的持久上下文是缺口所在。

**为何重要：** 点名了驱动编码智能体中记忆层和技能趋势的精确痛点——即瞬态会话记忆。

---

## 新星

### Palmier Pro：一个智能体可以端到端驱动的 MCP 原生视频编辑器

[so_sthbryan](https://x.com/so_sthbryan/status/2068067293599350954)：Palmier Pro 是一个免费、开源的 macOS 视频编辑器，内置 MCP 服务器。把 Claude Code、Codex 或 Cursor 指向它，智能体就能编辑你的时间线——第一个智能体可以端到端驱动的真实视频编辑器。

**为何重要：** MCP 作为应用接口的清晰示例：应用把自己暴露为智能体操作的工具面，把智能体可控面扩展到创作软件中。

---

## 论文

### 多智能体计算机使用（MACU）—— 会修订 DAG 的 CUA 协调

[Semantic Scholar](https://www.semanticscholar.org/paper/ff065ba9442e7dfd4c77e8d3d752b5697175875e) · CMU（Koh、Salakhutdinov、Fried）· 代码：[github.com/kohjingyu/multi-agent-computer-use](https://github.com/kohjingyu/multi-agent-computer-use)

一个管理 LLM 从用户请求创建子任务 DAG，为每个子任务调度并行的计算机使用子智能体，并在子智能体报告发现时*持续修订 DAG*。论文论证单串行智能体 CUA 对于复杂长时程任务是次优的。

**为何重要：** DAG 修订循环——基于实时发现更新计划——是对静态任务分解的实质推进，也是计算机使用智能体的一次重大架构转变。

### SHERLOC：面向代码修复智能体的结构化故障定位

[arXiv:2606.24820](http://arxiv.org/abs/2606.24820v1)

SHERLOC 是一个免训练框架，重新定义了代码修复智能体的故障定位。它不把定位当作文件检索，而是产生可操作的诊断输出——回应了一个观察：LLM 智能体解决仓库级编码任务时，约 50% 的预算花在编辑前的定位故障上。

**为何重要：** 如果编码智能体把一半预算浪费在只是找到*修哪里*上，结构化定位就是直接的效率提升。把定位从检索变成诊断。

### SAFARI：面向长时程运行的主动调查式故障归因

[arXiv:2606.24626](http://arxiv.org/abs/2606.24626v1)

SAFARI 用工具增强的诊断循环替代把完整智能体轨迹塞进上下文的做法，该循环主动调查并定位长多步智能体运行中的失败步骤，比先前方法高出 20%。

**为何重要：** 随着智能体处理跨越数百步的任务，调试哪一步出错是顶级的工程痛点。SAFARI 把故障归因从工程师工时转移到推理开销——对于超出上下文窗口的运行，是必不可少的智能体运维工具。

### ToolPro：作为灵活智能体 Web 服务接口的工具程序

[arXiv:2606.19992](http://arxiv.org/abs/2606.19992v1)

ToolPro 把智能体的工具意图表示为可执行的「工具程序」，用循环、条件、连接和重试来紧凑编码多步 Web 服务交互——替代了当前重复单次 API 调用的模式。

**为何重要：** 直接解决一个核心智能体架构痛点：编排多步工具工作流。从静态端点调用转向可执行工具程序，可能从根本上改变智能体与 Web 服务和 API 交互的方式。

### 为什么多步工具使用 RL 会崩溃——以及监督信号如何修复它

[arXiv:2606.26027](http://arxiv.org/abs/2606.26027v1) · 作为 **Tool-RL-Box** 开源（基于 verl-tool 和 veRL）

论文表明多步工具使用强化学习在训练中途灾难性崩溃，并引入监督（过程级）信号来稳定学习并防止崩溃。

**为何重要：** 用 RL 训练工具使用智能体是不稳定的——一个核心痛点。监督信号修复对任何微调函数调用模型的人都可操作。代码已开源。

### 更多值得追踪的论文

- **[Agent Alpha](https://www.semanticscholar.org/paper/3d53bb6020cf87a11d34176517acfb3c5265b53c)** —— GUI 智能体的树搜索，统一了生成、探索和评估；支持回归搜索以复用部分成功并从早期错误中恢复。智能体的测试时计算扩展。
- **[Beyond Function Calling / ToolBench-X](http://arxiv.org/abs/2606.25819v1)** —— 在不可靠工具环境（坏工具、延迟、对抗性响应）而非干净稳定假设下基准测试工具使用智能体。弥合实验室到部署的差距。
- **[自动化 SKILL.md 生成](http://arxiv.org/abs/2606.20363v1)** —— 三阶段流水线从 GUI 交互轨迹挖掘技能库，证明技能可以自动生成而非手写。验证了技能库方法并信号出对 SKILL.md 标准的收敛。
- **[计算机使用智能体的不确定性量化](http://arxiv.org/abs/2606.25760v1)** —— 跨 VLM 和 GUI 数据集对计算机使用智能体进行事后 UQ 基准测试，覆盖拒绝、校准和点击的空间安全区域。知道*何时不*点击和知道点哪里同样重要。
- **[给评分者打分](http://arxiv.org/abs/2606.24839v1)** —— 评估智能体数据分析系统的方法论，区分基于 LLM 评测中的真实分歧和评分者错误。对任何构建带 LLM 评审的智能体评测流水线的人都实用。

---

## 值得关注

- **[OpenAI Codex 智能体改进循环](https://developers.openai.com/codex)** —— 把 traces → evals → refine 循环定义为改进编码智能体的标准方法；Codex 现已在 Plus/Pro/Business/Enterprise 计划上可用。
- **OpenAI 合作伙伴网络** —— 三个交付层级、1.5 亿美元生态投资和 Codex 专项认证；信号出 OpenAI 的企业智能体交付押注。
- **[全局 AGENTS 文件](https://x.com/linuz90/status/2021534838466175225)** —— 将全局 AGENTS 文件软链接到 `~/CLAUDE.md` 和 `~/AGENTS.md`，保持 Claude Code、Codex、Gemini、Cursor 间行为一致。
- **[后台智能体会赢](https://x.com/johnlindquist/status/1935714164028084719)** —— 每任务一容器的 PR 智能体（Cursor/Codex）已处理大部分任务；未解决的是多智能体 PR/合并 UX。
- **[GPT-5.5、GPT-5.4、Codex 登陆 Bedrock](https://x.com/awscloud/status/2061564484523524302)** —— 前沿模型加 Codex 现已在 Amazon Bedrock 上 GA，支持自动扩展。
- **[MCP 作为跨厂商连接层](https://x.com/lenadroid/status/2064364987326550065)** —— Codex 和 Claude Code 都说 MCP，所以为一个写的连接器通常在另一个也能用；插件打包连接器和行为。
- **[Claude Code 之上的多智能体编排层](https://x.com/hasantoxr/status/2037963932204445836)** —— 5 种执行模式、32 个专业智能体，号称输出快 3-5 倍。
- **[Claude Architect 认证蓝图](https://x.com/sharyph_/status/2037393353478959336)** —— 权重：智能体架构（27%）：stop_reason 驱动循环、隔离子智能体上下文、编程式钩子；工具设计与 MCP（18%），每个智能体 4-5 个限定作用域工具。把共识最佳实践编码进正式课程。
- **[/agents 中的子智能体协议](https://x.com/sethlazar/status/2006214936603844668)** —— 通过子智能体协议用 Claude Code 做多智能体编排，为每个智能体提供其子任务所需的上下文。
- **[Visa 给 AI 智能体发卡凭证](https://x.com/sytaylor/status/2064957014879420456)** —— 与 OpenAI 合作做智能体商务；这是能自主交易的智能体的基础设施前提。
- **[厚技能、厚代码、薄外壳](https://x.com/garrytan/status/2045233931390484967)** —— Garry Tan（YC）分享 Steve Yegge 的框架：把复杂度推进技能和生成代码里，保持外壳最小化。用 AI 编码智能体的人是「10 倍到 100 倍」。
- **[为你自己构建第一个 AI 循环](https://x.com/cathrynlavery/article/2069193102586474781)** —— 观察真实的工具调用、shell 命令、失败、技能使用和修正时刻来改进智能体设计。附模板。
- **[循环：每个能扩展的 AI 系统背后那个安静的核心技能](https://x.com/cyrilXBT/article/2068850474384609543)** —— 提示是错误的衡量单元；循环才是关键的扩展单元，且循环的记忆在运行间持久。
- **[Clint Gibler 加入 OpenAI 安全](https://x.com/clintgibler/status/2064813665711444175)** —— 信号出 OpenAI 对智能体安全基础设施的投资（与技能即供应链主题相关）。
- **[管理智能体循环是最贵的事](https://x.com/HadrianVeidt0)** —— 把 AI 工程的成本模型从代码生成重塑为循环管理。
- **[问题不再是调用哪个模型](https://x.com/titus_k)** —— 对 2026 年的智能体构建者，关键问题是如何围绕模型来架构系统。
- **[循环：2026 年每个 AI 工程师都需要知道的](https://x.com/sairahul1/article/2064277888216555684)** —— 智能体在你自建的框架内循环，而记忆确保循环在运行间永不遗忘。
- **[超越静态排行榜：预测有效性](http://arxiv.org/abs/2606.19704v1)** —— 汇总 14 项针对一个基于 MCP 的工业智能体基准的并行研究；没有单一基准能触及超过 4-5 个部署维度。
- **[为 CUA 构建验证器的艺术](https://www.semanticscholar.org/paper/bba585a56bf7c9a861de68165a30c57c3f5b9388)** —— Web 任务的「通用验证器」；验证是智能体训练和评估的基础（Corby Rosset，微软研究院）。
- **[CLI-Anything：智能体原生的计算机使用](https://www.semanticscholar.org/paper/59b5ad8c186160eb04660b435468e6b1eabb6060)** —— 论证基于 CLI 的交互对智能体来说更可靠、更快、更便宜；CUA 领域可能在视觉方法上过度索引。
- **[IntentCUA：意图级技能抽象](https://www.semanticscholar.org/paper/ac9625ec0bda72b4c2185e57f0dfbcb0d647c8bc)** —— 学习意图级表示，实现技能抽象和多智能体规划，减少长时程上的错误累积。
- **[模型自适应多智能体 RAG](http://arxiv.org/abs/2606.25191v1)** —— 对 7B-9B 模型的免训练干预，通过 isolate-vs-score 策略使多智能体 RAG 成本高效。
- **[隐私保护的多智能体 RAG](http://arxiv.org/abs/2606.24623v1)** —— 一个多智能体框架通过语义重写清洗检索到的 RAG 内容，而不牺牲上下文保真度。
- **[Fara-1.5：CUA 的可扩展学习环境](http://arxiv.org/abs/2606.20785v1)** —— 可扩展的环境生成加自动化验证器，用于训练计算机使用智能体（Ahmed Awadallah，微软研究院）。
- **[智能体安全的盲区](https://www.semanticscholar.org/paper/1849c0ea146198831e17f1b3ccde57493b980442)** —— 良性用户指令在上下文被误解读时也可能导致 CUA 执行有害动作；把智能体安全扩展到提示注入之外。
- **[量化膨胀推理 token](http://arxiv.org/abs/2606.25519v1)** —— 推理模型的低比特量化膨胀了 token 数量，抵消了每个 token 的节省；应测量总 token 数，而非仅每 token 延迟。
- **[在 LLM 渗透测试中解耦侦察与利用](http://arxiv.org/abs/2606.25332v1)** —— 端到端黑盒评估掩盖了真实能力；阶段解耦评估揭示了智能体实际在哪里失败。
- **[电力系统智能体基准](http://arxiv.org/abs/2606.20950v1)** —— 电力工程中的可执行智能体评估；「检查动作后果」范式正在超越软件领域扩散。
- **[MedGuards：多智能体医疗错误检测](http://arxiv.org/abs/2606.25651v1)** —— 一个用于可靠输出验证的多智能体验证器/校正器模式，可推广到医疗之外。
- **[物理受限基座模型的智能体进化](http://arxiv.org/abs/2606.25532v1)** —— 一个多智能体发现引擎自主架构硬件兼容的设计。
- **[BrainAgent：多智能体脑信号理解](http://arxiv.org/abs/2606.25400v1)** —— 一个用于 BCI 应用中自主脑信号分析的多智能体 LLM 框架。
- **[当有用性凌驾于因果谨慎之上](http://arxiv.org/abs/2606.24839v1)** —— LLM 中因果推理的上下文依赖性抑制和恢复。

---

## 中文社区 / 知乎

来自中文来源（通过 `site:zhihu.com` 搜索收集）：

- **[大模型智能体(Agent)全解析：原理、架构、框架与实操指南](https://zhuanlan.zhihu.com/p/1999522578218390118)** —— 系统梳理智能体核心概念、PEAS 模型、智能体循环和提示工程，横向对比三种主流架构：ReAct、Plan-and-Solve 和 Reflection。
- **[2026年AI Agent技术全景：12大主流框架深度解析与架构演进趋势](https://zhuanlan.zhihu.com/p/2026254728342905724)** —— 框架综述覆盖 12 个主流智能体框架、核心架构、框架选型和演进趋势。把 2026 年定义为「智能体之年」——模型（GPT-4o、Claude 3.5）在推理和工具调用上已足够成熟。
- **[一文讲清！AI智能体到底是什么？](https://zhuanlan.zhihu.com/p/1961862896981094827)** —— 概念澄清：AI 智能体是否只是「换皮聊天机器人」，以及为什么它被称为下一代 AI 范式。
- **[Agent架构设计全解析：9大核心技术](https://zhuanlan.zhihu.com/p/1935734877472420257)** —— 面向企业的系统化架构设计：「LLM 是引擎；智能体架构是底盘和传动系统，决定了 AI 能走多远。」
- **[AI Agent开发路线图2025](https://zhuanlan.zhihu.com/p/1985379253525697644)** —— 学习路线图，其中 L3 阶段涉及多智能体系统（LangChain、LlamaIndex、AutoGPT、MetaGPT）。

---

*通过 X 关键词/公司搜索和网页搜索（arXiv、Semantic Scholar、知乎）收集。流水线：collect → prefilter → analyze（5 个分块）→ merge → synthesize。*
