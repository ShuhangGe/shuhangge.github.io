---
title: "Agent Architecture Daily Digest — June 3, 2026"
description: "Perplexity's Search-as-Code paradigm, State-Externalizing Harnesses, Skill Evolution as Meta-Skill, OpenAI's harness-vs-model thesis, and 7 new skill-learning papers reshape agent architecture."
pubDate: "2026-06-03"
lang: en
tags: ["Agent Architecture", "LLM", "Skill Learning", "Codex", "Daily Digest"]
---

## TL;DR — Top Stories

1. **Perplexity's Search as Code** replaces function-calling with agent-written Python pipelines — a paradigm shift from declarative tool calls to imperative orchestration ([@yibie](https://x.com/yibie/status/2061633153325015067))
2. **State-Externalizing Harnesses (Harness-1)** splits policy from environment state — a 20B model trained this way outperforms full-transcript RL agents ([@dair_ai](https://x.com/dair_ai/status/2061825437693841651))
3. **OpenAI's Yann Dubois**: "If we froze current models and invested fully in harnesses, people would feel AGI in every domain already." Continuous learning remains the unsolved frontier ([@servasyy_ai](https://x.com/servasyy_ai/status/2061678793170059596))
4. **SkillEvolver**: skills as meta-skills — agents that learn to improve their own skills from execution feedback, without model retraining (Zhang et al., May 2026)
5. **EvoAgent**: hierarchical sub-agent delegation + evolutionary skill metadata — the most complete agent architecture paper this month (Zhang et al., April 2026)
6. **FastClaw**: cloud-native agent runtime using compute-storage separation — 1/40 the code, 1/7 the resources, sub-second cold start vs 15s for OpenClaw ([@idoubicc](https://x.com/idoubicc/status/2061405448356741364))
7. **MiniMax M3**: first open-weights model hitting 59% SWE-Bench Pro, 66% Terminal Bench 2.1, with 1M-context sparse attention ([@MiniMax_AI](https://x.com/MiniMax_AI/status/2061266317815296322))
8. **MetaSkill (OpenSquilla)**: self-organizing skill protocol — describe a goal in plain words, it discovers, composes, and can write new skills ([@OpenSquilla](https://x.com/OpenSquilla/status/2061481500974157956))
9. **skillGenBench**: first benchmark for evaluating whether LLMs can automatically generate reusable SKILL.md files — foundational for agent skill engineering ([@sanbuphy](https://x.com/sanbuphy/status/2061629549516083222))
10. **Pi Agent**: open-source minimal coding agent harness — "core stays small, complexity goes into extensions" ([@teach_fireworks](https://x.com/teach_fireworks/status/2061804587833786629))

Today's Numbers: **25 detailed items across 4 categories | 7 papers | 6 notable mentions | 200 raw tweets filtered to 100 candidates**

---

## Company Updates

### Perplexity's Search as Code: The End of Function Calling?
[@yibie](https://x.com/yibie/status/2061633153325015067) · 115 likes · 26 retweets

Perplexity just published a new architecture called **Search as Code (SaC)**. The core insight: instead of an AI agent calling search APIs one by one through function calling (one LLM inference roundtrip per search operation), the agent directly writes Python code to orchestrate the entire search pipeline. 

In traditional architectures, each search requires a full LLM inference roundtrip — fine for single queries, but a bottleneck when agents need hundreds of searches for complex tasks. SaC collapses that into a single code-generation step followed by code execution. This isn't just a performance optimization — it's a paradigm shift from declarative function calling to imperative code-based orchestration, with massive implications for agent latency and cost.

### OpenAI Voice Hack Night: Voice Agents Hit Mobile OS
[@OpenAIDevs](https://x.com/OpenAIDevs/status/2061558243911155722) · 391 likes

The winner was **Agentic OS for a Phone** — a voice-first mobile OS where users talk and agents answer and take action across the phone. Wagner (multi-agent virtual meeting room for infrastructure planning) and two other finalists rounded out the field. Voice as a first-class agent modality is arriving faster than expected — mobile OS integration means always-on, ambient agent interaction that goes far beyond chatbot UX.

### Codex in the Wild: 5-Person Team Running Full Product on Codex
[@OpenAIDevs](https://x.com/OpenAIDevs/status/2061610165783380052) · 584 likes

Proaction, a 5-person fleet management startup, is using Codex across their full workflow — sales demos, support follow-ups, marketing assets, and engineering. This goes beyond the solo-developer CLI pattern: entire product workflows are now agent-driven, with small teams achieving what previously required much larger organizations.

### Anthropic Expands Project Glasswing
[@AnthropicAI](https://x.com/AnthropicAI/status/2061796327986454883) · 2,172 likes

Claude Mythos Preview is now available to ~150 additional organizations across 15+ countries. Project Glasswing remains Anthropic's most mysterious advanced program — the expansion signals growing confidence in its readiness and provides clues about how frontier AI access control is being structured at scale.

---

## Industry Leaders & Architecture

### The Most Important Paper You Haven't Heard: State-Externalizing Harnesses
[@dair_ai](https://x.com/dair_ai/status/2061825437693841651) · 62 likes

**Harness-1** introduces a genuinely novel agent training paradigm: split the policy (what the model learns) from the harness (what the environment manages). The key insight: if state can be reliably maintained by the environment, it doesn't belong inside the model. Move it to the harness.

A 20B model trained with state-externalized harnesses outperforms and generalizes further than one trained on the full interactive transcript. The RL process no longer needs to learn semantic search and routine bookkeeping simultaneously — the harness handles the mundane state management, freeing the policy to focus on reasoning. This has major implications for agent training efficiency, especially as context windows grow.

### Yann Dubois (OpenAI Post-Training Lead): The Harness is Ready, the Model Isn't
[@servasyy_ai](https://x.com/servasyy_ai/status/2061678793170059596) · 1 like (criminally underrated)

Yann Dubois, co-lead of OpenAI's post-training frontier team, shared five critical insights on the MAD Podcast:

1. **AI progress is continuous, perception is discontinuous.** Reliability crossed a threshold around December 2024 — tools suddenly became "usable." Before that, the same incremental progress was invisible.
2. **Three drivers**: reliability hitting the threshold, model self-acceleration (AI writing AI), and RL expanding from verified rewards to real-world use cases.
3. **Generic harnesses won't last.** Domain-specific, short-lived harnesses are the way to go. Be ready to constantly rebuild them.
4. **"If we froze current models and invested fully in harnesses, people would feel AGI in every domain already."** This is the strongest statement yet that the bottleneck is engineering, not model capability.
5. **Continuous learning is unsolved.** Models have flat learning curves — they're day-zero experts who never improve. Humans monotonically improve. This gap is AI's biggest remaining weakness.

### FastClaw: The "Serverless for Agents" Architecture
[@idoubicc](https://x.com/idoubicc/status/2061405448356741364) · 322 likes

A hosting provider shared their migration story: 500 agent Pods on Kubernetes, 18 servers, $5K/month cost, <$8K MRR. After migrating to **FastClaw** — a cloud-native agent runtime with compute-storage separation — they collapsed to 3 servers, 1/6 the cost.

FastClaw's architecture is fundamentally different: agents are not stateful long-lived processes. They're ephemeral. When a request arrives, a sandbox is dynamically mounted to serve it, then torn down. Code volume: 1/40 of OpenClaw. Resource usage: 1/7. Cold start: sub-second vs 15s. Single binary, no environment dependencies.

This mirrors what serverless did to traditional application servers — and it may become the default pattern for multi-tenant agent hosting.

### MiniMax M3: Open-Weights Finally Competitive on Agent Benchmarks
[@MiniMax_AI](https://x.com/MiniMax_AI/status/2061266317815296322) · 8,174 likes

MiniMax M3 claims to be the first open-weights model combining three frontier capabilities: **59.0% SWE-Bench Pro**, **66.0% Terminal Bench 2.1**, **34.8% SWE-fficiency**, with sparse attention scaling context to 1M tokens and native multimodal from training start. An open-weights model at these numbers fundamentally changes the cost equation for self-hosted coding agents. Weights and tech report expected in ~10 days.

### JetBrains Mellum2-12B: 2.5B-Active MoE With Tool Calling
[@vllm_project](https://x.com/vllm_project/status/2061621691995005301) · 322 likes

A 12B MoE activating only 2.5B parameters, with reasoning parser and tool calling for agentic workflows, running natively in vLLM from day 0. 128K context window. This is the kind of model that makes "your agent runs locally on the laptop" increasingly viable — the active parameter count is small enough for consumer hardware while maintaining enough capability for agentic tasks.

### Hermes Agent: Streaming UX Matters
[@Teknium](https://x.com/Teknium/status/2061730212799525205) · 1,286 likes

Smooth streaming token delivery on Telegram for Hermes Agent. Streaming UX is a critically under-discussed aspect of agent architecture — latency perception directly impacts user trust and adoption. A 200ms token delivery delay makes an agent feel "slow" even if it's reasoning faster than a human.

---

## From the Papers: Skill Learning Renaissance

This week saw an unusual concentration of papers on **agent skill learning** — a signal that the research community has identified skill acquisition and evolution as the next frontier in agent architecture.

### SkillEvolver: Skill Learning as a Meta-Skill
Zhang, Zhu, Zhou et al. · May 2026 · [Paper](https://www.semanticscholar.org/paper/d54fb47cb1bc0643d6126e96506537f9297737fc)

The most novel concept in this batch: **skills that learn.** Most agent skills today are static — authored once, consumed unchanged. SkillEvolver proposes a lightweight plug-and-play solution where skills self-improve from execution feedback without model retraining. The ability to learn skills is itself treated as a meta-skill. If this works at scale, it closes the loop between agent deployment and capability growth.

### EvoAgent: Hierarchical Delegation + Evolutionary Skills
Zhang, Guo, Jia et al. · April 2026 · [Paper](https://www.semanticscholar.org/paper/b1e1398ec1f6edaa64ed5c56e1cf8fbfc804cf1a)

The most architecturally complete paper in this batch: skills are modeled as **multi-file structured capability units** with triggering mechanisms and evolutionary metadata. A parent agent delegates tasks to sub-agents, each equipped with evolving skills. This directly mirrors the agent-delegation patterns used in production systems (Codex, Hermes, Claude Code) but formalizes the delegation hierarchy with evolutionary tracking.

### Skill-R1: RL for Skill Evolution (Without Retraining)
Vishe, Surana, Jiang et al. · May 2026 · [Paper](https://www.semanticscholar.org/paper/7cb626df241b6f8f4d69e65e7013d05bd5987d46)

Skills are treated as first-class RL-optimizable objects. Rather than improving skills through prompt engineering or model fine-tuning (costly and model-specific), Skill-R1 applies reinforcement learning rewards directly to skill procedures. This decouples skill improvement from model improvement — you can evolve skills without touching the underlying LLM.

### SkillLearnBench: Can Your Agent Actually Learn Skills?
Zhong, Lu, Ning et al. · April 2026 · [Paper](https://www.semanticscholar.org/paper/f73d5cb01fb23264ba211fb6305586ed7b31d61c)

The first benchmark specifically for **continual skill learning** — does an agent's skill system actually work over time, or does it suffer catastrophic forgetting? Evaluates skill acquisition, retention, and transfer across real-world tasks. If skills are the future of agent extensibility, we need to measure how well they work — this provides the measuring stick.

### ARISE: Hierarchical RL With Intrinsic Skill Evolution
Li, Miao, Qi et al. · March 2026 · [Paper](https://www.semanticscholar.org/paper/5ed5c5334488838e7e1267c8f35b7b4926eebde0) · 10 citations

Agents evolve reusable skills during mathematical reasoning training instead of treating each problem in isolation. Skills accumulate and transfer across problem instances — the RL equivalent of learning transferable subroutines.

### WebXSkill: Bridging the Grounding Gap for Web Agents
Wang, Wu, Zhang et al. · April 2026 · [Paper](https://www.semanticscholar.org/paper/f283b199100a8d976d1f3b6a8561f7597c6a068e) · 5 citations

Skills for autonomous web agents face a "grounding gap": textual workflow skills provide natural language guidance but lack the DOM-level grounding needed for reliable browser automation. WebXSkill directly addresses this bottleneck.

### From History to State: Privacy-Preserving Skill Compression
Xie, Wang, Wang et al. · May 2026 · [Paper](https://www.semanticscholar.org/paper/a676d0f60a6d68da5b919c7dbbb0534bd395f13e)

Compresses long agent interaction histories into constant-context skill representations. Solves the privacy-capability tension for personal agents: cloud models are powerful but expose sensitive intermediate context. Skill distillation from history to compact state enables privacy-preserving execution without quality loss.

---

## Trending

### Agentic Spatial Intelligence at CVPR 2026
[@ChenSiyich](https://x.com/ChenSiyich/status/2061675029964992546) · 29 likes · NVIDIA/UMich

CVPR 2026 research on VLMs that adaptively use vision tools and treat robots as tools for spatial reasoning and real-robot manipulation. The "robot as tool" abstraction is architecturally significant — it applies the agentic tool-use pattern to physical systems, unifying virtual and physical tool use under one framework. Poster: ExHall A 87, Sun Jun 7, 2:30 PM PDT.

### Investment Research Agent: Distilling Expertise Into Pipelines
[@kaikaibtc](https://x.com/kaikaibtc/status/2061393702258667968) · 2,657 likes

A detailed breakdown of distilling two professional investors' methodologies into an autonomous research agent. The architecture is domain-specific: pipeline design, BOM-level supply chain analysis, human-in-the-loop decision-making. "AI handles the grunt work, humans make the final call" — a practical agent architecture pattern that prioritizes augmentation over replacement.

---

## Rising Stars

### Pi Agent: The Anti-Monolith Coding Agent
[@teach_fireworks](https://x.com/teach_fireworks/status/2061804587833786629) · 1 like

Pi is an open-source minimal terminal coding agent. The philosophy is explicit: core stays small, all complexity goes into extensions, skills, prompts, and packages. Multi-model support is built-in. At 1 like, this is almost certainly an undiscovered project — but the architecture ethos (modular, hackable, multi-model) is exactly what the community is gravitating toward.

### Coding Agent Showdown: Codex App > Cursor > Claude Desktop
[@dotey](https://x.com/dotey/status/2061569125948760319) · 47 likes · 96 replies

A detailed first-hand comparison: **Cursor's multitask mode** enables parallel background tasks (Codex and Claude Code don't match this). **Plan mode + multitask** is a stable combination. But Cursor still lacks /goal, mobile support, and Chrome/Computer use debugging that Codex has. The takeaway: "planning + parallel execution" is emerging as the winning architecture pattern across all three platforms.

### MetaSkill: Self-Organizing Skill Protocol (Open Source)
[@OpenSquilla](https://x.com/OpenSquilla/status/2061481500974157956) · 37 likes

Describe a goal in plain words. MetaSkill discovers relevant skills, picks the best combination, composes them into a workflow, and can even write new skills when needed. Apache 2.0, open source. If this works as described, it's a step beyond static MCP server registries toward dynamic skill synthesis — the holy grail of agent extensibility.

### Grok CLI: X Search Tools for Agents
[@gkxspace](https://x.com/gkxspace/status/2061413377461596526) · 347 likes

Grok CLI added 4 X search tools: **keyword search** with full advanced operators (from:, since:, min_faves:), **semantic search** with date/relevance filtering, **user search**, and **thread fetch** (parent + all replies). All local, agent-callable, backed by realtime Twitter data. This is a platform play — if standard agents get first-class X data access, it changes the information landscape for agent workflows.

### WorkBuddy: China's "MCP-Killer" Skill Ecosystem
[@gengdaJ](https://x.com/gengdaJ/status/2061427044571861476) · 925 likes

The Chinese AI tool WorkBuddy has a skill store spanning Feishu, Google Workspace, Obsidian, QQ Mail, Tencent Docs, WeChat auto-publishing, Xiaohongshu assistant, Twitter content scraping, and even 12306 (train tickets) and ride-hailing. This deep platform integration model diverges from the Western MCP/open-protocol approach — both have strengths, and the Chinese ecosystem's integration depth is worth watching.

---

## Notable Mentions

- **Codex Voice Unblocking** — Codex now uses voice to ask for human help when blocked (e.g., 1Password-gated releases). Agent-initiated interaction is an underexplored UX frontier. [@steipete](https://x.com/steipete/status/2061574752574283858) · 975 likes

- **Hermes Agent Desktop App** — Official desktop release for Mac/Windows/Linux with OpenClaw migration path. Desktop apps lower the agent adoption barrier. [@hisevenih](https://x.com/hisevenih/status/2061755140697411918) · 325 likes

- **CC Switch v3.16.1** — Use DeepSeek or other third-party models with Codex while retaining official features (mobile remote, plugins). [@blueskylh1](https://x.com/blueskylh1/status/2061488980353536457) · 247 likes

- **Cursor Composer 2.5 Reverse Proxy** — Cursor's proprietary model made available to arbitrary agents via reverse proxy. Shows demand for model-agnostic runtimes. [@dingyi](https://x.com/dingyi/status/2061735326348169530) · 159 likes

- **longflow: Visual Workflow for Codex** — Installable visual workflow tool gives agents structured multi-step visual workflows. [@aronhouyu](https://x.com/aronhouyu/status/2061430368759353560) · 315 likes

- **Voice Hack Night Finalists** — Wagner (multi-agent infrastructure planning), Surgical Triage, and Curo show the breadth of voice+agent applications. [@OpenAIDevs](https://x.com/OpenAIDevs/status/2061558256821305700) · 54 likes
