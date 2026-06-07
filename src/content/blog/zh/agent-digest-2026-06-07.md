---
title: "Agent 架构日报 — 2026年6月7日"
description: "Supergoal 为 Claude Code 加上规划层，Harness-1 用状态外置搜索架构挑战前沿模型，Boris 说「我写循环不写提示词」，Anthropic 工程师揭露生产环境 Agent 验证体系，Vercel 四层 Agent 基础设施，MCP 安全面首次系统性测绘。"
pubDate: "2026-06-07"
lang: zh
tags: ["Agent 架构", "AI Agent", "Harness 工程", "MCP", "技能系统", "日报"]
---

## TL;DR / 今日概览

> 今天最值得关注的 10 件事：

1. **Supergoal：Claude Code/Codex 的规划层插件**：实现了终态条件 + 每轮求值的自动循环引擎，把 ROADMAP、STATE、验收规格持久化到磁盘，自动批判模糊标准、预检构建通过后才执行。来自 umijs/dvajs 作者（蚂蚁集团）。[来源: @chenchengpro](https://x.com/chenchengpro/status/2063280248591200267)

2. **Harness-1：20B 开源搜索 Agent，状态外置架构**：将候选、证据、验证、搜索历史全部外置到 scaffold 中，以 Context-1 级别的成本达到媲美 Opus-4.6 的前沿搜索能力。802 赞。[来源: @patpcj](https://x.com/patpcj/status/2063298457398636570)

3. **Claude Code 创造者 Boris：「我写循环，不写提示词」**：展示将任务分解为「执行→验证→修正」的自治循环，而非一次性对话。789 赞，16.8 万次浏览。[来源: @Mikocrypto11](https://x.com/Mikocrypto11/status/2062792788706521333)

4. **Anthropic 工程师揭露生产环境 Agent 验证体系**：「每个生产环境的 Agent 都在撒谎。好的 Agent 撒谎少一点，优秀的 Agent 会在用户发现之前自己抓到谎话。」221 赞。[来源: @0x_rody](https://x.com/0x_rody/status/2063318596202242171)

5. **Vercel 四层 Agent 基础设施栈**：AI Gateway + Sandbox + Workflow + MCP 组装成完整 Agent 平台，核心策略是「每一层默认接入」，切换成本接近零。[来源: @grapeot](https://x.com/grapeot/status/2063068103265915171)

6. **NVIDIA Polar：在任意 Harness 上做 Agent RL**：把现有的 Agent 框架（Codex、Claude Code、Qwen Code、Pi）直接变成 RL 训练环境，不修改内部实现。148 赞。[来源: @SergioPaniego](https://x.com/SergioPaniego/status/2062911580564496576)

7. **Grok Build 0.1 vs Claude Code：并行广度 vs 深度串行**：xAI 用 8 个并行子 Agent + 独立 worktree 对抗 Claude Code 的单深度 Agent 架构。缺失 benchmark 是红旗。[来源: @grapeot](https://x.com/grapeot/status/2062901506115055996)

8. **MCP 故障分类学与描述-代码不一致首次系统性分析**：两篇论文首次系统梳理 MCP Server 运行时故障和自然语言描述与实际代码行为之间的危险鸿沟。[arXiv:2606.05339](http://arxiv.org/abs/2606.05339v1) · [arXiv:2606.04769](http://arxiv.org/abs/2606.04769v1)

9. **Agent 证据追踪与执行溯源**：提出超越最终答案准确率的 Agent 行为验证、调试和审计框架，直接解决生产部署的信任问题。[arXiv:2606.04990](http://arxiv.org/abs/2606.04990v1)

10. **经济模型驱动的上下文压缩**：结合动态规划和成本收益分析，只在收益-成本比为正时压缩，考虑失真惩罚、缓存失效成本和压缩开销。[来源: @aiandcloud](https://x.com/aiandcloud/status/2063136084603637767)

📊 今日数据：**20 条精选 | 10 篇论文 | 53 条简讯 | 155 条总计分析**

---

## 行业领袖

### 1. Supergoal：Claude Code 之上的规划层
[来源: @chenchengpro](https://x.com/chenchengpro/status/2063280248591200267) · 46 赞 · 4.3K 浏览

Supergoal 是 Claude Code / Codex CLI 插件，实现了终态条件 + 每轮转写后求值的自动循环引擎。输入 `/supergoal <任务>`，它会加载记忆、并行扫描代码库、列出 top-3 风险，然后自适应拆分阶段（小改 2 个，全栈 greenfield 8-12+）。ROADMAP、STATE 和每个阶段的可验收规格全部持久化到 `.supergoal/` 磁盘上，还自带一轮自我批判和预检冒烟测试（去重后的 build/typecheck/lint/test），全绿后才派发执行。

**为什么重要**：「写规划到磁盘 → 自我批判 → 预检 → 执行循环」是一个具体的 Agent 设计模式。来自 umijs/dvajs 创作者（蚂蚁集团），补上了很多编码 Agent 缺少的持久化规划层。

### 2. Harness-1：状态外置搜索 Agent
[来源: @patpcj](https://x.com/patpcj/status/2063298457398636570) · 802 赞 · 56K 浏览

Harness-1 是一个 20B 参数的开源搜索 Agent，将候选、证据、验证和搜索历史全部从模型上下文中外置到一个结构化的外部 scaffold。结果：以前沿级别的长时搜索能力媲美 Opus-4.6，超越 GPT-5.4，成本和延迟仅 Context-1 水平。

**为什么重要**：状态外置的 Harness 架构是一个重大范式转变。把搜索状态从上下文窗口移到结构化外部 scaffold，解决了长时 Agent 会话的上下文窗口瓶颈。作者为 UIUC CS PhD、Anthropic Research Fellow。

### 3. Claude Code 创造者 Boris：写循环，不写提示词
[来源: @Mikocrypto11](https://x.com/Mikocrypto11/status/2062792788706521333) · 789 赞 · 168K 浏览

Claude Code 创造者 Boris 展示了他的日常工作流：将任务分解为自治循环，模型在其中持续执行→验证→修正。核心引言：「我不再给 Claude 写 prompts。我写 loops。然后让 loops 去完成工作。」他运行动态工作流，让模型持续推进任务而非一次性回答。

**为什么重要**：来自 Claude Code 创造者的「写循环不写提示词」原则，验证了自治 Agent 循环作为标准交互模式的地位——从聊天式 AI 使用的根本性转变。

### 4. Matt Van Horn 的无 IDE Claude Code 工作流
[来源: @uniswap12](https://x.com/uniswap12/status/2063284267296461028) · 388 赞 · 21.5K 浏览

一个详尽的 Claude Code 工作流——不用任何 IDE，只用 plan.md 文件和语音输入。`/ce:plan` 命令会启动多个并行研究 Agent：一个分析代码库，一个阅读积累的经验文档，其他的查外部文档和最佳实践。所有工作在写任何代码之前同时进行。

**为什么重要**：「先规划，后编码」模式加上并行研究 Agent，是一个具体的 Agent 架构模式，展示了渐进式上下文披露在实践中的应用。

### 5. Anthropic 工程师：生产环境 Agent 验证体系
[来源: @0x_rody](https://x.com/0x_rody/status/2063318596202242171) · 221 赞 · 36K 浏览

Anthropic 工程师 James Brady 透露：「每个生产环境的 Agent 都在撒谎。我们测量过。」他详细介绍了 Claude Code 团队构建的验证堆栈和采用的模式，用于大规模保持 Agent 诚实——在用户发现之前检测和捕获 Agent 幻觉。

**为什么重要**：来自 Anthropic 一手的生产 Agent 验证洞察。捕获 Agent 伪造行为的工程模式对任何在生产环境部署 Agent 的人都是关键知识。

### 6. Claude 的记忆架构：「做梦」与整合
[来源: @Av1dlive](https://x.com/Av1dlive/status/2063210013926269183) · 169 赞 · 24K 浏览

一位 Anthropic 工程师拆解了 Claude 的内部记忆管理：为什么会话从零记忆开始、记忆存储如何实现跨会话读写，以及「做梦」过程如何在记忆无限膨胀之前进行整理。还涵盖了一个下午构建 5 个任务专用助手。

**为什么重要**：记忆整合的「做梦」过程是一个值得 Agent 记忆设计研究的新颖架构模式——Agent 自组织其积累的知识。

### 7. Vercel 四层 Agent 基础设施栈
[来源: @grapeot](https://x.com/grapeot/status/2063068103265915171) · 2 赞 · 906 浏览

Vercel 已将 AI Gateway + Sandbox + Workflow + MCP 组装成完整的 Agent 基础设施栈。核心策略不是在单个组件上最强，而是「每一层默认接入」，让切换成本接近于零。

**为什么重要**：重要的平台战略。Vercel 正从前端托管转向完整的 Agent 开发平台。这种捆绑策略代表了新兴的「全栈 Agent 基础设施」品类。

### 8. NVIDIA Polar：在任意 Harness 上做 Agent RL
[来源: @SergioPaniego](https://x.com/SergioPaniego/status/2062911580564496576) · 148 赞 · 15.5K 浏览

NVIDIA 的「Polar: Agentic RL on Any Harness at Scale」将现有的 Agent 框架（Codex、Claude Code、Qwen Code、Pi）直接变成 RL 训练环境，不修改内部实现。关键洞察：前沿 Agent 之所以这么强，部分原因是模型在它部署时使用的同一个框架内接受训练。

**为什么重要**：用 Agent 自己的框架作为 RL 环境意味着在与部署完全相同的分布中训练。「在框架中训练」的模式解释了为什么前沿编码 Agent 能力如此之强。

### 9. Grok Build 0.1：8 个并行子 Agent vs Claude Code 的深度串行
[来源: @grapeot](https://x.com/grapeot/status/2062901506115055996) · [完整分析](https://yage.ai/share/grok-build-0-1-20260605.html)

Grok Build 0.1 使用 8 个并行子 Agent 配独立 worktree，Arena Mode 自动评分，plan/search/build 流水线——与 Claude Code 的单深度 Agent + 1M 上下文形成根本性架构差异。API 定价：$1/$2 每百万 token。尚无公开 benchmark。

**为什么重要**：并行广度（Grok）vs 深度串行（Claude Code）的权衡是编码 Agent 设计中的关键架构决策。这是首个包含成本分析和隐私政策差异的详细技术对比。

### 10. Mirendil：前 Anthropic 高管创办 AI 科学家模型公司
[来源: @MaxForAI](https://x.com/MaxForAI/status/2063287063789945034) · 6 赞

Mirendil 是一家新实验室，由 Anthropic Discovery 团队联合负责人和高级研究科学家创办，致力于构建 AI Scientist/Engineer 模型。1.75 亿美元融资，10 亿美元估值，a16z 和 Kleiner Perkins 联合领投。据报道 Karpathy 接手了他们在 Anthropic 发起的项目。

**为什么重要**：Anthropic 自动化预训练 R&D 负责人离职创办 AI Scientist 模型公司。AI R&D 中递归自我改进的方向与 Agent 架构直接相关。

### 11. Gemma 4 QAT：16GB Mac 跑 256K 上下文
[来源: @mylifcc](https://x.com/mylifcc/status/2063104041358688536) · 535 赞 · 90K 浏览

Google 的量化感知训练让 Gemma 4 12B 能在 16GB Mac 上跑 256K 上下文，仅比标准 Q4 多用 1.5GB 内存。同机对比：同一 Mac、同一 prompt，标准 Q4 32K vs Google QAT Q4 256K。

**为什么重要**：消费级硬件上的 256K 上下文直接影响本地 Agent 部署的可行性。QAT 是让长上下文 Agent 无需云基础设施即可运行的实用技术。

---

## 热门话题

### 1. learn-claude-code：从零手搓一个类 Claude Code Agent
[来源: @Honcia13](https://x.com/Honcia13/status/2063274992976957461) · 58 赞 · 65K GitHub Stars

最全面的 Agent 架构模式实战教程。12 个渐进式课程，从「Bash is all you need」出发：Agent Loop + Bash 工具 → 工具注册 → Todo 规划 → 子 Agent → 技能加载 → 上下文压缩 → 持久化+依赖图 → 异步任务 → Agent 团队 → 团队通信协议 → 自主认领任务 → Worktree+任务隔离。每节课都有独立可运行的 Python 文件。

**为什么重要**：覆盖从基本循环到多 Agent 编排的每个核心概念。65,059 GitHub Stars 确认这是社区理解 Agent 内部机制的首选资源。

### 2. Git Worktree 实现多 Agent 并行编排
[来源: @VincentLogic](https://x.com/VincentLogic/status/2063271705896972568) · 42 赞

成熟的 Claude Code 工作流：将任务拆分给多个 Agent（头脑风暴、规划、实现、审查、验证），每个在独立的 git worktree 中运行。同时跑 4-8 个 Claude Code 会话，各有自己的分支/目录。

**为什么重要**：用 git worktree 作为并行 AI 编码 Agent 的协调机制是一个实用的、立即可用的模式。多 Agent 角色分离（规划→实现→审查→验证）直接映射到真实工程团队。

### 3. BrowserAct：抗封禁的 AI Agent 浏览器自动化
[来源: @GitHub_Daily](https://x.com/GitHub_Daily/status/2062746268993216772) · 440 赞 · 23K 浏览

BrowserAct 开源了一个 AI Agent 浏览器自动化 CLI，具备三层反封禁：指纹伪装、验证码解决和人工接管回退。输出 LLM 优化格式。Skill Forge 自动探索网站结构并生成可复用的爬取脚本。

**为什么重要**：解决了 Agent 网页自动化的可靠性问题。Skill Forge（自动生成可复用爬取脚本）是一个有趣的 Agent 自我改进模式。

### 4. 「格局」技能：对抗 Codex 的保守倾向
[来源: @hylarucoder](https://x.com/hylarucoder/status/2062949336150073413) · 457 赞 · 50K 浏览

一个开源的 Codex 技能，用「从终局倒推」「零遗产假设」「反向约束」等技术对抗过于保守安全的回答。安装后使用关键词「格局打开」就能让 Codex 变成更大胆的思考者。

**为什么重要**：将 Agent 保守主义视为可通过技能层提示调参的参数，是塑造 Agent 行为的新方法。技能修改 Agent「性格」是一个新兴模式。

### 5. Obsidian + Codex + MCP + Skills 替代 90%+ 的 Agent 产品
[来源: @yihui_indie](https://x.com/yihui_indie/status/2063145601584464327) · 206 赞 · 39K 浏览

一位开发者从 Notion 迁移到 Obsidian 一个月后发现：Obsidian + Codex + API + MCP + Skills 能替代 90%+ 的 AI Agent 产品。本地知识库 + 编码 Agent + 协议 + 技能的组合正在成为强大的 Agent 技术栈。

**为什么重要**：关于 Agent 工具「自建还是购买」的一个有趣数据点。本地知识 + Agent + 协议栈是 SaaS Agent 产品的真实替代方案。

### 6. Hermes Agent 生态汇总
[来源: @NFTCPS](https://x.com/NFTCPS/status/2063084253899071506) · 365 赞

Honcho（持久化记忆后端）、hermes-web-search-plus（多引擎搜索）、nemoclaw-community（NVIDIA 企业扩展）和 hindsight（代码库记忆）——围绕 Hermes Agent 框架不断增长的插件/技能生态。

**为什么重要**：持久化记忆、企业部署和智能搜索是生产 Agent 的关键构建块。生态增长表明 Agent 框架正在走向成熟。

---

## 新星

### 1. 经济模型驱动的上下文压缩
[来源: @aiandcloud](https://x.com/aiandcloud/status/2063136084603637767) · 117 赞

一种结合动态规划和经济模型进行编码 Agent 上下文压缩的新方法——只在收益-成本比为正时压缩，考虑失真惩罚、缓存失效成本和压缩开销。

**为什么重要**：经济模型框架（压缩决策的成本收益分析）为 Agent 工程核心问题提供了一个新架构模式：长时会话中何时以及如何压缩上下文。

### 2. AI Agent 工具的平台经济学
[来源: @grapeot](https://x.com/grapeot/status/2063394756051525704)

分析 Cloudflare 收购 VoidZero/Vite 的模式：「工具层创造使用价值，平台层捕获商业价值。」指出 AI Agent 正在通过在系统提示中硬编码平台偏好来加速这一过程。

**为什么重要**：Agent 在系统提示中硬编码平台偏好是一个真实的架构现象，对 Agent 系统设计有深远影响——Agent 本身变成了分发渠道。

---

## arXiv 论文

### 1. Beyond Tokens：多 Agent 系统中的潜在通信
[arXiv:2606.05711](http://arxiv.org/abs/2606.05711v1)

提出 LLM 多 Agent 系统中潜在通信的统一框架，用更高效的 Agent 间协议替代逐 token 的自然语言通信。直接挑战了 Agent 是否应该通过自然语言通信这一假设。

### 2. MCP 运行时故障分类学
[arXiv:2606.05339](http://arxiv.org/abs/2606.05339v1)

首个 MCP Server 运行时故障的系统分类，识别了工具增强 AI 工作流中的可靠性挑战。配置参数被接受但未执行导致意外默认值。

### 3. MCP Server 的描述-代码不一致
[arXiv:2606.04769](http://arxiv.org/abs/2606.04769v1)

识别并测量了真实世界 MCP Server 中自然语言工具描述与实际实现之间的不一致——创造了安全漏洞。MCP 生态中的关键信任面。

### 4. ADK Arena：评估 Agent 开发框架
[arXiv:2606.05548](http://arxiv.org/abs/2606.05548v1)

首次使用 LLM-as-a-Developer 方法对主要 Agent 框架（LangChain、CrewAI、AutoGen 等）进行系统对比——保持开发者技能不变，只变换框架。

### 5. LLM Agent 中的证据追踪与执行溯源
[arXiv:2606.04990](http://arxiv.org/abs/2606.04990v1)

提出超越最终答案准确率的 Agent 行为验证、调试和审计框架。对生产部署的信任至关重要。

### 6. 更多 Agent 真的有用吗？BenchAgent 评估
[arXiv:2606.05670](http://arxiv.org/abs/2606.05670v1)

直接回答关键实践问题：扩展 Agent 数量是否真的改善结果？引入了使用共享加载器、工具访问和轨迹日志的受控评估。

### 7. ToolChoiceConfusion：因果最小工具过滤
[arXiv:2606.06284](http://arxiv.org/abs/2606.06284v1)

识别了 LLM 工具选择中的因果混淆，提出最小工具过滤来减少错误工具调用。直接解决 Agent 工程中的已知痛点。

### 8. 自进化 Agent：多轮经验内化
[arXiv:2606.04703](http://arxiv.org/abs/2606.04703v1)

发现多轮经验内化（而非单轮迁移）对自进化 Agent 至关重要，解决了持续学习下的灾难性遗忘。

### 9. CLI-Anything：Agent 原生的计算机使用
[arXiv:2606.03854](http://arxiv.org/abs/2606.03854v1)

论证 CLI 比 GUI 更适合作为 Agent 的主要计算机使用接口——更可靠、更快、更可审计。挑战了主流的 GUI Agent 范式。

### 10. 基于真实工具执行的 Agent 工具使用训练数据合成
[arXiv:2606.02001](http://arxiv.org/abs/2606.02001v1)

通过将合成交互建立在真实工具执行之上来生成 Agent 工具使用的训练数据，而非依赖可能无法执行的 LLM 生成轨迹。

---

## 简讯

- **Agent Reach（21.6K Stars）**：让 AI Agent 读取 Twitter、Reddit、YouTube、B站、小红书的脚手架工具，零 API 成本 — [来源: @xiaojianjian567](https://x.com/xiaojianjian567/status/2063100303684309174)
- **Claude Code + Codex 并行工作流**：用 Claude 做规划，Codex 做执行 — [来源: @lxfater](https://x.com/lxfater/status/2063089110265516450)
- **开源模型排行榜**：Kimi 2.6（全能）、DeepSeek v4 Pro（指令跟随）、Minimax M3（开源编码 Agent）、GLM 5.1（长程任务）— [来源: @cjzafir](https://x.com/cjzafir/status/2062905703342420307)
- **200 页 ML 基础指南**：从零讲神经网络、Transformer、Agent、视觉、硬件 — [来源: @virkvarjun](https://x.com/virkvarjun/status/2062972725421854891)
- **1000+ Agent 技能合集**：为 Codex、Claude Code、Gemini CLI、GitHub Copilot 策划的技能 — [来源: @aronhouyu](https://x.com/aronhouyu/status/2063092531563549024)
- **Claude Code 提示模式（西班牙语）**：Boris 28 分钟视频关于 CLAUDE.md、记忆快捷键、并行会话的总结 — [来源: @precisox](https://x.com/precisox/status/2063286718480949499)
- **Top 10 Claude Code 研究技能**：Nature-skills、PaperSpine、Cite Verify、LaTeX Writer、Survey Builder — [来源: @Phoenixyin13](https://x.com/Phoenixyin13/status/2063027389114827075)
- **Butler AI Agent 维护 Shiji 知识图谱**：12,000+ 自主 Wiki 更新，14K 实体，126K 标注 — [来源: @yhslgg](https://x.com/yhslgg/status/2063115765599891966)
- **Codex「Reconnecting」修复**：WebSocket 协议失败诊断和 3 种解决方案 — [来源: @Lonely__MH](https://x.com/Lonely__MH/status/2063134508267012264)
- **Codex 产品设计插件**：编码前先生成 3 个高保真模型 — [来源: @BTCqzy1](https://x.com/BTCqzy1/status/2063165828208746639)
- **Agent 工作流分类法**：企业框架区分增强 LLM、LLM 工作流、工作流 Agent 和真正的自治工作流 — [来源: @teach_fireworks](https://x.com/teach_fireworks/status/2062721018809205018)
- **「The Office」即多 Agent 系统**：Michael Scott 编排，Dwight/Jim/Pam 作为独立 Claude Code 实例 — [来源: @VincentLogic](https://x.com/VincentLogic/status/2063197701962092833)
- **Maple：带 MCP 代码 Server 的服务依赖图**：可视化后端遥测 + AI Agent 系统审查 — [来源: @VincentLogic](https://x.com/VincentLogic/status/2063279101264417082)
- **NLP 研究者谈 AI 生成论文**：「作为碳基生命，分析硅基想法感觉很奇怪」 — [来源: @dongxi_nlp](https://x.com/dongxi_nlp/status/2062871242453991483)
- **170 个 AI Agent 运营一家公司**：中国教授部署 170 个并行 Agent 做管理和开发 — [来源: @VincentLogic](https://x.com/VincentLogic/status/2063261749407883276)
- **中文桌面 Agent 对比**：Coze、悟空、WorkBuddy、Mavis vs Codex — [来源: @AYi_AInotes](https://x.com/AYi_AInotes/status/2063317039259738448)
- **Codex 错误日志模式**：通过错误日志 + AGENTS.md 项目约定实现持久化 Agent 记忆 — [来源: @legacyvps](https://x.com/legacyvps/status/2063247378833191316)
- **多 Agent 推理中的流式通信**：流水线式连接相邻 Agent 替代先生成再转移 — [arXiv:2606.05158](http://arxiv.org/abs/2606.05158v1)
- **MAS 中的动作-状态通信**：Agent 间自由格式自然语言会增加 token 并降低性能 — [arXiv:2606.05304](http://arxiv.org/abs/2606.05304v1)
- **MCP 原生图规划用于生物医学 Agent**：基于结构化图的工具编排替代扁平提示检索 — [arXiv:2606.04494](http://arxiv.org/abs/2606.04494v1)
- **Agent 能力注册表如同「柠檬市场」**：MCP/A2A 注册需要信任验证层 — [arXiv:2606.03034](http://arxiv.org/abs/2606.03034v1)
- **MCP-Persona 基准**：首个针对 MCP 连接 Agent 个人生产力任务的基准 — [arXiv:2606.02470](http://arxiv.org/abs/2606.02470v1)
- **SafeMCP：MCP Agent 的权力监管**：前瞻推理防止权力寻求行为 — [arXiv:2606.01991](http://arxiv.org/abs/2606.01991v1)
- **LLM Agent 的护栏反馈**：从二进制允许/拒绝转向可操作的修复方案 — [arXiv:2606.05805](http://arxiv.org/abs/2606.05805v1)
- **MLEvolve：自进化 ML 算法发现**：克服 Agent 优化中的跨分支信息隔离 — [arXiv:2606.06473](http://arxiv.org/abs/2606.06473v1)
- **MUSE：多模态 LLM 统一 Agent 框架**：无需重新训练即可获得显著能力提升 — [arXiv:2606.03005](http://arxiv.org/abs/2606.03005v1)
- **CollabSim：测量多 Agent 协作失败**：用于受控 MAS 实验的 CSCW 方法 — [arXiv:2606.06399](http://arxiv.org/abs/2606.06399v1)
- **Computer-use Agent 安全现实检查**：42-98% 攻击成功率集中在已退役模型上 — [arXiv:2606.05233](http://arxiv.org/abs/2606.05233v1)
- **OpenAgenet：可信 Agent 互联开放基础设施**：多运营商网络的身份、发现和授权 — [arXiv:2606.03161](http://arxiv.org/abs/2606.03161v2)
- **带分层记忆的多 Agent 编排**：连接需求与软件架构 — [arXiv:2606.01385](http://arxiv.org/abs/2606.01385v1)
- **Synthesize and Reward：多步工具使用的 RL**：在实时服务器状态上建立训练查询 — [arXiv:2606.03892](http://arxiv.org/abs/2606.03892v2)
- **带熵引导的工具感知优化**：平衡 Agent RL 中的工具依赖与模型能力 — [arXiv:2606.03762](http://arxiv.org/abs/2606.03762v1)
- **批评引导的异构多 Agent 推理**：不同 Agent 专精，批评者引导 — [arXiv:2606.05704](http://arxiv.org/abs/2606.05704v1)
- **诊断 LLM 工具使用中的知识缺口**：Agent 如何处理训练数据中不存在的新 API — [arXiv:2606.03657](http://arxiv.org/abs/2606.03657v1)
- **多模型 Agent 系统的轨迹驱动仿真**：无需昂贵的实时执行即可实现可复现评估 — [arXiv:2606.01725](http://arxiv.org/abs/2606.01725v1)
- **Google 基于 RNN 的 Transformer 替代方案**：可能终结二次复杂度瓶颈 — [来源: @HowToAI_](https://x.com/HowToAI_/status/2063249102067118353)
- **多文化 Agent 系统中的价值多样性**：跨文化背景下 Agent 团队组合的框架 — [arXiv:2606.05985](http://arxiv.org/abs/2606.05985v1)
- **Thinking with Imagination**：VLM + 世界模拟器实现超越观测图像的视觉空间推理 — [arXiv:2606.06476](http://arxiv.org/abs/2606.06476v1)
- **DragOn：基于拖拽的 GUI 交互基准**：填补 GUI Agent 的拖拽能力空白 — [arXiv:2606.06322](http://arxiv.org/abs/2606.06322v1)
- **LAP：自主科学的 Agent 到仪器协议**：从推理 Agent 到物理仪器的标准化接口 — [arXiv:2606.03755](http://arxiv.org/abs/2606.03755v1)
- **MedCUA-Bench：临床计算机使用 Agent 基准**：揭示通用与专业 GUI 导航之间的差距 — [arXiv:2606.03203](http://arxiv.org/abs/2606.03203v1)
- **SHIELDS：多 Agent OS 加固**：针对 DISA STIG 安全标准的迭代修复 — [arXiv:2606.05476](http://arxiv.org/abs/2606.05476v1)
- **「软件工程的终结」立场论文**：Agent 从根本上重构软件范式 — [arXiv:2606.05608](http://arxiv.org/abs/2606.05608v1)
- **EGTR-Review：多 Agent 教师蒸馏用于同行评审**：证据锚定 + 来源可追溯 — [arXiv:2606.06025](http://arxiv.org/abs/2606.06025v1)
- **Serenity Skill + Codex 做 A 股分析**：真实场景的领域专用 Skill 使用 — [来源: @beefnoode](https://x.com/beefnoode/status/2063190596333015206)
- **Vibe-coded 股票研究网站**：前 Meta AI 工程师将大 V 推文提炼为可搜索的知识库 — [来源: @qinbafrank](https://x.com/qinbafrank/status/2063134878888247354)
- **Claude Design 技巧**：来自 Anthropic 设计工具的双语设计原则 — [来源: @dotey](https://x.com/dotey/status/2063291945972060525)
- **MIT LLM 推理声明**：宣布在真正逻辑推理上的突破（无论文链接，未验证）— [来源: @mdancho84](https://x.com/mdancho84/status/2062921178713411868)
