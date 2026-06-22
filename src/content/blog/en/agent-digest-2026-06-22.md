---
title: "Agent Architecture Daily Digest — June 22, 2026"
description: "Databricks open-sources Omnigent meta-harness, MCP moves to Linux Foundation, the /goal validator-loop pattern stabilizes in agentic coding, SING solves tool discovery at scale, compositional skill routing formalizes decompose-retrieve-compose, PreAct caches repeated CUA tasks, VISUALSKILL adds multimodal skills, PRPO targets Pareto-optimal tool efficiency"
pubDate: "2026-06-22"
lang: en
tags: ["Agent", "LLM", "AI Architecture", "Daily Digest"]
---

## TL;DR — Today's Overview

> Top 10 things to know today:

1. **Databricks open-sources Omnigent — a meta-harness above all coding agents**: Instead of picking one of Claude Code, Codex, Cursor, or custom agents, Omnigent sits above them all. You compose, govern, sandbox, and swap harnesses without rewriting orchestration code — policies survive model changes. — [@Dinosn](https://x.com/Dinosn/status/2067549695732293775)

2. **SING: intention graphs solve tool discovery at scale**: As agent tool libraries grow to hundreds of APIs, finding the right tool becomes the bottleneck. SING builds an intention→tool graph that retrieves dynamically by task state, improving recall by 59.8% while cutting tool-schema context exposure by 99.8%. — [arXiv:2606.16591](https://arxiv.org/abs/2606.16591v2)

3. **Compositional Skill Routing formalizes decompose-retrieve-compose**: Real tasks need multiple skills composed, not just single-skill selection. This paper formalizes the problem class and the execution-plan composition pattern that scalable agent architectures require. — [arXiv:2606.18051](https://arxiv.org/abs/2606.18051v1)

4. **MCP donated to the Linux Foundation — vendor-neutral governance**: Anthropic moved the Model Context Protocol to the Agentic AI Foundation (AAIF), co-founded with Block under the Linux Foundation. MCP tools and servers will be interoperable across all providers, not just Anthropic. — [@AnthropicAI](https://x.com/AnthropicAI/status/1998437922849350141)

5. **The `/goal` validator-loop is now a standard agentic-coding primitive**: Both Codex and Claude Code shipped a `/goal` command that runs an autonomous loop until a small validator passes. Autonomous iteration gated by verification is no longer experimental — it's productized. — [@sachinrekhi](https://x.com/sachinrekhi/status/2064013928892645786)

6. **PreAct: computer-use agents get faster on repeated tasks**: Today's CUAs re-read screens and re-reason every action even for tasks they've done before. PreAct caches interaction trajectories and replays them, amortizing cost dramatically for recurring workflows. — [arXiv:2606.17929](https://arxiv.org/abs/2606.17929v1)

7. **PRPO targets Pareto-optimal tool-using agents**: Current RL maximizes task accuracy and ignores tool efficiency. PRPO aligns agents along both axes using dominance-based advantage estimation — directly addressing the accuracy-vs-cost trade-off every production agent hits. — [arXiv:2606.16111](https://arxiv.org/abs/2606.16111v1)

8. **VISUALSKILL: text-only skills are insufficient for GUI agents**: Computer-use agents lose information when skills are text-only. VISUALSKILL represents skills with multimodal artifacts (screenshots, visual annotations), improving long-horizon task performance on unseen software. — [arXiv:2606.18448](https://arxiv.org/abs/2606.18448v1)

9. **Decoupling search from reasoning — vendor-agnostic grounding**: Native search grounding bundles retrieval policy, provider, cost, and latency behind one model boundary. This paper separates them, making grounding inspectable and portable across providers. — [arXiv:2606.18947](https://arxiv.org/abs/2606.18947v1)

10. **Hermes ships Tool Search for MCP**: Instead of dumping all MCP tools into context, Hermes Agent discovers relevant tools on demand per task — reducing context bloat and improving selection accuracy. A key pattern as tool libraries scale. — [@AgenticAIFdn](https://x.com/AgenticAIFdn/article/2061490253559689322)

📊 Today's Numbers: **16 X highlights | 10 detailed papers | 27 notable mentions | 8 Chinese community items | 61 total items**

---

## The Pattern: The Agent Stack Is Composting Into Layers

The strongest signal across today's collection isn't a single release — it's that the agent ecosystem is rapidly stratifying into distinct architectural layers, each with its own design problems:

- **The meta-harness layer** (Omnigent) sits above individual coding agents, making the choice of Claude Code vs Codex vs Cursor a swappable implementation detail rather than an architectural commitment.
- **The skill/tool layer** (SING, Compositional Skill Routing, VISUALSKILL, Hermes Tool Search) is formalizing how agents discover, select, compose, and represent tools — moving from "dump everything in context" to intention-graph-driven retrieval.
- **The protocol layer** (MCP under Linux Foundation governance) is becoming vendor-neutral infrastructure, the way HTTP became for the web.
- **The efficiency layer** (PRPO, PreAct) is emerging because accuracy alone is no longer enough — tool-call efficiency, latency, and cost on repeated tasks are now first-class optimization targets.

This is the agent operating system filling in. The model is still the engine, but the architecturally interesting work has moved up the stack.

---

## Company Updates

### Databricks: Omnigent — A Meta-Harness for All Coding Agents

The most architecturally significant release this week. [Omnigent](https://x.com/Dinosn/status/2067549695732293775) is an open-source meta-harness that orchestrates Claude Code, Codex, Cursor, Pi, and custom agents under a single governance layer. You can swap harnesses without rewriting orchestration code, enforce policies and sandboxing uniformly, and share agent configurations across teams. The key trade-off Databricks notes: context cache drops when handing off between different model backends — so cross-harness composition isn't free. But the value proposition is real: agent lock-in is a deployment risk, and a layer above individual harnesses means your policies, evals, and observability survive model swaps. ([Databricks blog, Jun 13](https://www.databricks.com/blog))

### Anthropic: MCP Donated to the Linux Foundation

Anthropic [moved the Model Context Protocol](https://x.com/AnthropicAI/status/1998437922849350141) to the Agentic AI Foundation (AAIF), a directed fund under the Linux Foundation co-founded with Block. In one year, MCP went from an Anthropic internal spec to the default tool-access protocol for the agent ecosystem. Linux Foundation governance means MCP servers and tools will be interoperable across all model providers — Google, OpenAI, Mistral, and Chinese labs — not locked to Anthropic's roadmap. A reported Q3 2026 joint MCP/A2A specification effort is underway.

### Anthropic: Agent SDK Billing Split (June 15)

[Anthropic separated](https://x.com/unicity_labs/article/2066447178516611244) Agent SDK billing from interactive Claude.ai usage. Programmatic agent usage now bills at standard API rates rather than subscription rates. This changes the unit economics of building agents on Claude: if you're running agents at scale, cost modeling shifts from "subscription seats" to "per-token API pricing." Relevant for any team deciding whether to build on the Agent SDK vs. direct API calls.

### Linear: Linear Agent Powered by Claude Code and Codex

[Linear launched Linear Agent](https://x.com/linear/status/2065143120468017326), integrating agentic coding into project management workflows. Claude Code and Codex are the initial backends, with more coming. This matters as a signal: mainstream project-management tools are now embedding coding agents, moving agents from developer-only tools into everyday team workflows. When your issue tracker can directly write and review code, the feedback loop between planning and execution tightens dramatically.

### Nous Research: Hermes Agent Ships Tool Search for MCP

[Nous Research added Tool Search](https://x.com/AgenticAIFdn/article/2061490253559689322) to Hermes Agent — instead of pre-loading every available MCP tool into context, the agent discovers relevant tools on demand per task. This is the same architectural pattern that SING (below) formalizes in research: dynamic tool retrieval beats static context loading as tool libraries scale. Reducing context bloat improves both tool-selection accuracy and reasoning quality.

### Supabase: MCP Server + Agent Skills Plugin

[Supabase shipped a plugin](https://x.com/supabase/article/2063245852026777754) bundling their MCP server with agent skills — letting agents query databases, manage migrations, and deploy Edge Functions directly. Database-as-a-service companies shipping MCP-first integrations confirms MCP as the default tool interface layer for infrastructure, not just a Claude-specific convenience.

---

## Industry Leaders

### Sachin Rekhi: The Four Stages of Agentic Coding

[Sachin Rekhi maps](https://x.com/sachinrekhi/status/2064013928892645786) the evolution of agentic coding through four stages, culminating in a key productization milestone: in spring 2026, both Codex and Claude Code shipped a `/goal` command that runs an autonomous "ralph loop" until a small validator passes. The validator-gated autonomous loop is becoming a standard primitive — not an experiment, but a shipped feature in the two leading coding agents. This means the pattern of "iterate until verification passes" is now accessible to every developer using these tools, not just teams building custom harnesses.

### Matt Van Horn: Every Agentic Engineering Hack (June 2026)

A [practical, tested collection](https://x.com/mvanhorn/article/2061877533885473181) of workflow optimizations for daily agent-assisted development — including making every new terminal tab open directly into Claude Code, context management patterns, and tool-permission strategies. Directly actionable for builders who live in coding agents daily. The meta-insight: agentic engineering is developing its own craft knowledge, separate from traditional software engineering practices.

### Aaron Levie: Enterprise Agent Deployment Is the Next Frontier

[Aaron Levie notes](https://x.com/levie/status/2051344780328858040) that both Anthropic and OpenAI are launching enterprise AI agent deployment initiatives, calling it "a trend that's early but going to get very big fast." High-level observation without technical detail, but the signal matters: enterprise agent deployment — governance, security, audit trails, integration with existing IT — is where the commercial frontier is moving. Builders who solve enterprise deployment constraints (compliance, data residency, observability) will capture this market.

---

## Trending

### Hermes Desktop: The First Agent Built Around Persistent Context

[Hermes Desktop launched](https://x.com/PrajwalTomar_/status/2066195642997969255) in early June 2026, positioned as the first AI agent built around persistent context — builders no longer need to re-explain their codebase and goals every morning. This addresses a core friction: the "cold start" problem where every new agent session loses accumulated context. Whether persistent context is a feature or a product category remains to be seen, but the pain point is real — context re-establishment is a daily tax on agent-assisted workflows.

### Colin Hacks: Which Coding Agent Can Dispatch Subagents Dynamically?

[Colin Hacks asks](https://x.com/colinhacks/status/2067004040689647720) a pointed question: which coding agent can dispatch subagents in a truly dynamic way? His answer: "idk the answer but it's not claude, codex, or opencode." This highlights a real capability gap — current coding agents handle subagent dispatch in limited, predefined ways, but truly dynamic subagent orchestration (where the parent agent decides at runtime how to decompose and delegate based on task structure) remains unsolved by mainstream tools. The gap between "parallel subagents for known task types" and "dynamic decomposition for novel tasks" is where the next architectural leap needs to happen.

---

## Rising Stars

### "Dive into Claude Code" Paper Recommended by Andriy Burkov

[Andriy Burkov recommends](https://x.com/burkov/status/2048233381305942381) the paper "Dive into Claude Code: The Design Space of Today's and Future AI Agent Systems" (arXiv:2604.14228) — a systematic analysis of Claude Code's architecture that identifies 5 motivating human values (competence, transparency, user control, efficiency, trust) traced through 13 design principles. The most thorough academic analysis of a production agentic coding system. Open-source repo at [github.com/VILA-Lab/Dive-into-Claude-Code](https://github.com/VILA-Lab/Dive-into-Claude-Code).

### Claude Architect Certification: The Blueprint Codifies Best Practices

The [Claude Architect certification exam](https://x.com/sharyph_/status/2037393353478959336) blueprint defines 5 domains: 27% on agentic architecture (stop_reason loops, isolated subagent context) and 18% on tool design and MCP (keep agents to 4-5 scoped tools, not 18). The "4-5 scoped tools" rule is a concrete design guideline emerging from Anthropic's formalization of agent architecture knowledge — implying a maturing consensus on best practices.

### Antigravity SDK for Agentic PR Review + Gemini CLI Shutdown

[Building an agentic PR reviewer](https://x.com/RemikSamborski/article/2067690122703765510) with the Antigravity SDK, coinciding with the announced shutdown of Gemini CLI and Gemini Code Assist IDE extensions. Google's pivot away from its CLI coding agent (announced June 18, 2026) signals a strategy shift — potentially consolidating around a different interface or provider. Meanwhile, Antigravity SDK emerges as a new option for building custom review agents outside the major platform ecosystems.

### Hermes MCP Servers Off by Default — Security-First Design

A [daily recap notes](https://x.com/NeoAIForecast/article/2068632723674263864) that Hermes MCP servers remain off by default unless manually re-enabled via `hermes tools` or `hermes setup agent` commands. This is a deliberate security-first design choice — agents don't auto-connect to external tools without explicit opt-in. As agents gain tool access to databases, file systems, and APIs, default-off becomes the safe baseline. Builders should treat tool-enablement as an explicit, audited decision.

### Nous Research × NVIDIA × Stripe: Business Agent Hackathon

[Nous Research launched](https://x.com/NousResearch/status/2066921443548348436) the Hermes Agent Accelerated Business Hackathon with NVIDIA and Stripe — challenging builders to create agents that can earn, spend, and run real operations. The theme (earn/spend/run operations) defines the next frontier: agents handling real economic transactions, not just demos. When agents have wallets and can transact, the architecture problems shift from "can it complete the task?" to "can it be trusted with money?"

---

## Papers

### SING: Synthetic Intention Graph for Scalable Active Tool Discovery

**Authors:** Qiao Xiao, Haochen Shi, Yisen Gao et al. | [arXiv:2606.16591](https://arxiv.org/abs/2606.16591v2) | June 15, 2026

SING builds an intention-aware active tool discovery framework using a synthetic intention graph that links user intentions, tool capabilities, and collaboration patterns. It retrieves tools dynamically based on evolving task states rather than loading all tool schemas upfront. Results: Global Recall@5 improves by up to 59.8% while reducing tool-schema context exposure by 99.8%. This is directly relevant to the agent OS/tool-graph thesis — as harnesses connect to hundreds or thousands of APIs, discovering the right tool becomes the critical bottleneck, and intention-graph-driven retrieval is a fundamentally new approach.

### Compositional Skill Routing: Decompose, Retrieve, and Compose

**Authors:** Xueping Gao et al. | [arXiv:2606.18051](https://arxiv.org/abs/2606.18051v1) | June 16, 2026

Formalizes the Compositional Skill Routing problem: given a complex query and a large skill library, decompose the query into sub-tasks, retrieve relevant skills per sub-task, and compose them into a coherent execution plan. This addresses a core gap — real tasks need multiple skills composed, not just single-skill selection. The decompose-retrieve-compose pattern is fundamental to scalable agent architectures and parallels how Hermes Agent's own skill system works.

### PreAct: Computer-Using Agents that Get Faster on Repeated Tasks

**Author:** Bojie Li | [arXiv:2606.17929](https://arxiv.org/abs/2606.17929v1) | June 16, 2026

PreAct lets computer-using agents cache and replay interaction patterns for tasks they've seen before — avoiding re-reading the screen and re-reasoning every action on repeated tasks. By caching interaction trajectories and replaying them when similar tasks recur, PreAct significantly reduces latency and cost while maintaining accuracy. A must-have optimization pattern for production CUAs, where recurring workflows (daily reports, routine data entry, repeated UI sequences) dominate actual usage.

### VISUALSKILL: Multimodal Skills for Computer-Use Agents

**Authors:** Ziyan Jiang, Li An, Yujian Liu et al. | [arXiv:2606.18448](https://arxiv.org/abs/2606.18448v1) | June 16, 2026

Extends skill libraries for computer-use agents to include multimodal (visual) representations, since GUI interaction is inherently visual and text-only skills lose information. VISUALSKILL represents skills with screenshots, visual annotations, and UI element layouts — improving long-horizon task performance on unseen software. The key insight: text-only skill descriptions are insufficient for visual GUI tasks, and multimodal artifacts capture context that text misses.

### PRPO: Pareto-Optimal Tool-Integrated Agents

**Authors:** Junyi Li, Xiaowei Qian, Yingyi Zhang et al. | [arXiv:2606.16111](https://arxiv.org/abs/2606.16111v1) | June 15, 2026

Introduces Pareto Ranking Policy Optimization (PRPO) — a two-stage method (SFT warm-up then Pareto-ranking-based RL) that aligns tool-using agents along both task accuracy AND tool-use efficiency, rather than accuracy alone. Uses dominance-based advantage estimation to directly target the accuracy-vs-efficiency trade-off. Tool-use efficiency (fewer calls, lower latency/cost) is a core practical deployment constraint that today's RL methods ignore by maximizing only accuracy.

### Decoupling Search from Reasoning: Vendor-Agnostic Grounding

**Authors:** Emmanuel Aboah Boateng, Kyle MacDonald, Amardeep Kumar et al. | [arXiv:2606.18947](https://arxiv.org/abs/2606.18947v1) | June 17, 2026

Proposes a vendor-agnostic grounding architecture that decouples retrieval policy, provider choice, evidence injection, cost, and latency from generation — making search grounding inspectable and portable. The core argument: native search grounding bundles everything behind a single model-provider boundary, preventing independent tuning. Decoupling enables teams to swap retrieval backends, audit evidence injection, and control costs without changing the model.

### ProvenanceGuard: Source-Aware Factuality for MCP-Based Agents

**Authors:** Ander Alvarez, Santhiya Rajan, Samuel Mugel et al. | [arXiv:2606.18037](https://arxiv.org/abs/2606.18037v1) | June 16, 2026

Proposes source-aware factuality verification for MCP-based agents that traces answers back to specific evidence sources (search, APIs, databases, clinical records), not just pooled evidence. Standard factuality metrics test whether answers are supported by pooled evidence but don't verify source-specific provenance. As MCP becomes the standard interface for agent tool use, provenance-aware factuality is essential for trustworthy agents — especially in high-stakes domains like clinical records.

### Automating SKILL.md Generation via Interaction Trajectory Mining

**Authors:** Yuexing Hao, Xiaomin Li et al. | [arXiv:2606.20363](https://arxiv.org/abs/2606.20363v1) | June 18, 2026

A three-stage pipeline that segments GUI interaction trajectories, clusters them, and auto-generates reusable skill libraries for computer-use agents. Directly addresses whether agent skill libraries can be automatically mined from interaction data rather than hand-authored — a core scalability question. If skills can be auto-generated from observed interactions, the skill library grows organically with usage rather than requiring manual curation.

### OpenClaw-Skill: Collective Skill Tree Search

**Authors:** Tianyi Lin, Chuanyu Sun, Jingyi Zhang et al. | [arXiv:2606.16774](https://arxiv.org/abs/2606.16774v1) | June 15, 2026

A framework that automatically constructs reusable skills for LLM agents via collective skill tree search — enhancing tool use, multi-step reasoning, and dynamic environments. Rather than hand-authoring skills, the agent builds its own skill tree through search. Core to the "trainable skills" thesis in agent architecture: skills become learnable, evolvable objects, not static configuration files.

### VisualClaw: Real-Time Personalized Agent for the Physical World

**Authors:** Haoqin Tu, Jianwen Chen, Zijun Wang et al. | [arXiv:2606.16295](https://arxiv.org/abs/2606.16295v1) | June 15, 2026

A cascade-based VLM agent that reduces a 1-hour live-streaming session from ~3,600 API uploads to only 5-20 calls, while self-evolving its scaffold post-deployment to personalize to the user. Directly tackles two deployment bottlenecks: the cost of dense video processing and static post-deployment scaffolds. The cascade cost-reduction pattern (filter cheaply, escalate only when needed) is broadly transferable to text/tool-centric agents too.

---

## Notable Mentions

**Tool & Skill Architecture**
- **ToolPro**: Represents agent tool intent as executable programs (with loops, conditionals, joins, retries) rather than static API endpoints — a program-as-tool-interface pattern for the agentic web. ([arXiv:2606.19992](https://arxiv.org/abs/2606.19992v1))
- **Large Language Models Do Not Always Need Readable Language**: Investigates whether LLMs can use compact, non-human-readable encodings for model-to-model communication, potentially reducing token overhead in multi-agent pipelines. ([arXiv:2606.19857](https://arxiv.org/abs/2606.19857v1))
- **SafeClawBench**: Separates three harm stages in tool-using agents — semantic (unsafe text), audit-evidence (provenance), and sandbox (actual tool effects like writing memory or modifying databases). ([arXiv:2606.18356](https://arxiv.org/abs/2606.18356v1))

**Multi-Agent Systems**
- **Contagion Networks**: Formal framework measuring how evaluator biases propagate through multi-agent LLM systems — biased evaluators contaminate the entire reasoning chain. ([arXiv:2606.20493](https://arxiv.org/abs/2606.20493v1))
- **On the Reliability of Networks of AI Agents**: Applies density evolution and stopping set analysis (from coding theory) to understand when networks of imperfect agents succeed or fail at combining solutions. ([arXiv:2606.18121](https://arxiv.org/abs/2606.18121v1))
- **AdaSTORM**: Adaptive spatio-temporal multi-agent collaboration to overcome the scaling bottleneck of LLM reasoning on dynamic graphs. ([arXiv:2606.16328](https://arxiv.org/abs/2606.16328v1))
- **Multi-Agent Multi-Objective Optimization**: A multi-agent system for cost-minimization under performance constraints in dynamic environments using RL. ([arXiv:2606.20236](https://arxiv.org/abs/2606.20236v1))
- **Hierarchical Control in Multi-Agent Games**: LLM-based planning combined with RL execution for complex multi-agent game environments — the planner/executor split pattern. ([arXiv:2606.20014](https://arxiv.org/abs/2606.20014v1))

**Evaluation & Benchmarks**
- **Beyond Static Leaderboards**: Aggregates 14 parallel implementation studies of an MCP-based industrial agent benchmark — finding no single benchmark covers more than 4-5 deployment dimensions. Predictive validity across benchmarks is limited. ([arXiv:2606.19704](https://arxiv.org/abs/2606.19704v1))
- **How Inference Compute Shapes Frontier LLM Evaluation**: Shows evaluation results are increasingly sensitive to inference compute allocation, yet many evaluations don't control for it — making cross-system comparisons unreliable. ([arXiv:2606.17930](https://arxiv.org/abs/2606.17930v1))
- **Black-Box Uncertainty Estimation for LLMs**: Systematically evaluates uncertainty estimation methods accessible via API only — essential for trustworthy agent systems in the practical black-box setting. ([arXiv:2606.19868](https://arxiv.org/abs/2606.19868v1))
- **LabOSBench**: Benchmark of web-based scientific-instrument simulators for evaluating computer-use agents on tasks with hard procedural ordering and feedback-driven parameter adjustment. ([arXiv:2606.16802](https://arxiv.org/abs/2606.16802v1))

**Domain-Specific Agent Applications**
- **AgentFinVQA**: Deployable multi-agent pipeline for auditable financial chart QA with built-in audit trails and on-premise deployment patterns. ([arXiv:2606.19782](https://arxiv.org/abs/2606.19782v1))
- **Guava**: Harness-based approach to embodied manipulation combining LLM reasoning with external perception/action modules — an alternative to end-to-end VLA. ([arXiv:2606.18363](https://arxiv.org/abs/2606.18363v1))
- **Zero-Shot Dexterous Manipulation**: Uses VLM reasoning with multi-view RGB to produce 3D task plans without training end-to-end policies. ([arXiv:2606.19340](https://arxiv.org/abs/2606.19340v1))
- **AI-Assisted Scientific Workflow Management**: Uses LLMs to automate workflow design, implementation, and debugging in scientific WMS. ([arXiv:2606.18425](https://arxiv.org/abs/2606.18425v1))
- **LectūraAgents**: Multi-agent framework for adaptive personalized AI-assisted learning. ([arXiv:2606.16428](https://arxiv.org/abs/2606.16428v1))
- **PowerAgentBench-SS**: Benchmarks whether LLM agents can execute engineering workflows in power systems. ([arXiv:2606.18789](https://arxiv.org/abs/2606.18789v1))
- **GeoDisaster**: Benchmarks orchestrated agents on tool-grounded spatial reasoning for disaster geo-intelligence. ([arXiv:2606.17246](https://arxiv.org/abs/2606.17246v1))

**Governance & Safety**
- **Architectural Wisdom**: Framework arguing AI systems need architectural mechanisms to question whether an objective should be optimized at all — not just optimization toward under-specified goals. ([arXiv:2606.16319](https://arxiv.org/abs/2606.16319v1))
- **Library-Aware Doubles and Iterative Repair**: Automated unit-test authoring workflow for LLM-generated tests in firmware — library-aware test generation with iterative repair. ([arXiv:2606.19725](https://arxiv.org/abs/2606.19725v1))

---

## Chinese Community / 中文社区

> Today's Chinese content is from Zhihu web search, covering agent architecture trends and framework analyses.

- **国产模型登上编程 Agent 榜单**: DeepSeek V4 Pro、Kimi K2.6、GLM-5.1 全部进入实测排行榜，以 Claude Code 为框架底座 —— 模型可换，但 harness 才是护城河。

- **2026 年 AI Agent 技术全景**: 12 大主流框架深度解析，现代 Agent 标准架构包含感知→决策→行动→记忆完整闭环。([知乎专栏](https://zhuanlan.zhihu.com/p/2026254728342905724))

- **AI Agent 完整工作流全景解析**: 深度拆解 Agent 的感知、规划、记忆与行动闭环，结合 Gartner 与 McKinsey 数据，提供可落地的架构指南。([知乎专栏](https://zhuanlan.zhihu.com/p/1996954141231190461))

- **15 篇 AI Agent 研报**: 架构向「云原生+Agent 协同」重构，支持多 Agent 协同与微秒级推理。([知乎专栏](https://zhuanlan.zhihu.com/p/1996902325206405568))

- **2026：Agent 之年**: 智能系统实现真正的多模态理解与执行，Zero-trust 安全架构强调每一步操作的验证与监控。([知乎专栏](https://zhuanlan.zhihu.com/p/2005591914448193177))

- **LangGraph 成为生产级 Agent 运行时事实标准**: LangChain 发布 Deep Agents（基于 LangGraph 的超级 Agent Harness），与 NVIDIA 合作。发现 LangChain/LangGraph 路径遍历和 SQL 注入漏洞。([知乎专栏](https://zhuanlan.zhihu.com/p/2021064941650679235))

- **Hermes 智能体研究报告**: Hermes Agent 由 Nous Research 开发的开源自主 AI 智能体，2026年2月正式发布，与 OpenClaw 对比分析。([知乎专栏](https://zhuanlan.zhihu.com/p/2026622473097978502))

- **Agentic AI 十大关键趋势**: 系统架构从单体应用向分布式智能体网络演进，IBM 预测将出现 Agent 控制平面和多 Agent 仪表盘。([知乎专栏](https://zhuanlan.zhihu.com/p/1991451643544355292))

---

*Collected via X web search, arXiv API, and Zhihu web search. X "For You" feed was unavailable (session auth gap) — X items come from keyword and company-account web search instead. Full candidate archive saved locally.*
