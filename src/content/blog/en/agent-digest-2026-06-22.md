---
title: "Agent Architecture Daily Digest — June 22, 2026"
description: "Coding benchmarks called out for collapsing model+harness+environment into one score, SkillOps frames agent skill libraries as a technical-debt problem, Microsoft Foundry makes MCP the default interop layer, A2A protocol hits v1.0, Claude Code Dynamic Workflows hit stable parallelism, the June model flood continues"
pubDate: "2026-06-22"
lang: en
tags: ["Agent", "LLM", "AI Architecture", "Daily Digest"]
---

> ⚠️ **Note on today's sources:** X/Twitter "For You" collection failed this morning — the logged-in Chrome session on x.com has expired (AUTH_REQUIRED). Today's digest is therefore built from web search, arXiv search, and the Chinese-language community, with no live X items. The X feed will return once the session is re-authenticated.

## TL;DR — Today's Overview

1. **Coding benchmarks are misaligned with production**: A sharp new analysis (published June 21) argues SWE-bench Verified and peers evaluate at the *agent* level but collapse model, harness, and environment into a single end-to-end score — while production teams operate at the *system* level. Vendor scores aren't comparable because scaffold, tool, and evaluator differences dominate. — Source: codex.danielvaughan.com

2. **SkillOps reframes agent skills as a software-engineering problem**: Skill libraries accumulate "skill technical debt" — library-level defects that don't break any single skill but degrade future retrieval, composition, and execution. SkillOps proposes library-time maintenance as a first-class concern, mirroring how teams maintain code dependencies. — Source: arXiv:2605.13716

3. **Microsoft Foundry makes MCP the default integration layer**: At Build 2026, MCP is now native across Foundry, Agent 365, IQ context services, Teams SDK, and Copilot. Toolboxes (tool discovery/security), Foundry IQ (unified knowledge layer, now GA), and Hosted Agents (hypervisor-isolated compute) ship as production primitives. — Source: Microsoft Foundry Blog

4. **A2A protocol reaches v1.0 — agent-to-agent handoffs are real**: Where MCP connects a model to tools, A2A connects agents to each other. Reaching v1.0 means agent-to-agent task delegation is no longer a preview spec. A reported Q3 2026 MCP/A2A joint specification effort is underway. — Source: buildmvpfast.com, zylos.ai

5. **Claude Code Dynamic Workflows: parallel agents are stable at 10–20**: Community testing confirms Claude Code's Dynamic Workflows handle large refactors (multi-file migration, batch test fixes) via parallel subagents, stable up to ~10–20 agents; coordination overhead rises sharply past 30. With Opus 4.8 default and fast mode, Claude Code's positioning shifts from "terminal agent" to "orchestrable engineering environment." — Source: LearnAgent (中文社区)

6. **Claude now authors 80%+ of Anthropic's merged code**: Anthropic's recursive-self-improvement writeup reports that as of May 2026, over 80% of code merged into Anthropic's codebase was written by Claude — a milestone for AI-assisted development at the company building the agents. — Source: anthropic.com/institute

7. **ORAgentBench: can LLM agents do operations research?**: A new benchmark (June 18) tests whether LLM agents can solve challenging OR problems requiring multi-step reasoning, tool actions, and agent-authored justification — probing the boundary of agent capability on hard optimization. — Source: arXiv:2606.19787

8. **Chinese frontier models close the coding-agent gap**: DeepSeek V4 Pro, Kimi K2.6, and GLM-5.1 now appear on real software-engineering leaderboards — running on Claude Code as the harness base, showing the model is swappable but the harness is the moat. — Source: 搜狐 / Chinese community

9. **Latent-space parallelism for multi-agent reasoning**: A June 12 paper proposes direct latent-space synthesis for parallel branches in LLM reasoning, applied to tool-use QA and multi-agent diagnostic tasks — cutting the cost of branching by operating in latent rather than token space. — Source: arXiv:2606.14672

10. **The agent-protocol stack is hardening**: Multiple 2026 analyses converge on a two-layer protocol stack — MCP (model↔tools) + A2A (agent↔agent) + emerging ACP (agent commerce/payments) — with OpenAPI schemas as the lowest-common-denominator fallback. Practitioners report the architecture has converged faster than expected. — Source: turion.ai, ruh.ai

📊 Today's Numbers: **0 X items (auth gap) | 7 detailed papers | 8 product/company items | 6 Chinese community items | ~35 notable mentions | note: web-search-only collection**

---

## The Pattern: The Hard Problems Moved to the System Layer

The strongest signal across today's collection isn't a model release — it's that the conversation has decisively shifted from *"can the model do the task?"* to *"does the system around the model work in production?"*

Three threads converge:

- **Benchmarks are finally being read at the right level.** The Vaughn analysis (Jun 21) names the core problem plainly: end-to-end agent scores collapse model + harness + environment into one number, which is useless to teams who operate, swap, and debug those layers separately. The leaderboard era is maturing into a systems-engineering discipline.
- **Skills are becoming a managed asset class.** SkillOps names "skill technical debt" — the same decay that hits any shared code library, now applied to agent skills. This is the moment agent skills stopped being cool demos and started being software with maintenance obligations.
- **The protocol stack crystallized.** MCP (model↔tools) and A2A (agent↔agent) are both at production maturity, with Microsoft betting its entire agent platform on MCP-as-default. The interop layer that "nobody had six months ago" is now the spine of every converged engineer stack.

This is the agent operating system filling in. Models are still flooding in (GPT-5.6, Gemini 3.2, Qwen 3.7, DeepSeek V4.1 all in June) — but the architecturally interesting work is one layer up.

---

## Company Updates

### Microsoft Foundry: "AI App and Agent Factory" with MCP as the Spine

Microsoft's Build 2026 Foundry announcements are the most concrete enterprise agent-platform move this quarter. The headline framing — "AI app and agent factory" — is marketing, but the underlying primitives are real:

- **MCP is now the default integration layer** across Foundry, Agent 365, IQ context services, Teams SDK, and Copilot. Microsoft didn't bolt MCP on; it made it native.
- **Toolboxes** addresses tool discovery, management, and security — one of the biggest production pain points (which tools can an agent call, and how do you govern that?).
- **Foundry IQ** is generally available as the dedicated knowledge layer behind Foundry agents, unifying Work IQ, Fabric IQ, Azure SQL, File Search, and MCP sources behind one SLA-backed retrieval endpoint.
- **Hosted Agents** in Foundry Agent Service: hypervisor isolation, per-agent Entra ID, source-code deployment via `azd`, built-in content safety — expected GA in the coming weeks.

This is the enterprise stack answer to the agent-harness problem: governed tool access, a unified retrieval layer, and isolated compute per agent. For builders, it's a strong signal that the "agent factory" pattern (design → customize → deploy → monitor at scale) is becoming a productized category.

— [Microsoft Foundry Blog](https://devblogs.microsoft.com/foundry/agent-service-build2026/) · [Build 2026 analysis: arcade.dev](https://www.arcade.dev/blog/microsoft-build-2026-agent-stack/)

### Anthropic: Claude Writes 80%+ of Its Own Codebase

Anthropic's recursive-self-improvement writeup reports that as of May 2026, more than 80% of the code merged into Anthropic's codebase was authored by Claude. This is the most direct public datapoint on AI-assisted development velocity at a frontier lab — and it's coming from the company whose agent products (Claude Code, Cowork) are themselves the tools doing the writing.

The implication for agent builders: the harness + skills + review-loop combination is now good enough to carry the bulk of real production code, not just prototypes. The human role is shifting to review and architecture.

— [anthropic.com/institute/recursive-self-improvement](https://www.anthropic.com/institute/recursive-self-improvement)

### The June 2026 Model Flood

The model layer is still churning, but the deltas are now incremental rather than paradigm-shifting:

- **GPT-5.6** (OpenAI), **Gemini 3.2 / 3.5 Pro** (Google, multimodal mid-cycle refresh), **Qwen 3.7**, **DeepSeek V4.1**, and **Hunyuan** updates all landed in a ~six-week window ending mid-June.
- Anthropic shipped two sibling lines: **Claude Fable 5** (a Mythos-class model made safe for general use) and **Claude Mythos 5** GA, plus **Opus 4.8** as the default Claude Code model with fast-mode pricing (2x rate for ~2.5x speed).

The takeaway: model quality is no longer the differentiator it was. The harness, the skills, and the protocol stack matter more for outcomes than which frontier model you pick — which is exactly why the Chinese models can now compete on the *same* harness (Claude Code) and appear on real leaderboards.

— [Presenc AI June roundup](https://presenc.ai/research/june-2026-llm-release-roundup) · [getcreatr.com coding race](https://getcreatr.com/news/the-2026-ai-coding-race)

### Cursor Background Agents: Autonomous PRs from a Daemon Container

Cursor's background-agent architecture runs on a local, process-isolated daemon container. When a developer assigns a task, the editor delegates workspace context to the daemon, which works asynchronously and opens a PR for review. GitHub Copilot's coding agent operates similarly — picking up an assigned issue, working in a GitHub Actions environment, and opening a draft PR.

This is the async-development pattern crystallizing across tools: the agent isn't a pair-programmer in your editor, it's a teammate that runs in a container and submits work for review.

— [businesstechnavigator.com](https://businesstechnavigator.com/news/cursor-background-agents-autonomous-pr) · [GitHub Copilot plans](https://dev.to/ssojet/6-background-ai-agents-for-async-development-2b47)

---

## Papers

### Coding Benchmarks Are Misaligned with Production (Vaughan, Jun 21)

The freshest and most pointed piece in today's collection. The thesis: current benchmarks — SWE-bench Verified chief among them — evaluate at the *agent* level but collapse model, harness, and environment into one end-to-end score. In production, teams operate at the *system* level: they swap models, tune harnesses, and change environments independently, and they need signal on each. End-to-end numbers can't separate a good model from a good scaffold or evaluator, so vendor-reported scores aren't directly comparable.

Why it matters: this is the benchmark maturity moment. As agent systems get built at scale, "which model scored highest on the leaderboard" stops being a useful question; "which harness+scaffold+eval combination is reliable for *my* workflow" becomes the real one. Expect system-level evaluation frameworks to emerge as a category.

— [codex.danielvaughan.com (Jun 21)](https://codex.danielvaughan.com/2026/06/21/coding-benchmarks-misaligned-agentic-software-engineering-codex-cli-harness-feedback-loops/)

### SkillOps: Skill Libraries Get Their Own Technical-Debt Discipline

SkillOps (arXiv:2605.13716) names a previously underexplored failure mode: *skill technical debt* — the accumulation of library-level defects that don't break any individual skill in isolation but degrade future retrieval, composition, and execution as libraries grow. Existing skill-based agents focus on task-time retrieval, planning, and repair; SkillOps proposes library-time maintenance as a first-class concern.

The method is a method-agnostic plug-in: it works with hierarchical skill graphs and maintains a self-growing skill ecosystem over the library's lifetime. It treats agent skills the way mature engineering teams treat shared code dependencies — versioned, tested for composition, and periodically refactored.

Why it matters: as organizations accumulate hundreds of agent skills (the wshobson marketplace already lists 156+), the maintenance problem becomes real and structural. SkillOps is the first paper to formalize it. This is the "skills are software" thesis reaching its operational conclusion.

— [arXiv:2605.13716](https://arxiv.org/abs/2605.13716) · [GitHub: Hik289/SkillOps](https://github.com/Hik289/SkillOps)

### ORAgentBench: Can LLM Agents Do Operations Research?

ORAgentBench (arXiv:2606.19787, Jun 18) tests whether LLM agents can solve challenging operations-research problems — tasks requiring multi-step reasoning, tool actions, and agent-authored justification rather than single-shot answers. It probes the boundary of agent capability on hard optimization where correctness is verifiable but the reasoning path is long and tool-dependent.

Why it matters: OR is a domain with crisp ground truth (the optimal solution is checkable) but high reasoning complexity. It's a natural stress test for whether agent reasoning + tool use generalizes beyond coding and web tasks into quantitative decision-making. Expect this benchmark to surface where current agents break.

— [arXiv:2606.19787](https://arxiv.org/html/2606.19787v1)

### Latent-Space Synthesis for Parallel Branches in LLM Reasoning

A June 12 paper (arXiv:2606.14672) proposes direct latent-space synthesis for parallel branches in LLM reasoning, applied to tool-use agentic QA and multi-agent diagnostic tasks. Instead of running parallel reasoning branches in token space and merging text, the approach operates in latent space — cutting the token cost of branching and merging, which is the dominant expense in multi-branch agent workflows.

Why it matters: parallelism is how agents explore multiple solution paths, but token-space branching is expensive. Moving branching into latent space is a concrete efficiency lever for multi-agent and tree-search-style agent architectures.

— [arXiv:2606.14672](https://arxiv.org/html/2606.14672)

### PowerAgentBench-Dyn: Agents in Dynamic Power-System Workflows

PowerAgentBench-Dyn (arXiv:2606.20401, Jun 18) benchmarks LLM-based agents in multi-step power-system engineering workflows — interacting with software tools, interpreting intermediate results, and autonomously planning subsequent actions in a *dynamic* (changing) environment. It targets a real industrial domain where agents must adapt to shifting state, not just solve static puzzles.

Why it matters: dynamic-environment benchmarks are rarer and harder than static ones, and they expose whether an agent's planning loop holds up when the world changes under it. Power systems are a high-stakes testbed where correctness matters and tool interactions are non-trivial.

— [arXiv:2606.20401](https://arxiv.org/pdf/2606.20401)

### Beyond Tokens: Latent Communication in Multi-Agent Systems

A unified framework (June 2026) for latent communication in LLM-based multi-agent systems. The dominant protocol today is natural language — agents exchange messages as tokens. This work argues for latent-channel communication across reasoning, planning, and tool-use tasks, reducing the overhead of inter-agent messaging at scale.

Why it matters: as multi-agent systems grow (Microsoft Foundry already targets 200-agent scale), natural-language messaging becomes a bottleneck. Latent communication is the plumbing upgrade that makes large agent populations economical.

— [roboticscenter.ai](https://www.roboticscenter.ai/research/papers/beyond-tokens-a-unified-framework-for-latent-communication-in-llm-based-multi-agent-system-2606)

### Agent Skills Survey: The Skill Abstraction Layer Formalized

A consolidating survey (arXiv:2602.12430, updated to v3 in Feb 2026) focuses specifically on the emerging skill abstraction layer — natural-language instructions + executable code in a format agents trust — and its security implications. It covers skill architecture, acquisition, and a threat landscape where three concurrent studies (Oct 2025–Feb 2026) provide the first empirical characterization of skill-based attacks.

Why it matters: the survey codifies "skills" as a first-class architectural object and flags that their implicit-trust execution model creates a real attack surface. As skills become standard (SkillOps, marketplaces), the security dimension will become a build-vs-buy decision.

— [arXiv:2602.12430](https://arxiv.org/abs/2602.12430v3)

---

## Industry Leaders & Practitioner Analysis

### MCP vs Skills vs Subagents: A Practitioner's Layering Guide

A practical guide (fazm.ai) clarifies the three abstraction layers builders now juggle: **MCP tools** (atomic capability calls), **custom skills** (encapsulated expertise that's expensive to re-derive), and **subagent orchestration** (the highest layer, delegating whole sub-tasks to agents with their own context windows). The key insight: skills exist because re-deriving expertise from first principles every call is wasteful; subagents exist because some tasks need isolated context.

Why it matters: the confusion between "should this be a tool, a skill, or a subagent?" is one of the most common architectural questions in agent design. Clear layering rules reduce over- and under-engineering.

— [fazm.ai](https://fazm.ai/t/mcp-tools-skills-subagent-orchestration)

### The Agent Harness Is Now a Named Concept

A trends roundup (firecrawl) names the thing practitioners already feel: the *agent harness* — the software infrastructure that coordinates tool execution, memory, and state persistence across sessions — is now a recognized layer. Single-agent workflows are giving way to coordinated teams of specialized agents working in parallel, because complex tasks exceed any single context window.

Why it matters: naming the harness as a distinct concern is a precursor to it becoming a productized category (which Microsoft Foundry is already doing). Builders who think explicitly about their harness — rather than letting it emerge ad hoc — build more reliable agents.

— [firecrawl.dev agentic trends](https://www.firecrawl.dev/blog/agentic-ai-trends)

### Claude Code Agent Memory: The Layered Architecture

A deep-dive (orchestrator.dev) into Claude Code's layered memory architecture — how to engineer persistent, context-aware agents that retain what matters across sessions. This is the memory layer that turns a stateless model into something that learns over a project's lifetime.

Why it matters: memory is the difference between an agent that rediscovers your codebase every session and one that builds on accumulated context. The layered approach (short-term session, persistent project memory, cross-session recall) is becoming the standard pattern.

— [orchestrator.dev](https://orchestrator.dev/blog/2026-04-06--claude-code-agent-memory-2026/)

---

## Notable Mentions

**Product & Platforms**
- GitHub Copilot coding agent picks up assigned issues, runs in GitHub Actions, opens draft PRs — the async-PR pattern is now standard across Copilot, Cursor, and Codex. ([dev.to](https://dev.to/ssojet/6-background-ai-agents-for-async-development-2b47))
- Codex CLI is OpenAI's local terminal coding agent, open-source and Rust-based for speed; runs in VS Code, Cursor, Windsurf via IDE install. ([developers.openai.com/codex/cli](https://developers.openai.com/codex/cli))
- wshobson/agents multi-harness marketplace: 84 plugins / 192 agents / 156 skills, with native skills + subagents (April 2026 spec). ([github.com/wshobson/agents](https://github.com/wshobson/agents))
- Agent Toolkit for AWS ships 20+ agent skills for Claude Code / Codex / MCP-compatible agents — but load-time is a real gotcha. ([dev.to/aws](https://dev.to/aws/the-new-agent-toolkit-for-aws-includes-20-agent-skills-but-your-agent-might-never-load-them-1p6d))
- Best AI Coding Agents June 2026 leaderboard (morphllm) scores harnesses on accuracy and cost; new Copilot paid sign-ups were paused during billing rollout. ([morphllm.com](https://www.morphllm.com/best-ai-coding-agents-2026))
- CrewAI v1.14.6 (May 2026): 52k+ stars, ~5M monthly downloads, native MCP via `crewai-tools[mcp]` and A2A task delegation. ([morphllm.com framework guide](https://www.morphllm.com/ai-agent-framework))

**Protocols & Standards**
- MCP 2026 roadmap: stateless transport, server discovery, tasks, enterprise auth, triggers, streaming, skills, extensions, SDK v2. ([tedt.org](https://tedt.org/MCPs-2026-Roadmap/))
- A2A reached v1.0 — agent-to-agent handoffs are no longer a preview spec. ([buildmvpfast.com](https://www.buildmvpfast.com/blog/ai-engineer-stack-2026-mcp-a2a-protocol))
- Reported Q3 2026 MCP/A2A joint specification effort underway; convergence likely runs through NIST standards and W3C DID infrastructure. ([zylos.ai](https://zylos.ai/research/2026-03-26-agent-interoperability-protocols-mcp-a2a-acp-convergence/))
- Two-layer protocol stack (MCP + A2A + emerging ACP for agent commerce) is the converged 2026 engineer stack. ([turion.ai](https://turion.ai/blog/ai-agent-protocol-stack-2026/))

**Research & Benchmarks**
- SWE-bench Verified progress curve: 1.96% (Claude 2, 2023) to >80% in vendor-reported late-2025/early-2026 — but scores aren't comparable across vendors due to scaffold/tool/evaluator differences. ([thenewspaperdaily.com](https://thenewspaperdaily.com/top-7-benchmarks-that-actually-matter-for-agentic-reasoning-in-large-language-models/))
- "Every major agent benchmark just got hacked" — a skeptical read arguing leaderboards function as a de facto trust layer that's now strained. ([LinkedIn](https://www.linkedin.com/pulse/every-major-agent-benchmark-just-got-hacked-heres-what-kanis-patel-gl3yc))
- ForeSci (arXiv:2606.00644): evaluates LLM agents on forward-looking AI research planning tasks. ([arXiv](https://arxiv.org/html/2606.00644v2))
- Can LLM agents sustain long-horizon organizational dynamics? (arXiv:2606.01199) — simulating agent populations in organizational settings. ([arXiv](https://arxiv.org/html/2606.01199v1))
- SeClaw (arXiv:2606.02302): spec-driven security task synthesis for evaluating agent security across planning, memory, tool-use, and execution. ([arXiv](https://arxiv.org/html/2606.02302v1))
- The Evolution of Tool Use in LLM Agents (arXiv:2603.22862): from single-tool call to multi-tool orchestration, with memory-tool integration as the mainstream trend. ([arXiv](https://arxiv.org/html/2603.22862v2))

**Engineering Practice**
- Claude Code guide: 25 features including subagents, hooks, MCP, and Auto Mode with examples. ([marktechpost.com](https://www.marktechpost.com/2026/06/14/claude-code-guide-2026-25-features-with-examples-demo/))
- fast-agent: Python LLM agent framework with SKILL.md system, MCP OAuth/PKCE, deployable as MCP server or ACP agent. ([everydev.ai](https://www.everydev.ai/tools/fast-agent))
- Customizing agents: the five levers — rules, skills, agent modes, MCP, AgentIgnore; `AGENTS.md` as the project source-of-truth. ([habr.com](https://habr.com/ru/companies/veai/articles/1031992/))
- AI agent stack selection in 2026: frontier closed-source providers dominate production; the harness is the differentiator. ([thenuancedperspective.substack.com](https://thenuancedperspective.substack.com/p/how-to-choose-your-ai-agent-stack))

**Geopolitics & Industry**
- Huawei is transforming HarmonyOS into an AI-native OS where agents live in the system layer, not as standalone apps. ([peerlist.io](https://peerlist.io/cnyouzige/articles/ai-agents-news--june-2026-openai-robotics-huawei-harmonyos-a))
- OpenAI's newly established robotics division signals a push toward embodied intelligence and real-world deployment. ([peerlist.io](https://peerlist.io/cnyouzige/articles/ai-agents-news--june-2026-openai-robotics-huawei-harmonyos-a))
- Anthropic's reported IPO preparations and $965B valuation highlight the lab-to-public-company transition. ([kersai.com](https://kersai.com/june-2026-ai-news-anthropic-spacex-google-business-impact/))
- AI governance talks: executives from Anthropic, OpenAI, and Google engaged with governments on infrastructure and governance. ([marketingprofs.com](https://www.marketingprofs.com/opinions/2026/55065/ai-update-june-19-2026-ai-news-and-views-from-the-past-week))

---

## Chinese Community / 中文社区

> 注：今日 X 采集失败（登录过期），中文内容来自知乎、搜狐、腾讯云等技术社区的网页搜索。/ Today's Chinese section is from web search of Zhihu, Sohu, Tencent Cloud, etc., since X collection failed.

- **国产模型登上编程 Agent 榜单**：DeepSeek V4 Pro、Kimi K2.6、GLM-5.1 全部进入实测排行榜，以 Claude Code 为框架底座，在真实软件工程任务上展现出实战级能力 —— 说明模型可换，但 harness 才是护城河。([搜狐](https://www.sohu.com/a/1031393624_121124376))

- **Claude Code Dynamic Workflows 实测稳定**：大型重构（多文件迁移、批量测试修复、跨模块重命名）已可自动拆分并行；与 Auto mode 组合后解决"频繁确认"和"单 agent 瓶颈"。社区反馈并行上限在 10-20 个 agent 内稳定，超过 30 个协调开销明显上升。配合 Opus 4.8 默认模型和 fast mode，Claude Code 定位从"终端 Agent"变为"可编排的软件工程环境"。([LearnAgent](https://learnagent.org/library/compare/ai-coding-agents-2026-mid-year/))

- **Claude Code 自定义 Agent 实战**：把重复指令固化为一个 Markdown 文件（定义 system prompt、工具权限、模型选择），之后一句话即可调用专属智能体 —— 这是 skill 化趋势在中文社区的落地。([腾讯云开发者社区](https://cloud.tencent.com/developer/article/2657591))

- **AI Coding 工具红黑榜**：Claude Code Skills 实战——把"博客发布前自检"等重复流程写成 Skill，配合 `context: fork` 不污染主对话。这是 skills-as-software 在个人工作流中的真实应用。([陈广亮技术博客](https://chenguangliang.com/posts/blog149_ai-coding-tools-2026-review/))

- **2026 年 AI Agent 技术全景**：12 大主流框架深度解析与架构演进趋势，涵盖核心架构、框架选型与未来方向。([知乎专栏](https://zhuanlan.zhihu.com/p/2026254728342905724))

- **ICML 2026 LLM×Graph 论文总结**：Graph4LLM、Graph4Agent、智能体记忆（Memory）、AgenticRL、RAG 方向的论文整理 —— 图结构与 agent 记忆的结合是新趋势。([知乎专栏](https://zhuanlan.zhihu.com/p/2039861022064907854))

---

*Collected 2026-06-22 via web search, arXiv search, and Chinese-community search. X/Twitter "For You" feed unavailable (session expired). Tomorrow's run will retry X collection.*
