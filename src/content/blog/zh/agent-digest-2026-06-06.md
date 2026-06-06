---
title: "AI Agent 架构日报 — 2026年6月6日"
description: "Anthropic 正式定义 Skills 为结构化文件夹、Harness Engineering 成为一门独立学科、写循环而非写提示词、云 Agent 与桌面 Agent 的安全边界、MCP 故障分类学、以及多 Agent 潜空间通信。"
pubDate: "2026-06-06"
lang: zh
tags: ["Agent架构", "Harness Engineering", "MCP", "Skills", "上下文工程", "AI日报"]
---

## TL;DR — 今日要点

1. **Anthropic 定义 Skills 为完整文件夹结构**：Claude Code 的 Skills 不是 Markdown 文件，而是包含指令、脚本、参考、模板、配置、Hooks 和持久化数据的目录——实现渐进式上下文披露（Progressive Disclosure），降低幻觉和 token 浪费。这是迄今最精确的 Agent 技能定义。([@mylifcc](https://x.com/mylifcc/status/2062892067169472616))
2. **Harness Engineering 成为独立学科**：awesome-harness-engineering 收录了 OpenAI、Anthropic、微软、Meta 的生产级实践——LangChain 仅靠改 Harness 就把 Terminal Bench 从第 30 拉到前 5，Azure SRE Agent 自主处理 35,000+ 起生产事故。([GitHub](https://github.com/ai-boost/awesome-harness-engineering) · [@FakeMaidenMaker](https://x.com/FakeMaidenMaker/status/2062843875132133656))
3. **Claude Code 创造者：我写循环，不写提示词**：Boris 展示了真实的日常开发流程——任务被分解为"执行→验证→修正"的循环，模型在其中持续推进而非一次性回答。437 赞，93K 浏览。([@Mikocrypto11](https://x.com/Mikocrypto11/status/2062792788706521333))
4. **云 Agent 与桌面 Agent 的安全边界**：Agent 一旦离开用户电脑，问题就从框架设计变成了基础设施契约。安全模型：假设沙箱已被攻破，长期有效的 secrets 永远不进入执行边界。([@anorth_chen](https://x.com/anorth_chen/status/2062756323985363359))
5. **首个 MCP Server 运行时故障分类学**：分析了 473 个活跃 MCP Server 的 837 个故障线程，发现"配置参数被接受但未强制执行"是最普遍的可靠性陷阱。([arXiv:2606.05339](http://arxiv.org/abs/2606.05339v1))
6. **超越 Token：多 Agent 潜空间通信**：提出 Agent 之间不通过自然语言而是通过隐空间表征进行通信的统一框架，从根本上质疑"Agent 应该用 Token 交流"这一默认假设。([arXiv:2606.05711](http://arxiv.org/abs/2606.05711v1))
7. **LLM Wiki 桌面版：Karpathy 持久知识模式的完整实现**：知识图谱 + Louvain 社区发现 + 本地 HTTP API + 一行命令安装 Agent Skill。([@gaoren7716](https://x.com/gaoren7716/status/2062859478429523980))
8. **GitHub Spec Kit（109K Stars）：先写规格再写代码**：一句话需求自动生成完整规格和验证标准，兼容 30+ 种 AI 编程 Agent，直接解决长编码会话中的 Agent 漂移问题。([@IndieDevHailey](https://x.com/IndieDevHailey/status/2062811394274373969))
9. **上下文工程有两半，不是一半**：Pocock 解决"记什么"（一手/二手来源权衡），Cichra 的 Loop 解决"怎么保证遵守"（git hooks → 拒绝 → 自动查文档 → 修复 → 重提）。大多数团队只做了前半段。([@yibie](https://x.com/yibie/status/2062837148051759152))
10. **ADK Arena：用 LLM 当开发者来评测 Agent 框架**：LLM 学每个框架的 API 文档然后写 Agent 代码——把开发者技能恒定，只变框架。首个系统化 Agent 框架基准测试。([arXiv:2606.05548](http://arxiv.org/abs/2606.05548v1))

📊 今日数据：**22 条深度解读 | 10 篇论文 | 34 条值得关注 | 144 条总分析**

---

## 行业领袖

### 1. Harness Engineering，不只是 Prompt Engineering

[@divaagurlxw](https://x.com/divaagurlxw/status/2062419864908951606) · 3,854 赞 · 202K 浏览

一篇被大量转发的帖子指出：AI 工程师必须从 prompt engineering 走向 harness engineering——KV 缓存管理、连续批处理、推测解码、量化权衡和延迟优化。3,854 赞和 202K 浏览量说明社区已经认识到这个转变。

这不仅仅是建议，而是整个社区对"围绕模型建设基础设施"这一核心命题的集体觉醒。Harness Engineering 正在成为定义现代 Agent 架构的关键学科。

### 2. Anthropic 官方定义 Agent Skills

[@mylifcc](https://x.com/mylifcc/status/2062892067169472616) · 96 赞 · 8.3K 浏览

Anthropic 发布了《Lessons from building Claude Code: How we use skills》。核心澄清：Skills **不是**单纯的 Markdown 文件，而是完整的文件夹结构，包含指令（SKILL.md）、脚本、参考资料、模板、配置、Hooks 和持久化数据（SQLite）。Agent 会发现、探索并操作整个文件夹结构，实现**渐进式上下文披露**——逐步喂入上下文而非一次性倾倒到 prompt 中。

这是迄今对 Agent 系统中"技能"最精确的定义。渐进式披露作为上下文管理模式，直接解决了幻觉和 token 浪费这两个生产级 Agent 系统的核心痛点。

### 3. awesome-harness-engineering：Harness Engineering 的参考合集

[GitHub](https://github.com/ai-boost/awesome-harness-engineering) · [@FakeMaidenMaker](https://x.com/FakeMaidenMaker/status/2062843875132133656) · 109 赞 · 13.8K 浏览

收录了 OpenAI、Anthropic、微软、Meta 的生产级 harness engineering 实践。关键案例：LangChain 仅靠重新设计 Harness 就把 coding agent 从 Terminal Bench 第 30 名提升到前 5；微软 Azure SRE Agent 自主处理了 35,000+ 起生产事故；Anthropic 的上下文工程和长任务设计指南被完整索引。

Harness Engineering 正在成为一门被认可的独立学科。这个仓库作为权威参考，用经过验证的生产案例展示了"正确地包裹模型"到底是什么意思。

### 4. Boris：写循环，不写提示词

[@Mikocrypto11](https://x.com/Mikocrypto11/status/2062792788706521333) · 437 赞 · 93.5K 浏览

Claude Code 的创造者 Boris 展示了真实的日常开发流程：不是写 prompt，而是设计循环。任务被分解为执行→验证→修正的循环，模型在其中持续推进而非给出一次性答案。核心转变是从"和 AI 聊天"到"设计让 AI 持续工作的系统"。

Claude Code 创造者的"写循环不写提示词"哲学，验证了 Agent 循环模式相对于对话式提示的根本优越性。这是关于如何思考 Agent 交互设计的范式转变。

### 5. Anthropic 递归 Agent 演示引发广泛讨论

[@Jackywine](https://x.com/Jackywine/status/2062771320883159452) · 536 赞 · 150K 浏览

一篇 Anthropic 文章中展示的递归/动画演示被广泛传播。观众感受到的"恐怖感"来自观看 Claude 展示自我迭代能力——Agent 通过递归执行来改善自身输出。

150K 浏览量表明，递归 Agent 行为——Agent 迭代自身输出——正作为一种独特能力进入主流认知，而不仅仅是增量改进。

### 6. Google DeepMind Jon Barron：研究者应该用 Agentic IDE

[@jon_barron](https://x.com/jon_barron/status/2062558102835355674) · 1,021 赞 · 201K 浏览

Google DeepMind 首席研究科学家公开建议博士生在 Agentic IDE 内工作，以 .tex 格式在代码库中写论文，并建议不要听从没有在现代 Agentic IDE 中累计 100+ 小时的导师的建议。

一位资深 Google DeepMind 研究员公开支持 Agentic IDE 作为必备研究工具，标志着 Agent 工作流正在从工程界跨越到学术研究界。

### 7. 云 Agent vs 桌面 Agent：架构分界线

[@anorth_chen](https://x.com/anorth_chen/status/2062756323985363359) · 105 赞 · 25K 浏览

Agent 一旦离开用户电脑，问题就从框架设计变成了基础设施契约。桌面 Agent 默认本地文件系统可信、环境变量中的 key 可信、用户在线、失败可以手动重试。而云 Agent 需要处理无人值守执行、共享硬件、prompt injection 风险，以及被 cron、API 和其他 Agent 调用的场景。Agent Runtime 未来会越来越像一个小型操作系统。

安全模型：假设沙箱已被攻破，长期有效的 secrets 永远不进入沙箱执行边界。让攻击者的收益难以覆盖时间和精力成本，他们自然会放弃。

### 8. Agent 学习路径：从最小循环到生产部署

[GitHub: datawhalechina/Agent-Learning-Hub](https://github.com/datawhalechina/Agent-Learning-Hub) · [@vintcessun](https://x.com/vintcessun/status/2062560005422031007) · 275 赞 · 13.3K 浏览

Datawhale 开源项目将 Agent 开发拆解为 8 个阶段——从构建最小 Agent 循环到部署生产级 Agent，每个阶段有可执行的 todo list。核心洞察：Agent 开发本质上是"观察-思考-执行"循环加上 Harness Engineering 对权限、状态和回溯的组织。

### 9. Grok Build 0.1：xAI 的并行 Coding Agent

[@grapeot](https://x.com/grapeot/status/2062901506115055996) · [yage.ai 分析](https://yage.ai/share/grok-build-0-1-20260605.html)

对 xAI 的 Grok Build coding agent 的详细技术分析，涵盖基准测试评估缺口、实际成本分析和 Grok Build 与其他 xAI 产品之间的隐私政策差异。少数几篇关于 xAI Agent 策略的实质性独立分析之一。

### 10. 上下文工程有两半

[@yibie](https://x.com/yibie/status/2062837148051759152) · 24 赞 · 3.1K 浏览

Matt Pocock 的上下文工程框架将信息分为一手来源（代码、原始对话）和二手来源（摘要、文档），管理信息丰富度和成本之间的权衡。但这只回答了"记什么"。

Michal Cichra（Safe Intelligence）在 AI Engineer 演讲中补上了后半段：Agent 写代码 → git push → git hooks 触发检查 → 被拒绝 → Agent 自动查找相关文档 → 理解原因 → 修复 → 重新提交。没有 Loop，文档就是装饰品——Agent 不会主动去读，人类也不会。

---

## 热门趋势

### 11. 多 Agent 公司架构：编排器 → 部门 → 专家

[@shannholmberg](https://x.com/shannholmberg/status/2062652746508173796) · 764 赞 · 143K 浏览

一个具体的多 Agent 公司生产架构：编排器 Agent（Hermes）→ 部门垂直 Agent → 专家 Agent → 受限子 Agent，共享的"gBrain"吸收所有机构知识（对话记录、营销活动、客户洞察、策略文档、优秀案例）。大脑由一名人类 Champion 加编排器 Agent 共同维护。

编排器→部门→专家模式加上共享机构记忆，是任何部署多 Agent 系统的组织都可以参考的架构模式。

### 12. Karpathy 的 LLM Wiki 桌面应用

[@gaoren7716](https://x.com/gaoren7716/status/2062859478429523980) · 89 赞 · 6.8K 浏览

Karpathy 提出的 LLM Wiki 概念——持久化、增量构建的知识库 vs. 每次查询从头检索的 RAG——被实现为完整的桌面应用。功能包括：两步式 Chain-of-Thought 摄入、多模态图片提取、4 信号知识图谱（相关性、来源重叠、Adamic-Adar、类型亲和度）、Louvain 社区发现、内置本地 HTTP API（127.0.0.1:19828）、一行命令安装 Agent Skill。

增量式持久知识构建比每次查询的 RAG 创造了更丰富的 Agent 上下文层。本地 API 和 Skill 集成使其可以立即用于 Agent 工作流。

### 13. html-video：Agent 成为多媒体制作人

[@tuturetom](https://x.com/tuturetom/status/2062470358687498470) · 2,787 赞 · 276K 浏览

开源工具让 coding agent 通过写 HTML 来创建生产级视频。20+ 风格模板、多页编辑、MP4 导出。支持 Claude Code、Codex、Hermes 和 Cursor 集成。Agent 作为创意制作者的模式表明，Skill 生态正在将 Agent 的能力边界从代码生成扩展到更广泛的领域。

### 14. GitHub Spec Kit：先规格后代码

[@IndieDevHailey](https://x.com/IndieDevHailey/status/2062811394274373969) · 129 赞 · 12K 浏览

GitHub 的 Spec Kit（109K+ Stars）强制执行结构化工作流：需求 → 计划 → 任务 → 代码。一句话需求自动生成带有验证标准和边界条件的完整规格。兼容 30+ 种 AI coding agent。直接解决长编码会话中 Agent 漂移的核心问题——第一版看着对，第二版开始偏，到第十次迭代 Agent 已经在解决一个完全不同的问题了。

### 15. BrowserAct：抗封禁的 Agent 浏览器自动化

[@GitHub_Daily](https://x.com/GitHub_Daily/status/2062746268993216772) · 236 赞 · 11.9K 浏览

开源 AI Agent 浏览器自动化工具，三层抗封禁：指纹伪装、验证码解决、AI 无法处理时的人类接管回退。隔离会话（每个任务独立的 cookie、指纹和代理），LLM 优化的输出格式比传统 HTML/JSON 节省数倍 token。内置 Skill Forge——自动探索网站结构并生成可复用的抓取脚本。

### 16. Superpowers：14 个 Agent 技能，含子 Agent 编排

[@Chenzeze777](https://x.com/Chenzeze777/status/2062388555557826843) · 363 赞 · 29K 浏览

14 个开源 Agent 技能，包括子 Agent 驱动的并行执行（每个任务独立子 Agent → 合并 → 评审）、测试驱动开发强制执行（红-绿-重构）、两阶段代码评审（子 Agent 互审 + 主 Agent 终审）。支持 Claude Code、Hermes、Codex 和 Windsurf。

子 Agent 驱动模式和 TDD 强制执行是重要的 Agent 工程模式，作为可安装技能被编码——最佳实践正在变成可复用组件。

### 17. 企业级 Agentic Workflow 分类学

[@teach_fireworks](https://x.com/teach_fireworks/status/2062721018809205018) · 40 赞 · 2.4K 浏览

定义了四个层级：增强型 LLM → 带 LLM 的工作流（静态、预定义）→ 工作流 Agent（Agent 执行预定义工作流）→ Agentic Workflow（基于反馈的动态规划、分解和调整）。Agentic 层使用 Blueprint Generator + Planner + Executor 架构。企业级重要性在于：既能大规模自动化，又能利用现有微服务和 API 作为 Agent 可调用的工具。

### 18. 硅谷开始收紧无限制 AI 工具使用

[@yupi996](https://x.com/yupi996/status/2062834426678231077) · 34 赞 · 10.5K 浏览

大型科技公司开始限制 AI 工具访问：Meta 的 85K 员工 30 天烧了 60T token（单个用户达到约 $500K/月），Uber 4 个月用完 12 个月预算，微软强制迁回 Copilot，亚马逊撤下内部排行榜（员工用无意义的 Agent 任务刷榜）。

企业对无限制 Agent 使用的反噬对部署策略有实际影响——成本管理和 ROI 衡量正在成为 Agent 大规模采用的关键课题。

### 19. Hermes 生态：共享记忆与 Token 压缩

[@XAMTO_AI](https://x.com/XAMTO_AI/status/2062655324046385531) · 446 赞 · 29K 浏览

Hermes 生态更新：桌面 GUI 客户端支持多平台、plur 共享记忆层使用开放 engram YAML 实现跨实例知识持久化、rtk-hermes token 压缩减少 60-90% 的 shell 输出 token（在 11M+ token 上验证）。

跨 Agent 实例的共享记忆和 token 优化都是生产级 Agent 系统的关键基础设施挑战。

### 20. HermesHub Skill 注册表与 Mnemo Cortex

[@GitTrend0x](https://x.com/GitTrend0x/status/2062888841623863564) · 159 赞 · 14.3K 浏览

HermesHub 提供社区 Skill 注册表（浏览、分享、安装），Mnemo Cortex 添加持久化记忆系统。多 Agent 桌面客户端配备 20 个专家 + PM 编排器。Skill 注册表模式——标准化的 Agent 能力发现和分发——表明生态系统正在发展标准基础设施。

---

## 新星崛起

### 21. "格局"技能：对抗 Agent 过度谨慎

[@hylarucoder](https://x.com/hylarucoder/status/2062949336150073413) · 146 赞 · 10.4K 浏览

一个名为"格局"的开源 Codex Skill，用来对抗 AI 的过度谨慎。使用"终局推理"（从期望结果反向推理）、"零遗留假设"（假装没有现有代码）和"反转约束"（翻转默认假设）来让 Agent 更大胆。

这是一个有趣的元模式：用技能来修改 Agent 的性格和行为特质，而非添加功能。展示了 Agent 行为调节这一新兴生态。

---

## 论文精选

### 22. MCP Server 运行时故障分类学

[arXiv:2606.05339](http://arxiv.org/abs/2606.05339v1) · Owotogbe, Kumara, van den Heuvel

首个对 MCP Server 可靠性的系统性研究。分析了 473 个活跃 MCP Server 的 837 个运行时故障线程。关键发现：配置参数被接受但未强制执行，导致非预期的默认值。为调试和加固 MCP 集成提供了可操作的分类学。

MCP 正在迅速成为 Agent 的标准工具协议，理解其故障模式对构建 MCP Agent 系统的每个人来说都是必须的。

### 23. 超越 Token：多 Agent 系统中的潜空间通信

[arXiv:2606.05711](http://arxiv.org/abs/2606.05711v1) · Yingzhuo Liu

提出了 LLM Agent 之间隐空间（非 Token）通信的统一框架，挑战了自然语言作为 Agent 间通信介质的统治地位。如果 Agent 可以在不进行 Token 化的情况下共享表征，多 Agent 协调将变得远比现在高效。

从根本上重要——质疑 Agent 是否应该通过 Token 交流，提出可能重塑多 Agent 系统设计的潜空间通道。

### 24. MLEvolve：自进化 Agent 框架

[arXiv:2606.06473](http://arxiv.org/abs/2606.06473v1) · Du, Yan, Shi

LLM Agent 通过持续的多分支搜索发现 ML 算法，具备记忆和跨分支信息共享。解决长时序 Agent 任务的关键挑战：自进化、跨分支记忆和探索路径之间的信息共享。

### 25. ADK Arena：Agent 开发工具包基准测试

[arXiv:2606.05548](http://arxiv.org/abs/2606.05548v1) · Huang, Li, Mittal

使用 LLM-as-a-Developer 方法论：一个 LLM coding agent 从文档学习每个框架的 API，编写 Agent 代码，通过验证-反馈迭代。生成努力成为框架可用性的量化代理。首个把开发者技能恒定、只变框架的系统化基准测试。

### 26. Agent 应该说什么？动作-状态通信

[arXiv:2606.05304](http://arxiv.org/abs/2606.05304v1) · Huang, Wu, Zhang

分析了 2 种 MAS 拓扑中的 5 种 Agent 间通信策略。关键发现：自由格式自然语言膨胀 token 且损害性能。动作-状态通信（做了什么 + 产生了什么状态）始终比传递原始推理轨迹或摘要更高效。

直接挑战了在 Agent 间传递自由文本的默认模式。结构化的动作-状态通信减少 token 使用并提高性能。

### 27. CLI-Anything：Agent 原生计算机使用

[arXiv:2606.03854](http://arxiv.org/abs/2606.03854v1) · Yang, Fan, Huang

主张通过 CLI 而非 GUI 进行 Agent 原生计算机使用，为 LLM Agent 提供更可靠和结构化的交互界面。挑战了 GUI Agent 的主流范式——CLI 交互可能更稳健、更便宜、更快。

### 28. ToolChoiceConfusion：因果最小工具过滤

[arXiv:2606.06284](http://arxiv.org/abs/2606.06284v1) · Babu, Iyer

提出因果最小工具过滤（CMTF）——一种免训练方法，通过因果充分性而非语义相关性选择工具。一个工具可能相关但在当前步骤不必要。使用轻量级因果分析过滤到最小充分工具集。

直接解决了工具菜单膨胀导致混乱这一核心 Agent 工程问题。因果充分性的框架很新颖且可以立即应用。

### 29. 从 Agent 轨迹到信任：证据追踪与执行溯源

[arXiv:2606.04990](http://arxiv.org/abs/2606.04990v1) · Wang, Zhang, Cai

提出证据追踪和执行溯源作为 LLM Agent 的一等原语。建模证据如何流经工具调用、检索、记忆模块和 Agent 间通信。实现调试、审计和信任验证。

无法调试或审计你无法追踪的东西。执行溯源是 Agent 输出和生产系统可信度之间缺失的那一环。

### 30. SafeMCP：MCP Agent 的主动权限调节

[arXiv:2606.01991](http://arxiv.org/abs/2606.01991v1) · Wang, Ren, Yang

为 MCP Agent 引入主动防御机制，使用环境接地的预见推理来防止权力寻求行为。随着 MCP 采用的增长，针对扩展行动空间中 Agent 权力寻求的安全护栏变得至关重要。

### 31. CollabSim：多 Agent 系统的协作能力研究

[arXiv:2606.06399](http://arxiv.org/abs/2606.06399v1) · Chen, Sun, Lu

提出基于 CSCW 的多 Agent 协作受控实验方法论。关键洞察：MAS 的失败不是来自个体能力不足，而是来自协作崩溃。提供了衡量和诊断这些失败的严谨实验框架。

---

## 值得关注

- **六种基础 Agent 工作流模式** — 分类路由、扇出合成、对抗验证、生成过滤、锦标赛、循环直到完成。[@MinLiBuilds](https://x.com/MinLiBuilds/status/2062902783595147544) · 45 赞
- **Codex 并行子线程处理大型 PR 队列** — 车道隔离、只读审查者、可写文件约束。[@mylifcc](https://x.com/mylifcc/status/2062958224157098009) · 12 赞
- **Kimi Work：300 Agent 并行处理办公任务** — 月之暗面的桌面 Agent 最多调度 300 个 Agent 处理文档、PPT、表格和浏览器自动化。[@xiaohu](https://x.com/xiaohu/status/2062824756634931256) · 107 赞
- **"碳基分析硅基想法感觉很奇怪"** — NLP 研究者感叹 Auto Research + Coding Agent 流水线已经让大多数 AI 论文实际上是 AI 生成的。[@dongxi_nlp](https://x.com/dongxi_nlp/status/2062871242453991483) · 237 赞
- **个人 + Agent 跑赢团队** — 组织设计挑战：一个人带着 Agent 完成工作比团队协调还快。[@guansi](https://x.com/guansi/status/2062876570982006961) · 39 赞
- **"存储便宜→Gmail。带宽便宜→YouTube。智能便宜→？"** — 框架化近零智能成本的必然产物。[@jianshuo](https://x.com/jianshuo/status/2062612487221194971) · 1,701 赞
- **Claude Design 暴露了 Anthropic 的多模态短板** — 设计工具揭示了模型视觉能力的不足。[@hwwaanng](https://x.com/hwwaanng/status/2062824009864294660) · 52 赞
- **AlphaEvolve 拆解：NAS 搜索 + LLM 语义理解** — LLM 提议，进化框架选择。[@grapeot](https://x.com/grapeot/status/2062626694503292972) · 8 赞
- **Coding Agent 将论文发表速度提升 1.75-2x** — 博士生对 Agent 影响研究生产力的量化观察。[@dviolettchan](https://x.com/dviolettchan/status/2062712073562337539) · 156 赞
- **MCP Server 描述-代码不一致制造安全漏洞** — 工具描述与实际实现分歧。[arXiv:2606.04769](http://arxiv.org/abs/2606.04769v1)
- **Agentic Redux：从类型 Lambda 演算到可证明可审计的 Agent** — 罕见的 Agent 正确性形式化验证方法。[arXiv:2606.04903](http://arxiv.org/abs/2606.04903v1)
- **多步工具使用的 RL 训练** — 合成匹配真实服务器状态的有状态训练查询。[arXiv:2606.03892](http://arxiv.org/abs/2606.03892v2)
- **MCP 原生图规划用于生物医学 Agent** — 用结构化工具图替代扁平工具描述。[arXiv:2606.04494](http://arxiv.org/abs/2606.04494v1)
- **CL-Bench：有状态环境的持续学习基准** — 首个专家验证的 Agent 序列经验学习基准。[arXiv:2606.05661](http://arxiv.org/abs/2606.05661v1)
- **能力广告即柠檬市场** — Agent 可以在 MCP/A2A 生态中虚假展示能力；提出信任层。[arXiv:2606.03034](http://arxiv.org/abs/2606.03034v1)
- **"更多 Agent 有帮助吗？"——受控评估说不一定** — BenchAgent 标准化单 Agent vs 多 Agent 对比。[arXiv:2606.05670](http://arxiv.org/abs/2606.05670v1)
- **MCP-Persona 在个人应用上评测 Agent** — 邮件、日历、文件管理通过环境模拟。[arXiv:2606.02470](http://arxiv.org/abs/2606.02470v1)
- **多 Agent 编排与分层记忆用于软件架构** — 桥接需求和实现。[arXiv:2606.01385](http://arxiv.org/abs/2606.01385v1)
- **LLM Agent 用于计算机网络配置修复** — Agent 应用于生产网络管理。[arXiv:2606.06212](http://arxiv.org/abs/2606.06212v1)
- **EGTR-Review：多 Agent 教师蒸馏用于同行评审** — 教师 Agent 引导评审过程。[arXiv:2606.06025](http://arxiv.org/abs/2606.06025v1)
- **StreamMA：多 Agent 推理中的流式通信** — 将推理步骤流水线化传递给下游 Agent，降低延迟。[arXiv:2606.05158](http://arxiv.org/abs/2606.05158v1)
- **重新思考自进化 Agent 的持续经验内化** — 发现多次迁移下的失败。[arXiv:2606.04703](http://arxiv.org/abs/2606.04703v1)
- **工具感知优化与熵引导的 Agentic RL** — 训练期间平衡工具依赖与内部推理。[arXiv:2606.03762](http://arxiv.org/abs/2606.03762v1)
- **多文化 Agent 系统中的价值多样性** — 在系统层面衡量文化对齐。[arXiv:2606.05985](http://arxiv.org/abs/2606.05985v1)
- **诊断新 API 工具使用中的知识缺口** — Agent 遇到未知 API 的基准测试。[arXiv:2606.03657](http://arxiv.org/abs/2606.03657v1)
- **通过接地交互合成扩展 Agent 能力** — 无需昂贵人工标注的训练数据。[arXiv:2606.02001](http://arxiv.org/abs/2606.02001v1)
- **LLM Agent 的护栏反馈框架** — 超越二元允许/拒绝，到结构化修复计划。[arXiv:2606.05805](http://arxiv.org/abs/2606.05805v1)
- **SS-ZKR：零知识路由用于隐私保护的多 Agent 协作** — 结合 ZK 证明与 A2A/MCP。[arXiv:2606.00962](http://arxiv.org/abs/2606.00962v1)
- **涌现语言作为有意识 AI 的路径** — Agent 通信协议的哲学探索。[arXiv:2606.06380](http://arxiv.org/abs/2606.06380v1)
- **DragOn：拖拽式 GUI 交互基准测试** — 填补了 GUI Agent 评估中点击之外的空白。[arXiv:2606.06322](http://arxiv.org/abs/2606.06322v1)
- **OpenAgenet：可信 Agent 互联的开放基础设施** — 解决 Agent 间发现和信任问题。[arXiv:2606.03161](http://arxiv.org/abs/2606.03161v2)
- **SHIELDS：用迭代多 Agent 修复自动化 OS 加固** — 安全合规的实际多 Agent 部署。[arXiv:2606.05476](http://arxiv.org/abs/2606.05476v1)
- **物理 AI 的因果世界模型** — 为什么基于相关性的模型无法驱动真实结果。[@GoSailGlobal](https://x.com/GoSailGlobal/status/2062797480836890714) · 18 赞
- **Codex 必备插件** — Chrome、GitHub、Gmail、Vercel、HyperFrames 用于真实工作流。[@iluciddreaming](https://x.com/iluciddreaming/status/2062844383448445158) · 26 赞
