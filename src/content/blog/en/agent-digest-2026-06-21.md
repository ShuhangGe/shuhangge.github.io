---
title: "Agent Architecture Daily Digest — June 21, 2026"
description: "LLM-as-Code challenges LLM-as-orchestrator paradigm, ToolPro redefines agent-tool interface with compiled tool programs, Unreal Engine 5.8 embeds MCP at process level, deep anatomy of Claude Code's agent loop, 50B-token Claude Code vs Codex comparison, SING for scalable tool discovery, compositional skill routing"
pubDate: "2026-06-21"
lang: en
tags: ["Agent", "LLM", "AI Architecture", "Daily Digest"]
---

## TL;DR — Today's Overview

1. **LLM-as-Code paper challenges the dominant agent paradigm**: Argues token explosion and control-flow hallucination are architectural consequences — not bugs — of letting the LLM orchestrate control flow. Proposes deterministic program control with the LLM invoked only for reasoning. — Source: arXiv:2606.15874

2. **ToolPro redefines the agent-tool interface**: Instead of stepwise API calls, agents emit compiled tool programs encoding loops, conditionals, retries, and effect types over MCP-style services. Cuts latency 53% and client traffic 96%. — Source: arXiv:2606.19992

3. **Unreal Engine 5.8 ships MCP at the process level**: Epic didn't build a Copilot plugin — they embedded an MCP server inside the Unreal Editor so any MCP-compatible agent (Claude Code, Cursor, Codex) can drive the editor over local HTTP. — Source: @llmgram

4. **Deep anatomy of Claude Code's agent loop**: A full breakdown of context assembly, the async generator loop, 43 built-in tools + MCP tools, and the permission system — essentially a blueprint for building agent harnesses. — Source: @Siddharth87

5. **50B tokens comparing Claude Code vs Codex multi-agent**: After weeks of heavy usage, the key architectural difference: Codex's CLI UI smoothness vs Claude Code's subagent-to-leader messaging mechanism. — Source: @xanderai

6. **SING: scalable active tool discovery for LLM agents**: As tool ecosystems grow to thousands of APIs, Synthetic Intention Graph replaces exhaustive schema injection with intention-aligned retrieval. — Source: arXiv:2606.16591

7. **SkillWeaver formalizes compositional skill routing**: Decompose a query into sub-tasks, retrieve the right skill for each, compose an executable dependency DAG. Ships with a 300-query benchmark. — Source: arXiv:2606.18051

8. **ProvenanceGuard: first source-aware factuality verifier for MCP**: Checks whether each atomic claim is attributed to the correct evidence source — not just supported by pooled evidence. Identifies a novel MCP-specific hallucination class: cross-source conflation. — Source: arXiv:2606.18037

9. **Enterprise multi-agent orchestration at 200-agent scale**: 208 production scenarios reveal that agent discovery noise — not task complexity — is the dominant bottleneck at enterprise scale. DAG and ReAct both degrade sharply. — Source: arXiv:2606.20058

10. **Multi-Agent Transactive Memory**: Extends RAG from human-authored to agent-generated artifacts — indexing agent trajectories so agent populations retrieve and reuse procedural knowledge instead of rediscovering solutions. — Source: arXiv:2606.19911

📊 Today's Numbers: **10 detailed highlights | 10 papers | 37 notable mentions | 12 X items | 8 Chinese community items | 67 total items**

---

## The Pattern: Agents Are Getting an Operating System

The strongest signal across today's collection isn't any single item — it's the convergence. Papers, product launches, and practitioner posts are all describing the same architecture emerging around agents:

- **Control flow is leaving the LLM** (LLM-as-Code, DAG orchestration, tool programs)
- **Skills are becoming composable objects** (SkillWeaver, CSTS, SKILL.md mining)
- **MCP is becoming the standard interop layer** (Unreal Engine, DSG grounding, ProvenanceGuard, Cua Driver)
- **Memory is becoming population-level infrastructure** (MATM, MemFactory, MemoryArena)
- **Tool discovery is scaling beyond prompt injection** (SING, compositional routing)

This is the agent-OS stack crystallizing in real time.

---

## Company Updates

### Unreal Engine 5.8: MCP as Process-Level Control

Epic didn't build a better Copilot. Unreal 5.8 embeds an MCP server directly inside the Unreal Editor process, so any MCP-compatible AI agent — Claude Code, Cursor, MCP Inspector — can drive the editor over a local HTTP connection. This is a process-level integration: agents run in the docked terminal and control editor state directly, with zero context-switching tax.

This is a major signal of MCP becoming the standard interop layer for complex creative and engineering tools. The agent isn't a plugin inside the editor; it's a controller outside the editor, connected via a protocol.

— [@llmgram](https://x.com/llmgram/status/2067371338348515441)

### Cua Driver: Background Computer-Use for Windows

Cua Driver now supports background computer-use for Windows. Any agent — Claude Code, Codex, or a custom loop — can drive real Windows apps through CLI or MCP while the desktop stays foreground-usable, with true multi synthetic pointer support.

Background computer-use that keeps the desktop usable is a meaningful UX advance for agent-driven GUI automation. MCP as the control interface shows the protocol extending well beyond coding tools.

— [@trycua](https://x.com/trycua/status/2059688960838828391)

---

## Industry Leaders

### The Anatomy of Claude Code — A Blueprint for Agent Harnesses

A deep technical breakdown of Claude Code's internal architecture. The agent loop runs in 8 steps: user input → context assembly → API call → response parsing → permission checks → tool execution → result feedback → context management. It exposes 43 built-in tools plus MCP tool integration, and a permission system that gates every tool call.

This is directly useful for anyone building agent harnesses. Understanding the async generator loop and how context is assembled, pruned, and fed back is the difference between a toy agent and a production one.

— [@Siddharth87](https://x.com/Siddharth87/status/2039159870243668243) · [Full article: sidbharath.com](https://sidbharath.com)

### 50B Tokens: Claude Code Agent Teams vs Codex Multi-Agents

After weeks of heavy usage across both systems (~50B tokens spent), the key architectural difference emerges: Codex CLI offers UI smoothness, while Claude Code uses a communication mechanism where subagents send messages to the leader agent. This is a rare large-scale empirical comparison of the two leading multi-agent coding systems — and the subagent-to-leader messaging pattern is a concrete design tradeoff for agent builders.

— [@xanderai](https://x.com/xanderai/status/2027839306296135790)

### Background Agents Will Win

A well-argued thesis: background agents that spin up containers and submit PRs will win over interactive Claude Code sessions, because local single-branch development is too limiting. Cursor and Codex already handle the majority of tasks by spinning up containers that submit PRs. This articulates the shift from interactive pair-programming to autonomous containerized agents — a key workflow trend.

— [@johnlindquist](https://x.com/johnlindquist/status/1935714164028084719)

### Claude Architect Certification Blueprint Leaked

The Claude Architect certification exam blueprint reveals 5 domains, with Domain 1 being Agentic Architecture (27%) and Domain 2 being Tool Design & MCP (18%). Together, these dominate nearly half the exam — signaling exactly the competencies Anthropic believes matter most for agent builders.

— [@sharyph\_](https://x.com/sharyph_/status/2037393353478959336)

### Global AGENTS.md: Cross-Agent Consistency

A practical pattern for multi-agent dev environments: a global `AGENTS.md` file symlinked to `~/CLAUDE.md` (Claude Code), `~/AGENTS.md` (Codex, Gemini, Cursor) keeps agent behavior consistent across all coding agents and projects. Simple, but solves a real fragmentation problem as teams mix agent tools.

— [@linuz90](https://x.com/linuz90/status/2021534838466175225)

---

## Trending

### Multi-Agent Orchestration Layer on Claude Code: 32 Specialized Agents

A community-built multi-agent orchestration layer on top of Claude Code provides 5 execution modes, 32 specialized agents, and claims 3-5x faster output. The "3-5x" claim is marketing language, but the core pattern — composing many small specialized agents on top of Claude Code's harness — is the real signal of where the ecosystem is heading.

— [@hasantoxr](https://x.com/hasantoxr/status/2037963932204445836)

### Anthropic Hackathon Winner: Production Claude Code Config

An Anthropic hackathon winner published a complete Claude Code configuration — agents, skills, hooks, commands, rules, MCPs — battle-tested over 10+ months, using PM2 + multi-agent orchestration with 6 new commands. This is a concrete, production-grade reference architecture showing how practitioners compose skills, hooks, and multi-agent orchestration into durable harnesses.

— [@aiwithjainam](https://x.com/aiwithjainam/status/2028436830404944129)

### Paper App: Shared Canvas + MCP

Paper (app) launched Paper Desktop + MCP, positioning Paper as a shared canvas that Cursor, Claude Code, and Codex can read/write to via MCP. A shared document/state layer accessible by any MCP-aware agent represents an emerging pattern for cross-agent shared context.

— [@paper](https://x.com/paper_status/status/2026349288805326878)

---

## Rising Stars

### Multi-Agent Orchestration on 1,000 Research Papers

Real-world lessons from using multi-agent orchestration with Claude Code for deep semantic analysis of ~1,000 research papers. Honest takeaway: it's extremely easy to mess up (and waste loads of compute), but also possible to land the plane when done right. The honesty about failure modes is valuable for builders scaling agent workloads.

— [@sethlazar](https://x.com/sethlazar/status/2006214936603844668)

### Bidirectional MCP: Agents as Both Server and Client

An elegant architectural proposal: multiple Claude Code instances coordinate in real-time by each being simultaneously an MCP server and MCP client, exposing tools like `coordinate_plan` to minimize merge conflicts. This bidirectional MCP pattern could become a standard for multi-agent codebase collaboration.

— [@radjathaher](https://x.com/radjathaher/status/1938462531510800622)

### Open-Source MCP for Multi-Agent Codebase Coordination

An open-sourced MCP server ([flor.io/agent-chat](https://flor.io/agent-chat)) that lets multiple coding agents coordinate when working in the same codebase. A concrete building block for teams running parallel agents on shared repos — the implementation of the coordination pattern described above.

— [@larryflorio](https://x.com/larryflorio/status/2042978393998672067)

---

## Papers — Agent Architecture Deep Dive

### 1. LLM-as-Code: Agentic Programming for Agent Harness

**The most architecturally significant paper this week.** Argues that token explosion, control-flow hallucination, and unreliable completion in LLM agents are not implementation bugs but architectural consequences of assigning control flow (looping, branching, sequencing) to a probabilistic system. Proposes "Agentic Programming": the program governs all control flow, and the LLM ("LLM-as-Code") is invoked only where reasoning or generation is needed.

This directly challenges the dominant agent-framework paradigm where the LLM decides what to do next, when to call tools, and when to stop. A strong, opinionated architectural thesis that aligns with the broader shift toward skills, memory layers, and verification loops over giant prompts.

— [arXiv:2606.15874](http://arxiv.org/abs/2606.15874v1) · Junjia Qi, Zichuan Fu, Jingtong Gao

### 2. ToolPro: Tool Programs as Agent Interface

**A major rethink of the agent-tool interface.** Instead of agents making stepwise API calls, ToolPro represents an agent's tool intent as an executable tool program encoding multi-step service interactions with loops, conditionals, joins, retries, and explicit effect types. Instantiated over MCP-style services with WebAssembly sandboxing.

Combines constraint-guided program construction, effect-aware replay for exactly-once state-modifying calls, and a profile-driven policy deciding when program execution beats stepwise calling. Reduces end-to-end latency by up to 53.4% and client-side traffic by up to 96.1%.

— [arXiv:2606.19992](http://arxiv.org/abs/2606.19992v1) · Mugeng Liu, Shuoqi Li, Yixuan Zhang

### 3. ProvenanceGuard: Source-Aware Factuality for MCP Agents

As MCP becomes the standard tool interface, verifying provenance — not just pooled-grounding — is a genuinely new failure mode. ProvenanceGuard checks whether each atomic claim in an agent's answer is attributed to the correct evidence source, not just supported by pooled evidence. It identifies a novel MCP-specific hallucination class: cross-source conflation.

Consumes captured MCP traces with stable tool/source IDs, decomposes answers into atomic claims, routes each claim to source-specific evidence, and flags cross-source conflation. First targeted verifier for MCP-based agents.

— [arXiv:2606.18037](http://arxiv.org/abs/2606.18037v1) · Ander Alvarez, Santhiya Rajan, Samuel Mugel

### 4. SING: Scalable Active Tool Discovery

Directly solves the scaling problem of agent harnesses facing huge tool inventories. As harness-connected tool ecosystems expand to hundreds or thousands of APIs, SING (Synthetic Intention Graph) replaces exhaustive tool-schema injection with intention-aligned retrieval. Addresses the failure of one-shot retrieval to align tool descriptions with true task intention in long-horizon tasks.

— [arXiv:2606.16591](http://arxiv.org/abs/2606.16591v2) · Qiao Xiao, Haochen Shi, Yisen Gao

### 5. Compositional Skill Routing (SkillWeaver)

Formalizes the Compositional Skill Routing problem: given a complex query and a large skill library, decompose the query into atomic sub-tasks, retrieve the right skill for each, and compose an executable dependency DAG. SkillWeaver combines an LLM task decomposer, a bi-encoder skill retriever with FAISS indexing, and a dependency-aware DAG planner. Ships with CompSkillBench (300 compositional queries over 2,209 real skills).

— [arXiv:2606.18051](http://arxiv.org/abs/2606.18051v1) · Xueping Gao

### 6. Enterprise Multi-Agent Orchestration at Scale

A rare production-scale empirical study. Evaluates DAG Plan-and-Execute vs ReAct across 208 enterprise scenarios at Persona/Department/Enterprise scales (up to 200 agents). Key finding: both architectures degrade sharply at enterprise scale as agent discovery noise dominates — simple tasks degrade more than complex ones. A Task Manager enables continuous operation via priority inference, event merging, and preemption. The orchestration architecture matters less than managing agent discovery noise at scale.

— [arXiv:2606.20058](http://arxiv.org/abs/2606.20058v1) · Harsh Rao Dhanyamraju, Leonidas Raghav, Aaron Lee

### 7. Multi-Agent Transactive Memory (MATM)

Extends RAG from consuming human-authored artifacts to consuming agent-generated artifacts. MATM treats agent trajectories as first-class retrievable artifacts — indexing procedural knowledge so newly instantiated agents can retrieve and reuse solutions instead of repeatedly rediscovering them. This is the agent-OS memory layer made concrete, moving beyond per-agent context to population-level retrieval and cross-agent knowledge transfer.

— [arXiv:2606.19911](http://arxiv.org/abs/2606.19911v1) · To Eun Kim, Xuhong He, Dishank Jain

### 8. Decoupled Search Grounding (DSG)

Solves a real production pain: vendor lock-in on search grounding. Native search grounding bundles retrieval policy, provider, evidence injection, cost, and latency behind one model boundary. DSG externalizes grounding via an MCP-compatible gateway with provider routing, source-aware rendering, configured fallback, retrieval-depth control, and caching. Across 5 frontier models: native search leads on recency-sensitive tasks, but DSG exposes a stronger frontier where control matters.

— [arXiv:2606.18947](http://arxiv.org/abs/2606.18947v1) · Emmanuel Aboah Boateng, Kyle MacDonald, Amardeep Kumar

### 9. OpenClaw-Skill: Collective Skill Tree Search (CSTS)

Automatically constructs a structured, generalizable tree of reusable skills for LLM agents via collective-intelligence-driven iterative search. Uses two iterative phases — Collective Skill Node Generation and skill composition — to search, identify, and compose effective skills into a tree. A direct advance on the "trainable skills" theme: turning ad-hoc skill libraries into searchable, composable structures.

— [arXiv:2606.16774](http://arxiv.org/abs/2606.16774v1) · Tianyi Lin, Chuanyu Sun, Jingyi Zhang

### 10. AutoPass: Evidence-Guided LLM Agents for Compiler Tuning

A multi-agent framework for compiler performance tuning that uses evidence-guided LLM agents to navigate complex microarchitectural effects and noisy runtime measurements. Demonstrates the multi-agent pattern applied to a hard systems optimization problem where noisy measurements make single-agent approaches fragile.

— [arXiv:2606.20373](http://arxiv.org/abs/2606.20373v1) · Zepeng Li, Jie Ren, Zhanyong Tang

---

## Notable Mentions

### Skill & Tool Engineering

- **Automating SKILL.md Generation via Trajectory Mining** — Tests whether skill libraries can be mined from GUI interaction trajectories. Honest negative result: mined clusters are readable (0.95+ purity) but do NOT transfer to downstream policy improvement. Important cautionary data for the skill-training paradigm. [arXiv:2606.20363](http://arxiv.org/abs/2606.20363v1)
- **VISUALSKILL: Multimodal Skills for Computer-Use Agents** — Extends reusable skill libraries beyond text-only artifacts by capturing the visual/multimodal nature of GUI interaction. [arXiv:2606.18448](http://arxiv.org/abs/2606.18448v1)
- **PreAct: Computer-Using Agents that Get Faster on Repeated Tasks** — Caches and replays execution traces so GUI agents avoid re-reasoning every screen. Practical cost-reduction pattern. [arXiv:2606.17929](http://arxiv.org/abs/2606.17929v1)
- **Towards Pareto-Optimal Tool-Integrated Agents** — Trains agents to balance task accuracy against tool-use efficiency via Pareto Ranking Policy Optimization. [arXiv:2606.16111](http://arxiv.org/abs/2606.16111v1)
- **Is Code Better Than Language for Algorithmic Reasoning** — Clean experimental design separating reasoning representation from execution mechanism. [arXiv:2606.15589](http://arxiv.org/abs/2606.15589v1)

### MCP Security & Reliability

- **Breaking the Protocol** — First formal security analysis of MCP's architectural design. Cited by CISA/US DoD cybersecurity advisory (June 2026). Defines the MCP threat model. [Semantic Scholar](https://www.semanticscholar.org/paper/a4acc9e39473f642ab9cf1f05201effe95600fba) · 10 citations
- **MCP-38: Comprehensive Threat Taxonomy** — 38 protocol-specific threat classes for MCP systems. Use as a security review checklist. [Semantic Scholar](https://www.semanticscholar.org/paper/cf41950de467bd8843e1961c6f0abf673ec0c938) · 4 citations
- **SMCP: Secure Model Context Protocol** — Adds authentication, authorization, and integrity guarantees to MCP. A constructive security proposal for hardened deployments. [Semantic Scholar](https://www.semanticscholar.org/paper/20627abfd2d5c40b44943308416639776437422c)
- **MCP Tool Descriptions Are Smelly** — Quantifies how ambiguous/incomplete natural-language tool descriptions degrade agent routing, with augmentation fixes. [Semantic Scholar](https://www.semanticscholar.org/paper/1261cfc97ceaa092b1eb7669e68e292630c3baad) · 6 citations
- **Real Faults in MCP Software: Taxonomy** — Mines bug reports and code from real MCP implementations to categorize recurring failure modes. [Semantic Scholar](https://www.semanticscholar.org/paper/b0decd1cf5c8ee88689a250af09b1c5a7e8f33de) · 3 citations
- **Enhancing MCP with Context-Aware Server Collaboration** — Coordinates multiple MCP servers with shared context, reducing redundant calls. [Semantic Scholar](https://www.semanticscholar.org/paper/b149f8ce792cde4b21d6e938097382e0767ea9e1)
- **SafeClawBench** — Separates tool-using agent harm into semantic, audit-evidence, and sandbox stages rather than collapsing into a single metric. [arXiv:2606.18356](http://arxiv.org/abs/2606.18356v1)

### Agent Memory

- **MemoryArena** — Benchmark for interdependent multi-session tasks where agents must distill experiences into memory and reuse it for later actions. 32 citations — highest-impact memory benchmark in this batch. [Project page](https://memoryarena.github.io)
- **MemFactory** — First framework unifying training, evaluation, and deployment of agent memory via RL. LLaMA-Factory-style approach for memory operations. [Semantic Scholar](https://www.semanticscholar.org/paper/4b6446f8c99cee6fff6bb5c2cadc7b67ee00f6c5)
- **StreamMemBench** — Streaming evaluation testing whether agents carry cues forward for future-oriented assistance, not just recall. [Semantic Scholar](https://www.semanticscholar.org/paper/5fed612263b97732175cab8118974a9835256d43)
- **STITCH (Grounding Agent Memory in Contextual Intent)** — Intent-conditioned retrieval that grounds recalled evidence in the user's current latent intent. [Semantic Scholar](https://www.semanticscholar.org/paper/9211f5e2e3c9bddd21a3fde10b946b9638352c4b)
- **Trojan Hippo** — Persistent agent-memory attacks that exfiltrate data by weaponizing stored memory, under a realistic threat model. [Semantic Scholar](https://www.semanticscholar.org/paper/6e4fc3a013a070a234a2ae9309e504483eb5f1ed)
- **When Stored Evidence Stops Being Usable** — Scale-conditioned evaluation showing memory rot: as irrelevant sessions accumulate, stored evidence becomes unusable. [Semantic Scholar](https://www.semanticscholar.org/paper/6ef88fd26512131f39ed7fc70f8f3786e74b9b98)
- **MemAdapter** — Fast alignment across different agent memory paradigms (explicit, parametric, latent) via generative subgraph retrieval. [Semantic Scholar](https://www.semanticscholar.org/paper/c85d6701264159e092c39683e10ebe71ec38b1fd)
- **SuperLocalMemory** — Local-first memory system with Bayesian trust defense against memory poisoning. [Semantic Scholar](https://www.semanticscholar.org/paper/458bf9d2719985a1f21923a0d13811a558e9ebce) · 5 citations
- **MemEye** — Visual-centric evaluation for multimodal agent memory, testing whether agents preserve visual evidence (not just captions). [Semantic Scholar](https://www.semanticscholar.org/paper/e5766ec08844810e4772beb40fffd7c4cc3576e9)
- **BMAM: Brain-inspired Multi-Agent Memory** — Addresses "soul erosion" — loss of temporal grounding in long-running agents. [Semantic Scholar](https://www.semanticscholar.org/paper/19811be63ef792e11a37de03570231405aeff8c1)

### Multi-Agent Systems & Evaluation

- **Contagion Networks** — Formal framework for measuring how evaluator biases propagate through interacting LLM agents in multi-agent systems. [arXiv:2606.20493](http://arxiv.org/abs/2606.20493v1)
- **Reliability of Networks of AI Agents** — Applies density evolution and stopping-set analysis from coding theory to reason about agent ensemble reliability. [arXiv:2606.18121](http://arxiv.org/abs/2606.18121v1)
- **Beyond Static Leaderboards** — Aggregates 14 parallel implementation studies of an MCP-based industrial agent benchmark. Important meta-evaluation. [arXiv:2606.19704](http://arxiv.org/abs/2606.19704v1)
- **How Inference Compute Shapes Frontier LLM Evaluation** — Argues eval is now highly sensitive to inference-compute allocation as tasks shift to longer tool-using trajectories. [arXiv:2606.17930](http://arxiv.org/abs/2606.17930v1)
- **Hierarchical Control in Multi-Agent Games** — LLM-planner + RL-executor pattern for agents needing both high-level reasoning and fast low-level control. [arXiv:2606.20014](http://arxiv.org/abs/2606.20014v1)
- **AgentFinVQA** — Deployable multi-agent pipeline for financial chart QA with auditable reasoning traces. Patterns transferable to any regulated use case. [arXiv:2606.19782](http://arxiv.org/abs/2606.19782v1)
- **MetaResearcher** — Scales deep research agents via self-reflective RL in adversarial virtual environments. [arXiv:2606.19893](http://arxiv.org/abs/2606.19893v1)

### Governance & Reasoning

- **Architectural Wisdom** — Governance framework giving AI systems an architectural mechanism to question whether an objective should be optimized at all. [arXiv:2606.16319](http://arxiv.org/abs/2606.16319v1)
- **LLMs Do Not Always Need Readable Language** — Investigates encoding semantic information in compact, non-human-readable forms for model-to-model communication. [arXiv:2606.19857](http://arxiv.org/abs/2606.19857v1)
- **Explicit Knowledge Conflict Resolution** — Detects and arbitrates when parametric and contextual knowledge disagree in LLM inference. [arXiv:2606.20245](http://arxiv.org/abs/2606.20245v1)

---

## Chinese Community — 知乎精选

- **2026 年 AI Agent 技术全景：12 大主流框架深度解析** — Modern AI Agent standard architecture: perception → decision → action → memory complete loop. LangGraph as production standard. [知乎](https://zhuanlan.zhihu.com/p/2026254728342905724)
- **2026年 Agentic AI 十大关键趋势** — Agent system architecture evolving from monolithic to distributed agent networks. IBM predicts Agent control planes and multi-agent dashboards. [知乎](https://zhuanlan.zhihu.com/p/1991451643544355292)
- **当数据消费者变成 Agent** — Data infrastructure stack: consumer (human/BI/assistant/Agent) → agent access layer (MCP universal connection + ADP governed execution) → semantic layer → metadata → execution engine. [知乎](https://zhuanlan.zhihu.com/p/2042181510762057971)
- **企业级 AI Agent 选型指南：避开这五个坑** — Enterprise Agent three sins: overconfident, unrealistic, expensive and useless. System integration capability is the core bottleneck. [知乎](https://zhuanlan.zhihu.com/p/2017558472355496387)
- **Hermes 智能体全面研究报告** — Hermes Agent by Nous Research: open-source autonomous AI agent designed to grow alongside you. [知乎](https://zhuanlan.zhihu.com/p/2026622473097978502)

---

*Collected from arXiv, Semantic Scholar, X/Twitter, and Zhihu. Filtered for agent architecture, MCP, multi-agent systems, skill engineering, and agent memory relevance.*
