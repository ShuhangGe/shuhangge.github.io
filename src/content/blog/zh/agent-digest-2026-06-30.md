---
title: "Agent 架构每日速递 — 2026 年 6 月 30 日"
description: "Agent 技术栈正在收敛为三大原语——循环（Loop）、技能（Skill）、记忆（Memory）：Anthropic 泄露的 Loop Engineering 手册将「生成器/评估器分离」正式化，微软 SkillOpt 把技能当作可训练对象，一批跨 Agent 记忆层（memorix、second-brain、agentmemory）涌向 MCP。Claude Code 子代理默认后台运行，RepoPrompt 开源「MCP 即主控」的架构反转，GLM-5.2 + SKILL.md 实战超越 Opus 4.8，Karpathy 的 LLM Wiki 把知识从「检索」重构为「编译」。"
pubDate: "2026-06-30"
lang: zh
tags: ["Agent", "LLM", "AI 架构", "每日速递"]
---

## TL;DR — 今日概览

> 今天最值得关注的 10 件事：

1. **Anthropic 的 Loop Engineering 手册泄露——并且已成为教条。** 这份内部指南（现已发为 O'Reilly 文章）把 agentic loop 定义为「harness engineering 之上」的一层。Claude Code 负责人 Boris Cherny 说：*"我不再 prompt Claude 了。我有循环在跑，由它们去 prompt Claude……我的工作是写循环。"* 五个动作（发现、交接、验证、持久化、调度），六个部件（自动化、worktree、技能、连接器、子代理、记忆），以及一条铁律：永远不要让生成器给自己的产出打分。 — [@milesdeutscher](https://x.com/milesdeutscher/status/2071528356152303770)，[O'Reilly Radar](https://www.oreilly.com/radar/loop-engineering/)

2. **Claude Code 子代理将默认在后台运行。** Boris Cherny 确认下一版 Claude Code 会在子代理工作的同时保持你的对话交互——需要前台运行时告诉 Claude 即可。配合可并行生成约 1000 个子代理的 "ultracode" 模式，Claude Code 正从一个聊天框变成一个并行编排运行时。 — [@bcherny](https://x.com/bcherny/status/2071647677591466098)

3. **微软 SkillOpt 把 Agent 技能当作可训练对象。** 这个「文本空间优化器」像训练神经网络一样训练一份自然语言 `best_skill.md`——epoch、mini-batch、学习率、留出验证门——但完全不碰模型权重。轨迹驱动编辑、反思、有界更新。含义：领域知识积累在技能里，冻结的模型不重训也能变强。 — [microsoft/SkillOpt](https://github.com/microsoft/SkillOpt)，[@nash_su](https://x.com/nash_su/status/2071480871794999552)

4. **RepoPrompt 开源了一个架构反转：MCP server 成为主控。** RepoPrompt CE 推翻了原设计——不再由 App 调度 Agent，而是内置 MCP server 负责编排，CLI 工具（Claude Code、Codex、OpenCode、Gemini CLI）变成可替换的执行层。一个推理模型做规划与分解，子任务分发给只看自己那部分文件的并行 Agent。 — [@dotey](https://x.com/dotey/status/2071348102263378085)，[repoprompt-ce](https://github.com/repoprompt/repoprompt-ce)

5. **Karpathy 的 LLM Wiki 把知识从「检索」重构为「编译」。** 这份刷屏文档主张：把知识一次性编译成互联的 Markdown 并持续更新，而不是每次都问 LLM 或跑 RAG。人拥有规格和边界，模型拥有执行和记账。"Prompt 很容易。Loop 很难。" — [@hanakoxbt](https://x.com/hanakoxbt/status/2071327017161617843)，[@polydao](https://x.com/polydao/status/2071105441698910415)

6. **GLM-5.2 + SKILL.md 实战超越 Opus 4.8、GPT 5.4、Gemini 3.5 Flash。** 智谱/Z.ai 的开源旗舰（约 753B 参数 MoE、MIT 协议、100 万 token 上下文）Terminal-Bench 2.1 拿到 81.0、SWE-bench Pro 拿到 62.1。实战者反馈：强制 GLM-5.2 使用技能会带来「巨大的性能跃升」，而且可以用 Codex 来给 GLM 写技能。技能是开源与闭源模型之间的最大均衡器。 — [@cjzafir](https://x.com/cjzafir/status/2071225619803750560)，[zai-org/GLM-5](https://github.com/zai-org/GLM-5)

7. **记忆层的淘金热涌向 MCP。** 数周内至少四个跨 Agent 记忆项目上线，且都收敛到同一个设计：存一次，通过 MCP 暴露，任何客户端都能召回。**memorix**（11 个编程 Agent 共用一个记忆）、**second-brain-cloudflare**（Cloudflare D1/Vectorize/KV，免费额度可个人用）、**agentmemory**（iii 引擎，覆盖 Hermes/Codex/Cursor）、**open-second-brain**（Hermes + 夜间「做梦」流程，把重复纠正转成确认偏好）。 — [@geekbb](https://x.com/geekbb/status/2071222081358799250)，[memorix](https://github.com/AVIDS2/memorix)

8. **整套中文模型栈比闭源便宜 87%，质量只损失约 4%。** 一位运营者 30 天的切换报告：Opus→Kimi K2.7（推理）、GPT-5.5→Qwen 3.7 Max（代码）、Sonnet→GLM 5.2（Agent 循环），全部可路由、可本地部署。DeepSeek 还开源了 **DSpark**——一种可无损提升推理效率、且可移植到其他大模型的方法。 — [@DeRonin_](https://x.com/DeRonin_/status/2071561335234531578)，[@manateelazycat](https://x.com/manateelazycat/status/2071247379580530854)

9. **Cognition 的 Scott Wu：软件入口从「写代码」走向「指挥电脑」。** Devin 创始人把 Agent 定义为新型人机交互——你说清目标，Agent 决定该写什么代码、跑什么脚本。PR 审查工作流只是「10 代以上产品中的第 1 代」，真正的创新还在任务表达、上下文理解、验证和交付流程。一次性软件从此变得经济可行。 — [@teach_fireworks](https://x.com/teach_fireworks/status/2071618602923610179)

10. **反向声音：「我停掉了 Claude Max。」** 一条高互动帖子（528 赞、142 评）反思 vibe coding 的倦怠——"搞出一堆垃圾、人越来越浮躁、认知浮于表面。" 提醒一点：只有配合 Anthropic 手册要求的验证纪律，loop 优先的架构才真正划算。 — [@PandaTalk8](https://x.com/PandaTalk8/status/2071407431146680664)

📊 今日数据：**采集 X「For You」136 条 | 预过滤后相关候选 44 条 | 详细条目约 30 条 | 短讯 30+ | 论文 6+** ——X 采集已恢复正常（今日无 AUTH_REQUIRED）。

---

## 本期主轴：技术栈收敛为三大原语——循环、技能、记忆

三股力量在这个周期汇合，X 上的讨论罕见地一致。

**第一股力量：循环成为工作单元。** 本周期传播最广的产物是 Anthropic 的 *Loop Engineering* 手册——先是泄露，后被整理成 O'Reilly 文章。它的核心论点重塑了一切：工作不再是写 prompt，而是写循环去 prompt 模型。Boris Cherny 的金句是标题，但实质是那套分类法。一个循环回合被分解为五个动作——*发现、交接、验证、持久化、调度*——系统由六个部件构成：自动化（定时器）、worktree（安全并行）、技能（持久知识）、连接器（GitHub/Linear 等）、子代理（生成器 + 评估器）、记忆（跨运行存活的状态）。那条不可妥协的规则——把生成器和一个*怀疑论者*评估器分开，因为 Agent 系统性地吹捧自己的产出——是"写个验证器"的工程化版本，现在还有了实证支撑。当 Claude Code 同时宣布子代理*默认后台运行*，loop 优先模型就不再只是博客文章，而成了产品的默认交互。对构建者的含义：你的价值上移一层，从"写 prompt"变成"设计围绕它的控制系统"。

**第二股力量：技能成为可训练对象。** 微软 **SkillOpt** 是本周期架构意义最大的发布。它把一份紧凑的自然语言技能文档当作*冻结 Agent 的可训练状态*，再通过 rollout、反思、有界编辑、留出验证门来学习它——把神经网络训练的词汇（epoch、mini-batch、学习率）搬进文本空间，却不碰一个权重。它重要，因为它给开源权重和冻结模型提供了一条*不靠重训*就能提升的路径：领域知识活在技能里，而技能被优化。@cjzafir 的实战报告——"GLM 5.2 + SKILL.md > Opus 4.8、GPT 5.4、Gemini 3.5 Flash"，用 Codex 来给 GLM *撰写*技能——就是证据：一个搭配优秀技能的开源模型可以打败一个 prompt 冷启动的前沿模型。这与 Karpathy 的 LLM Wiki 重构（知识编译一次，而非每轮检索）以及 RepoPrompt 的架构翻转（MCP server 编排，CLI Agent 变成可替换执行器）一脉相承。贯穿线完全一致：**别再管理 prompt 了，去管理那些能复利的持久产物**（技能、wiki、记忆文件）。

**第三股力量：记忆在 MCP 上整合。** 四个跨 Agent 记忆层在数周内先后上线，且都收敛到同一个设计：存一次，通过 MCP 暴露，任何客户端都能召回。**memorix** 覆盖 11 个编程 Agent；**second-brain-cloudflare** 在 Cloudflare 免费额度上自托管，用 D1/Vectorize/KV；**agentmemory** 跑一个覆盖 Hermes、Codex、Cursor、Copilot 的"iii 引擎"；**open-second-brain** 加了夜间"做梦"流程，把重复纠正升级为可信偏好。它之所以是趋势而非新奇玩意：当 Agent 在循环里带着后台子代理跑，*跨运行存活的状态*就成了"会学习的 Agent"和"每轮从零开始"的分水岭。记忆正是 loop 分类法所要求的持久化原语。

合在一起，给 Agent 构建者的信号很锐利：前沿不在更大的模型——而在于你围绕"本周最便宜的那个模型"包的三层持久层（循环控制、训练过的技能、持久记忆）。

---

## X / Twitter 精选

### Anthropic / Claude Code —— Loop Engineering 落地

[**@bcherny**](https://x.com/bcherny/status/2071647677591466098)（Boris Cherny，Claude Code 负责人）确认下一版 Claude Code 会**让子代理默认后台运行**，这样子代理工作时你还能继续和 Claude 对话——前台执行改成按需请求。这是 loop-engineering 论点的产品化：长任务被委派并隔离，主线程保持交互。配合据报道能并行生成约 1000 个子代理的 "ultracode" 模式，Claude Code 正被重建成一个分层 agentic 运行时（记忆、hook、技能、子代理、插件、MCP 作为独立层），而非一次对话。

[**@milesdeutscher**](https://x.com/milesdeutscher/status/2071528356152303770) 把他称为 Anthropic 泄露的内部**loop engineering 手册**做了整理——"今年我读过最有价值的 AI 指南。" 他的压缩是目前最清晰的公开总结：每个循环围绕 发现 → 交接 → 验证 → 持久化 → 调度 来组织；**永远把生成器和一个怀疑论评估器分开**（评估器必须*动手*——跑测试、点按钮、截图）；用六个部件搭建（自动化、worktree、技能、连接器、子代理、记忆）；并警惕四种失败模式——验证债务、对你自己代码库失去理解、token 成本爆炸、认知投降。token 成本那一节尤其实用，因为大规模 loop engineering 很贵。

[**@wangray**](https://x.com/wangray/status/2071531957360746768)（Anthropic partner、Upthos 创始人）继续 **Claude Architect 认证**备考系列，本期深挖 **Hook**——定位为"Agent 的安检口"，在工具调用执行前拦截并规范化数据。这个框架很关键：hook 是让自主循环能"在你睡觉时安全调度"的策略执行层。如果验证是循环的免疫系统，hook 就是它的外围防线。

### Karpathy 的 LLM Wiki 与上下文工程

[**@hanakoxbt**](https://x.com/hanakoxbt/status/2071327017161617843) 浓缩了本周的氛围：**"KARPATHY 用一份文档终结了 prompt 时代。"** 论点是：prompt 很容易，loop 很难，而每天写五十个 prompt 是没人会做两次的活。Karpathy 的方法把负担转移到 harness 上——契约定义一次，模型负责写/审/重启/对账，你保留判断力，它负责循环。"人拥有规格和边界。模型拥有执行和记账。"

[**@polydao**](https://x.com/polydao/status/2071105441698910415) 描述了实际收益：LLM Wiki 模式把 AI 变成你 Zettelkasten 的**全职维护者**——它读取每个新来源并整合进结构化 wiki，跨笔记查找矛盾，整个知识库自动复利，你一个链接都不用手动敲。Obsidian 变成可视化 IDE，Claude 是后端。

[**@dotey**](https://x.com/dotey/status/2071348102263378085) 给出了本周期技术细节最扎实的帖子，关于 **RepoPrompt 开源版（CE）**。关键的架构反转：RepoPrompt 不再让 App 调度 Agent——而是让**内置的 MCP server 成为主控**，CLI 工具（Claude Code、Codex、OpenCode、Gemini CLI）变成可替换的执行层。一个推理模型做规划和分解，子任务分发给只看自己那部分文件的并行 Agent。动机是*上下文工程*——把整个仓库丢给模型在超过约 32K token 后会让模型变笨，所以你精确筛选每个 Agent 真正需要的东西。（背景：OpenAI 的 Romain Huet 挖了 RepoPrompt 的作者；他在确保付费用户被妥善安排后才开源。）安装：`brew install --cask repoprompt-ce`。

### 技能成为可训练对象

[**@nash_su**](https://x.com/nash_su/status/2071480871794999552) 用正确的直觉标注了**微软 SkillOpt**："用训练模型的思路优化 skill。" 每轮调整 skill 的 Markdown 内容，跑结果，好了就继续推进——给一个 `.md` 文件跑 epoch。SkillOpt 自己的框架：像训练神经网络一样训练 Agent 技能，带验证门，但不碰权重。`best_skill.md` 产物可部署、可分享。

[**@cjzafir**](https://x.com/cjzafir/status/2071225619803750560) 给出了最锐利的实战报告：**"GLM 5.2 + SKILL.md > Opus 4.8、GPT 5.4、Gemini 3.5 Flash。"** 强制一个冻结模型使用优秀技能就会得到"巨大提升"——而且关键是，*你可以用 Codex 给 GLM 5.2 写技能*。要点：技能撰写是可组合、与模型无关的，这正是技能成为开源与闭源模型最大均衡器的原因。

### 记忆层的淘金热

[**@geekbb**](https://x.com/geekbb/status/2071222081358799250) 推荐了 **second-brain-cloudflare**——给 Claude、ChatGPT、Cursor、Codex 用同一个记忆层，在 Cloudflare Workers 上自托管，记忆落在你自己的 D1、Vectorize、KV、Workers AI 里，通过 MCP 暴露，强调*语义*检索而非字面匹配。个人规模可塞进 Cloudflare 免费额度。更大的故事是整个品类：**memorix**（[GitHub](https://github.com/AVIDS2/memorix)）通过 MCP 统一了 11 个编程 Agent 的记忆（Cursor、Claude Code、Codex、Windsurf、Gemini CLI、Copilot、Kiro、OpenCode、Antigravity、Trae、Pi）；**agentmemory**（[GitHub](https://github.com/rohitg00/agentmemory)）跑一个覆盖 Hermes、OpenClaw、Codex 的"iii 引擎"；**open-second-brain** 加了夜间*做梦流程*，把重复纠正升级为可信偏好。"存一次、随处召回、走 MCP"的收敛再明显不过。

### 中文开源权重栈与成本算术

[**@DeRonin_**](https://x.com/DeRonin_/status/2071561335234531578) 发布了一份详细的 30 天切换结果并刷屏：**运营成本下降 87%，产出质量平均下降约 4%，收入不变。** 路由方案：Opus 4.8 → Kimi K2.7（推理，便宜约 11 倍）、GPT-5.5 → Qwen 3.7 Max（代码，便宜约 7 倍）、Sonnet → GLM 5.2（Agent 循环，输入便宜约 5 倍）、GPT-5.5 mini → MiMo V2.5（批量，便宜约 12 倍）、GPT-Image-2 → Wan 2.5、Sora 2 → Kling 3.0。两个明确优势：模型不会被月中封禁，数据留在本地。

[**@manateelazycat**](https://x.com/manateelazycat/status/2071247379580530854)（前 Deepin CTO）指出梁圣开源的 **DeepSeek DSpark** 是一种**可移植、无损的推理效率提升**方法，可迁移到其他大模型——并暗想 Anthropic 和 OpenAI 会不会"偷偷抄作业"。

### Agent 产品与工具

[**@akshay_pachaar**](https://x.com/akshay_pachaar/status/2071509401224261823) 展示了 **Google 的 Agents CLI** 把 Karpathy 的"Agentic Engineering"操作化——一条 setup 命令给编程 Agent 注入 7 个 ADK 专用技能，覆盖脚手架、评估、部署、企业注册全流程。他的演示：用 Claude Code 从 `agentic_rag` 模板脚手架一个 RAG Agent、生成 20 个带 LLM-as-judge 打分的评估场景、部署到 Agent Runtime、注册到 Gemini Enterprise。评估记分卡在部署前就抓住了一个指令漏洞。

[**@zjp1997720**](https://x.com/zjp1997720/status/2071129331699745263) 推荐 **Lazy Codex**（oh-my-openagent 项目出品）——一个让 Codex *主动、有计划地*调用 Agents Team 处理复杂任务的插件（这是 Claude Code 默认就做、Codex 默认不做的事），还带不错的上下文共享机制。不止编码场景好用，演示的就是一个调研任务。

[**@indie_maker_fox**](https://x.com/indie_maker_fox/status/2071099277066272900) 提出一个值得钉住的产品哲学论断：**"未来程序员不再主要'做需求'，而是'做 agent'。"** Agent 把你的能力蒸馏出来：说清需求，剩下的交给它。他建议别从零开始，直接从 **Pi agent**（开源编程 Agent，类似 Claude Code）或 **Craft agent**（类似 Claude Cowork，基于 Pi + Claude Agent SDK）起步——并报告已在上面二开了一个接技能市场的产品"Echo"。

[**@teach_fireworks**](https://x.com/teach_fireworks/status/2071618602923610179) 分享了 **Cognition/Scott Wu 访谈笔记**：Devin 不被框定为编程 Agent，而是一种新型人机交互——你说清目标，Agent 决定写什么代码、跑什么脚本。一次性软件（扫 15 个 LinkedIn 档案、填表、做 Excel 分析）变得经济可行，因为执行路径按需生成。Wu 把 PR 审查工作流看作"10 代以上产品里的第 1 代"，真正的创新在任务表达、上下文理解、验证和交付流程。

[**@Yangtze_Seventh**](https://x.com/Yangtze_Seventh/status/2071564088228888769) 预告了 **Raven Agent**——一个以*你*为核心设计的 Agent，随时间迭代你自己的架构和能力，最终代表你并与其他个人 Agent 对话。框架是：Agent 是带着你"分身记忆"的伙伴，而非工具。不论产品是否落地，"*会复利的个人 Agent*"这个概念已经在空气里了。

[**@Easycompany333**](https://x.com/Easycompany333/status/2071204465168769451) 推荐 **Google 8 分钟"最小 agent loop"教程**作为理解循环最快的路径——并诚实地说，*理解*循环很容易，但*用好*很难，就像技能一样。"实践是检验真理的唯一标准。"

### 反向声音：Vibe Coding 倦怠

[**@PandaTalk8**](https://x.com/PandaTalk8/status/2071407431146680664) 发了本周期回复最多的反向帖子（528 赞、142 评）：**"我停掉了 Claude Max。"** 几个月的 vibe coding"搞出一堆垃圾，人也越来越浮躁，学习和认知浮在表面。" 回到读经典、深度思考、专注让他踏实多了。这是一个有用的纠偏——而且正好对应 Anthropic 手册里的"认知投降"警告。只有当验证纪律是真的，loop 优先架构才划算。

### 新星与构建者

[**@better_christal**](https://x.com/better_christal/status/2071247379580530854) 展示了 **n8n + Modbus 节点读取制造业 PLC 设备**——温度/压力/振动 → 阈值告警 → 自动化工作流。一个远离编程 Agent 回音壁、具体的 agentic 自动化案例，也提醒"工具调用"包括工业协议。

[**@Lonely__MH**](https://x.com/Lonely__MH/status/2071423674683711776) 分享了一个**查询 Codex 赠送额度/重置到期时间**的方法——用一个读取 `~/.codex/auth.json`、只汇总 `available_count`/`expires_at` 且不泄露 token 的 prompt——并推荐了 **Hermes Bible**，一个应对 Hermes Agent 快速迭代的社区工具。两者都说明围绕 Agent 平台的构建者生态正在成熟。

[**@Saccc_c**](https://x.com/Saccc_c/status/2071407431146680664) 用 Codex 做了个人网站（332 赞、111 评），主张"AI 时代个人网站是最好的简历"——一个小而有代表性的"按需生成一次性软件"案例，正好从另一个方向印证了 Cognition 的论点。

---

## 值得关注

- **@AlchainHust**：Claude Fable 5 和 GPT-5.6 都被要求通过白宫审查才能放出——GPT-5.6 的审查来得晚得多，进一步收窄了它的发布窗口。政府管控趋势延续。
- **@canghe / @mylifcc / @dotey**：GPT-5.6 Sol 据报道在**灰度推送**——"Juice 测试"（gpt-5.5 设 xhigh）返回 128 说明被路由到 5.6-Sol，否则是 768。社区在实时探测模型路由。
- **@_avichawla**：精彩的 **prefill 与 decode** 推理解析——为什么首 token 会卡一下（计算受限、并行算 Q/K/V），而后续 token 流式输出（访存受限）。理解 Agent 延迟预算的基础。
- **@Jolyne_AI**：开源**强化学习教材**推荐——介于"只讲概念"和"公式堆满"之间的实用中间地带。
- **@fiapp_pro**： endorse 一个 Codex 插件为"半年 Codex 用下来最爽的，没有之一"——Agents Team 的上下文共享。
- **@cjzafir**（thread）：用 **Codex 给 GLM 5.2 写技能**——技能撰写是跨模型、可组合的工作流。
- **MCP 2026-07-28 RC** 持续临近：**无状态协议核心** + Extensions 框架。6 月 29 日已追踪；仍为上述每个记忆/技能项目的协议层背景。
- **Claude Architect 认证**（@wangray）：Hook 作为 Agent 安检口——备考系列第 4 期。
- **open-second-brain**：Hermes Agent 记忆 + 夜间"做梦流程"——重复纠正 → 确认偏好。
- **memorix**：通过单一 MCP server 给 11 个编程 Agent 提供跨 Agent 记忆。
- **@Yangtze_Seventh**：Raven Agent——以*你*为核心、随时间复利的个人 Agent。
- **@teach_fireworks**：Devin 的 PR 工作流是"10 代以上里的第 1 代"。
- **@dotey**：RepoPrompt CE 架构反转——MCP server 即主控。
- **@hanakoxbt / @polydao**：Karpathy LLM Wiki——编译一次，别每次都检索。
- **@milesdeutscher**：Loop Engineering 五动作、六部件、生成器≠评估器。
- **@bcherny**：Claude Code 子代理默认后台运行。

---

## 论文与研究

1. **Loop Engineering：Anthropic 手册**（[HyperAI/IEEE](https://hyper.ai/en/papers/Loop-Engineering-IEEE)）—— 把 loop engineering 定义为 harness engineering 之上的第四层。把一个循环回合分解为五个动作和六个部件，并引入生成器/评估器分离，实证表明 Agent 系统性地吹捧自己产出，而独立调教的怀疑论评估器远比让生成器自我批判更可行。本周期的架构核心。

2. **SkillOpt：自进化 Agent 技能的文本空间优化**（[microsoft/SkillOpt](https://github.com/microsoft/SkillOpt)，[PyPI](https://pypi.org/project/skillopt/)）—— 把自然语言技能文档当作冻结 Agent 的可训练状态；通过 rollout、反思、有界编辑、留出验证门来学习它。把 epoch/batch/学习率的词汇搬进文本空间却不碰权重。6.4K stars。

3. **GLM-5：从 Vibe Coding 到 Agentic Coding**（[zai-org/GLM-5](https://github.com/zai-org/GLM-5)，[openlm.ai](https://openlm.ai/glm-5.2/)）—— 智谱/Z.ai 开源旗舰：约 753B 参数 MoE、MIT、100 万 token 上下文，改进的 MTP 层提升投机解码接受长度 +20%。**Terminal-Bench 2.1：81.0**（开源最强），**SWE-bench Pro：62.1**——为长程编程 Agent 设计，正在缩小与前沿闭源模型的差距。

4. **多 Agent LLM 审议中的隐藏锚点**（[arXiv:2606.19494](https://arxiv.org/abs/2606.19494)）—— 2026 年 6 月 17 日提交，13 页。分析扭曲多 Agent LLM 审议的锚定偏差——对任何设计生成器/评估器或多 Agent 辩论系统的人都相关。

5. **可微混合 Agent（Differentiable MoA）**（[arXiv，2026 年 5 月](https://24-ai.news/en/news/2026-05-18/arxiv-differentiable-mixture-of-agents-swarm/)）—— 引入多 Agent LLM 协作的*可微路由*机制，在 9 个基准上达到 SOTA。朝着*可学习*的 Agent 编排而非手接线图迈出的一步。

6. **可追溯推理的物理基础多 Agent 架构**（[arXiv:2605.04003](https://arxiv.org/html/2605.04003)）—— 发现单 LLM 基线在单工具调用查询上仍有竞争力，但在依赖性工具链上退化——为"多 Agent 编排到底何时划算"提供了实证。

---

*采集于 2026-06-30 02:00 CST。X「For You」信息流经 opencli（136 条）。Web/arXiv 经搜索。官方 X API 未配置——跳过 X 发帖。由 research-digest 流水线编译。问题或勘误请在 [X](https://x.com/shuhangge) 回复。*
