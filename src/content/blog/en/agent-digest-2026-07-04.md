---
title: "Agent Architecture Daily Digest — July 4, 2026"
description: "The competition has shifted from model to harness. Hugging Face's Meta-Harness matches Sonnet 4.6 by rewriting only the code around a frozen model; Maka harness engineering pushes DeepSeek Flash to GLM-5.2 territory for $0.55; Browser Use CLI 3.0 goes direct-CDP with self-evolving per-site skills; and ByteDance's EdgeBench reveals a log-sigmoid scaling law for long-horizon agent learning. Plus Superpowers vs Grill with Docs, ContextNest for verifiable agent memory, CMU's new Agents course, and the Fable 5 ban's lasting market share redistribution."
pubDate: "2026-07-04"
lang: en
tags: ["Agent", "LLM", "AI Architecture", "Daily Digest"]
---

## TL;DR — Today's Overview

> Top 10 things to know today:

1. **Don't train the model, evolve the harness.** Hugging Face took a frozen open model scoring 0% on a hard legal agent benchmark, left its weights alone, and let an automated loop rewrite only the runtime code (context, tool calls, termination logic). The result matched Sonnet 4.6 on the headline metric. The harness — not the weights — is the new competitive frontier. — [@akshay_pachaar](https://x.com/akshay_pachaar/status/2072961737008336937)

2. **Maka harness engineering pushes DeepSeek Flash to GLM-5.2 territory for $0.55.** The same DeepSeek Flash V4, with no model change, scored 0.8 (effectively 0.9) on terminal-bench sample — 10 coding agent tasks for 4 RMB total, with a 97.5% cache-hit rate. Harness design and cache optimization are the practical levers for closing the mid-tier-to-frontier gap. — [@jakevin7](https://x.com/jakevin7/status/2072923081463763342)

3. **ByteDance's EdgeBench reveals a log-sigmoid scaling law for long-horizon agents.** Across 134 real-world tasks running 12–72 hours, agent performance follows a log-sigmoid function of environment interaction time (R²=0.998). Learning speed doubles every 3 months, driven by accumulating and reusing task experience — not repeated sampling. — [@tikgiau](https://x.com/tikgiau/status/2072701593829695926)

4. **Browser Use CLI 3.0: direct CDP, self-evolving domain skills, auto-healing.** Installable as a skill in Claude Code/Codex, the browser agent bypasses DOM-in-context entirely — it reads Chrome's DevTools Protocol directly, persists per-site login flows/selectors as reusable domain-skills, and writes missing functions on-the-fly. 6× smaller than prior framework, far fewer tokens. — [@xiaohu](https://x.com/xiaohu/status/2072987979979837620)

5. **Superpowers vs Grill with Docs: two agent orchestration paradigms, two eras.** Superpowers outputs detailed execution plans (assumes agents can't do long-horizon tasks); Grill with Docs is a 5-line goal definition (assumes agents have strong goal-execution). They map to different assumptions about agent maturity — a genuinely useful framework for choosing your orchestration strategy. — [@kasong2048](https://x.com/kasong2048/status/2072611852773920956)

6. **Cursor, Claude Code, Codex, Antigravity, OpenHands are converging on harness design.** Their release notes now collide — /loop skills, shared canvases, remote control, multi-workspace. The competition has moved from model intelligence to harness design. Three layers of agent autonomy: goal-driven self-running loops, scheduled/triggered execution, multi-step task planning. — [@grapeot](https://x.com/grapeot/status/2073076048342995050)

7. **The Fable 5 ban's lasting redistribution: Anthropic's OpenRouter share dropped 20.7%→17.6%, GLM-5.2 rose 0→6.7% in two weeks.** A rigorous quantitative analysis of 446 models' daily token traffic shows the market is growing — but Anthropic is the only major provider not participating in that growth. — [@grapeot](https://x.com/grapeot/status/2072804509047488945)

8. **CMU launches a new AI Agents course (Fall 2026) covering scaffold creation, eval building, and RL training.** From Graham Neubig (OpenHands co-founder). Signals the academic formalization of agent engineering as a discipline. — [@gneubig](https://x.com/gneubig/status/2072730570304430183)

9. **Codex delegation without comprehension degrades team capability.** A team that adopted Codex months ago found developers stopped understanding the code — "Codex says it works, so it works." The fix: always go back and ask *why*, then form your own understanding. 93 replies of strong community resonance. — [@xiaogaifun](https://x.com/xiaogaifun/status/2072656279068463606)

10. **RAG + vector databases are a dead end; the future is agent-driven search.** An opinionated but architecturally sound take: use proper memory, chunking + indexing + summarization, agent-provided search tools, and fast SRAM inference providers (Groq/Cerebras). "Any one of these is 10,000× better than naive chunking into a vector database." — [@lidangzzz](https://x.com/lidangzzz/status/2073028193783615527)

📊 Today's Numbers: **24 detailed items | 10 papers | 37 notable mentions | 131 candidates analyzed | 8 chunks processed**

---

## The Pattern: The Harness Is the Model Now

Three independent signals landed on the same conclusion this cycle: **when model intelligence converges, the code layer around the model becomes the primary lever for agent performance.**

**Signal one: Hugging Face's Meta-Harness.** The most architecturally significant post of the day came from [@akshay_pachaar](https://x.com/akshay_pachaar/status/2072961737008336937), summarizing Hugging Face's work (arXiv 2603.28052). They took a frozen open model scoring 0% on a hard legal agent benchmark and let an automated loop rewrite only the harness — the runtime wrapper that feeds context, runs tool calls, and decides when a run ends. By the time the loop finished, the system matched Sonnet 4.6. The harness is the new competitive frontier when model intelligence converges.

**Signal two: Maka + DeepSeek Flash.** [@jakevin7](https://x.com/jakevin7/status/2072923081463763342) (OpenCLI/Maka builder) reported that harness engineering — not a model upgrade — pushed DeepSeek Flash V4 to 0.8 on terminal-bench sample (effectively 0.9, with one task correct but uncounted due to "artifact contamination"). The numbers: 60M total tokens, 58.5M cache hits (97.5%), total cost ~4 RMB (~$0.55 USD). Already approaching GLM-5.2's evaluation level. The improvement is entirely from harness design and cache optimization.

**Signal three: the tooling convergence.** [@grapeot](https://x.com/grapeot/status/2073076048342995050) mapped how Cursor (/loop skill, shared canvas, iOS app, Design Mode), Claude Code (/goal, /loop, remote control), Codex (agent loop, long-horizon tasks, multi-workspace), Antigravity, and OpenHands are all shipping overlapping features. The surface looks like feature convergence; the deeper truth is that competition moved from model capability to harness design. The three layers of agent autonomy he identifies — goal-driven self-running loops, scheduled/triggered execution, multi-step task planning — give builders a vocabulary for what "autonomous" actually means.

Put together, the message is sharp: **the code layer around the model is optimizable separately from the weights.** Start investing in harness engineering before you invest in a bigger model.

---

## X/Twitter Highlights

### Company Updates

[**@xiaohu**](https://x.com/xiaohu/status/2072987979979837620) (472 likes, 43K views) detailed **Browser Use CLI 3.0** — a major architectural upgrade for browser agents. Key features: (1) **Direct CDP control** — the model operates Chrome's DevTools Protocol directly instead of high-level `click()`/`type()` wrappers, eliminating the need to dump DOM trees into context. (2) **Self-evolving domain-skills** — per-site login flows, selectors, and edge cases persist as reusable knowledge; the agent gets better on repeated sites. (3) **Auto-healing** — when it encounters an operation without a function (e.g., file upload), the agent writes that function on-the-fly and continues. (4) Supports real Chrome with existing tabs/cookies/plugins, Browser Use cloud, or any CDP endpoint. The framework is 6× smaller than its predecessor with dramatically lower token consumption.

### Industry Leaders

[**@akshay_pachaar**](https://x.com/akshay_pachaar/status/2072961737008336937) (755 likes, 123K views) delivered the cycle's most-referenced post on **Hugging Face's Meta-Harness** approach: freeze the model weights, let an automated loop rewrite only the harness code (context, tool calls, run termination logic). The outer-loop system uses an agentic proposer with filesystem access to prior candidate scores and execution traces. By loop completion, the system matched Sonnet 4.6 on the benchmark's headline metric. Related work "Life-Harness" (2605.22166) proposes lifecycle-aware runtime harness improvement for frozen LLM agents. The core insight: the code layer around the model is optimizable separately from weights — and that's where competitive advantage now lives.

[**@jakevin7**](https://x.com/jakevin7/status/2072923081463763342) (439 likes, 37K views), the OpenCLI/Maka builder, provided concrete proof that **harness engineering closes the gap between mid-tier and frontier models at trivial cost.** Maka + DeepSeek Flash V4 scored 0.8 on terminal-bench sample (10 coding tasks from the 84-task full set). Key metrics: 60M total tokens, 58.5M cache hits (97.5% cache-hit rate), total cost ~4 RMB. "Is this because DeepSeek Flash got stronger? No — same DeepSeek Flash V4. It's the harness engineering." Already approaching GLM-5.2's evaluation level.

[**@gneubig**](https://x.com/gneubig/status/2072730570304430183) (1,575 likes, 86K views) — Graham Neubig, OpenHands co-founder and CMU professor — announced **CMU's new AI Agents course** launching Fall 2026. Curriculum: scaffold creation, eval building, and training agentic LLMs with RL. Signals the academic formalization of agent engineering as a discipline — scaffolds, evals, and RL training are the core curriculum.

[**@tikgiau**](https://x.com/tikgiau/status/2072701593829695926) (719 likes, 205K views) introduced **EdgeBench** from ByteDance Seed — a benchmark of 134 real-world executable tasks across 6 categories (scientific problems, professional knowledge work, software engineering, optimization, formal math, games) where agents operate continuously for 12–72 hours with multi-level realistic feedback. The key finding: **performance follows a log-sigmoid function of environment interaction time with R²=0.998.** Learning speed doubles every 3 months, and the improvement is driven by accumulating and reusing task experience — not repeated sampling. This changes how we think about agent training and deployment: environment interaction time is a first-class variable.

[**@yibie**](https://x.com/yibie/status/2072965594484543525) (345 likes, 42K views) recommended the **Superpowers 6** blog — the most complete practical report of using Fable 5 for autonomous R&D. 25 experiments, $165 total cost, 50% faster builds, 60% token reduction. The most valuable part isn't the result numbers — it's the complete experiment log documenting every failure, every dead idea, and three corrected measurement bugs. The subagent-driven development optimization shows real cost/performance improvements in agent orchestration.

[**@xiaogaifun**](https://x.com/xiaogaifun/status/2072656279068463606) (377 likes, 93 replies, 57K views) shared a cautionary tale from a team that used Codex for months: **after delegating everything to AI, developers stopped understanding the code.** The pitfall is treating agents as black boxes. "If we just keep giving instructions and accepting suggestions without understanding or judgment, those outputs don't truly count as our own capability — because anyone could click the same buttons." The fix: after AI completes a task, go back and ask *why*, then form your own experience and understanding. 93 replies indicate strong community resonance.

[**@michaelyli_**](https://x.com/michaelyli_/status/2072737069894689007) (244 likes, 45K views) introduced **QuasiMoTTo** — a method for scaling parallel inference sampling with correlated samples instead of independent ones. Result: same performance with 25–47% fewer samples in test-time scaling and 50% fewer RL training steps. Directly relevant to agent efficiency — correlated sampling reduces compute waste when agents run parallel attempts. Practical for any builder managing inference costs.

[**@lidangzzz**](https://x.com/lidangzzz/status/2073028193783615527) (336 likes, 53K views) made a strong, opinionated claim about agent memory architecture: **RAG + vector databases are a dead end.** The alternative: (1) proper memory, (2) chunking + indexing + summarization, (3) agent-provided search tools / multi-agent fuzzy search, (4) fast SRAM inference providers (Groq, Cerebras). No benchmarks cited, but the architectural reasoning is sound and widely discussed in the agent builder community.

[**@xiaohu**](https://x.com/xiaohu/status/2072882871535304974) (94 likes, 36K views) reported **Claude Code's new Artifacts feature** for Pro/Max users — turning coding sessions into shareable, interactive, real-time-updating web pages with version history and gallery management. The page auto-updates in place when changes are published, with version history for rollback. A meaningful UX shift: session state becomes a first-class shareable artifact.

[**@kasong2048**](https://x.com/kasong2048/status/2072611852773920956) (667 likes, 128 replies, 74K views) offered a genuinely insightful framework for **choosing agent orchestration strategy.** Superpowers outputs detailed execution plans — it assumes agents can't do long-horizon tasks, so the plan serves as compacted execution anchors. Grill with Docs uses a 5-line goal definition (goal → TDD → constraints → acceptance criteria → delivery standard) — it assumes agents already have strong goal-execution capabilities. These aren't competing tools; they're best practices for different stages of agent maturity. Essential reading for anyone choosing their orchestration strategy.

[**@mgoin_**](https://x.com/mgoin_/status/2072785822231728363) (192 likes, 16K views) — vLLM maintainer — demonstrated **disaggregated serving + training for online RL** at GB300 NVL72 scale. 9 vLLM nodes serve the full GLM-5.2 FP8 verifier via Mooncake RDMA store to 6 training nodes running FSDP (DP=24). Achieving 125k prefill tok/s and 1.5 steps/s for fully online RL training. A reference design for anyone building large-scale RL training infrastructure.

[**@quantum_soul1**](https://x.com/quantum_soul1/status/2072938968942006613) (234 likes, 23K views) surfaced a Kuaishou tech team article arguing that **new productive forces (AI agents) need matching production relations (org structure).** Department walls limit agent effectiveness at the company level, contrasting with strong individual-level gains. Zuckerberg's note that AI agents haven't significantly accelerated business deployment is likely an org-structure problem, not a tech problem. Reframes the enterprise agent bottleneck from model capability to organizational architecture.

### Trending

[**@Av1dlive**](https://x.com/Av1dlive/status/2072671669278195775) (1,611 likes, 265K views) flagged a 4.8K-star GitHub repo with a complete **"loop engineering" framework for trading agents** — a 12-step pipeline from strategy intent → market data → signals → trading agent → verify → refine → rerun. The verify-and-refine cycle is a practical reference for building self-correcting autonomous agents in any domain, not just trading.

[**@Saccc_c**](https://x.com/Saccc_c/status/2072700980442050667) (371 likes, 37K views) shared three **Codex skills for a professional stock research system**: ai-berkshire (macro framework from Buffett/Munger investment legends), UZI-Skill (deep single-stock analysis from fundamentals/technicals/valuation), QuantDinger (backtestable trading strategies). A practical example of composing Codex skills into a domain-specific agent workflow — adaptable for any vertical.

[**@billtheinvestor**](https://x.com/billtheinvestor/status/2072849002874429736) (227 likes, 92K views) highlighted a 19-year-old who built a **Claude Code cross-market arbitrage bot** scanning 50 markets simultaneously. The real takeaway isn't the returns (which may be cherry-picked) — it's the architecture pattern: converting manual monitoring into continuous automated execution. Claude Code building autonomous trading agents that handle multi-market monitoring and execution loops.

### Rising Stars

[**@grapeot**](https://x.com/grapeot/status/2072871953472573525) (AI builder, Superlinear Academy co-founder) analyzed the **diverging AI pricing models.** Buyers are fleeing token-based pricing (Uber burned annual AI budget in 4 months, Microsoft cancelled Claude Code licenses, Copilot users burned monthly quota in 2 days). Sellers are doubling down (OpenAI -$14B projected, Anthropic $1.25B/mo compute). The gap falls on builders: multi-model routing now diverts 74% of requests to cheaper models.

[**@grapeot**](https://x.com/grapeot/status/2072804509047488945) published rigorous quantitative analysis of **the Fable 5 ban's market impact.** Using OpenRouter API data across 446 models: total traffic is growing, but Anthropic's share dropped 20.7%→17.6% during Fable 5's 18-day ban. GLM-5.2 (released the day after the ban) rose from 0 to 6.7% share in two weeks. "The cake is getting bigger, but Anthropic is the only major provider not participating in the growth." GLM-5.2 is capturing real API market share.

[**@Xudong07452910**](https://x.com/Xudong07452910/status/2072898125027516832) (179 likes, 23K views) flagged a viral Hacker News thread: **Qwen 3.6 27B as the first genuinely general-purpose local model.** Dense parameters, native 256K context, 30 tok/s on M5 Max, 50 tok/s on RTX 5090. Handles creative writing and code simultaneously. If 30B-class models reach general-purpose quality locally, daily dev work and lightweight agent tasks can run without API calls — privacy, latency, and offline capability converge.

[**@snowboat84**](https://x.com/snowboat84/status/2072840491314598244) published an **8-chapter agent architecture overview** — the capstone of a "100 days, 100 long-form essays" series. Covers: what an agent is, tool use/MCP, context engineering/harness, multi-agent planning, memory, long-task endurance, browser/desktop control, production reliability, and monetization. From AutoGPT 2023 to production agents in 2026 — a high-value reference for the full architecture landscape.

[**@RealCodedAlpha**](https://x.com/RealCodedAlpha/status/2073045554654011521) summarized Daisy Holman's **"Beyond Claude Code Basics"** talk: agents need three things to work like real engineers — **Access** (design docs, CI/CD, runbook, monitoring, PR history), **Knowledge** (CLAUDE.md, Skills, doc retrieval), and **Tooling** (Hooks, LSP, lint, test feedback). The practical method: "log what you leave to look up" — every time you leave Claude Code to check Slack, CI, docs, or runbook, that's something you should wire into the agent's environment.

[**@wangyuanzju**](https://x.com/wangyuanzju/status/2072922979735122316) (remio founder) shared a practical integration guide for **using remio (agent with personal memory) alongside Obsidian and Claude Code/Codex.** The tools complement rather than compete — no switching cost needed. Addresses a real workflow question for developers using multiple AI coding assistants alongside knowledge management.

[**@leopardracer**](https://x.com/leopardracer/status/2073071937979347129) (36 likes) made a concrete point about **agent workspace organization**: one folder per concern, one index file at root. Without separation, "the agent opens 7 wrong files before finding the brief that never moved." Separating concerns dropped task time from 2 minutes to 10 seconds. Workspace/data organization directly impacts agent performance.

---

## Notable Mentions

- **@FeitengLi**: Bridgewater fine-tuned Qwen3-235B via Mira Murati's Thinking Machines platform for financial document analysis — 84.7% accuracy (29.8% lower error than GPT-5/Claude 4.8), 13.8× cost reduction. Open-weights + expert-curated data engine as the enterprise moat.
- **@jxnlco** (OpenAI vibes): "If you use Codex, is there any reason you still use ChatGPT?" — 704-reply discussion on the shift from chat to coding agents as primary interface.
- **@cnyzgkc**: First wave of "Vibe Coding victims" — non-technical bosses sold on zero-basis development now stuck with unmaintainable code mountains. Without engineering discipline, agent-generated code degrades.
- **@BtreeWw** (Uber ML platform engineer): LLMs perform poorly outside coding domains; training data lacks domain-specific optimization. "Context and harness can do very little here" — a reality check for agent builders.
- **@sheriyuo** (StepFun AI): "Denser ≠ Better" — challenges OPSD hype as a continual learning solution. Dense token-level teacher signals don't straightforwardly prevent catastrophic forgetting.
- **@MaxForAI** (LobeHub): Mocking vibe-coders for lacking CS fundamentals misses the point — AI's real shift is enabling cross-domain capability at low cost. "Don't refuse to evolve."
- **@MaxForAI**: Shawn Presser (@theshawwn, former #2 at Carmack's Keen AI lab, Groq LPU co-designer, Books3 creator) publicly pleads for employment amid a brutal AI job market. A stark signal about the 2026 AI employment landscape.
- **@ma_zhenyuan**: "Want to learn building AI agents? Skip courses — study LangChain's open-deep-research codebase thoroughly." Points to a production-grade multi-step agent with sub-agent delegation.
- **@iluciddreaming**: Alibaba Workbuddy competitor offers free Pro + 2000 credits (4000 with student verification), GLM 5.2 available. Signals competitive intensity in Chinese AI agent tooling.
- **@axiaisacat**: Meta open-sourced Astryx — but it's a React **design system** (not an agent runtime) that's "agent-ready" via a CLI and MCP server giving AI coding agents structured access to 90+ components. The MCP integration is the genuinely agent-relevant part.
- **@Lonely__MH**: Blind cost-cutting triggers "technical backlash" — KPI-driven AI adoption produces fragile systems. In the AI era, architects and senior engineers become MORE valuable, not less.
- **@yanhua1010**: Praises Workbuddy as a major winner — domain experts, expert teams, and Skills all in one platform. Reflects real market dynamics in the agent product ecosystem.

---

## Papers & Research

### Top Papers

1. **ContextNest: Verifiable Context Governance for Autonomous AI Agent** ([arXiv:2607.02116](http://arxiv.org/abs/2607.02116v1)) — Formalizes "context governance" for autonomous agents: provenance, version identity, integrity, traceability, and point-in-time reconstruction of external knowledge. Most agent RAG is relevance-only with no guarantees on provenance/versioning/auditability. ContextNest reframes retrieval as a governed, verifiable layer — directly relevant to building trustworthy long-running agents.

2. **Registry-Governed Agent Lifecycle: Completing EDDOps** ([arXiv:2607.00345](http://arxiv.org/abs/2607.00345v1)) — Frames enterprise agent adoption as EDDOps (Evaluation-Driven Development & Operations), where evaluation continuously governs the agent lifecycle — model registration, promotion, and retirement. Implemented as a registry-governed control plane on AWS AgentCore balancing quality, reliability, safety, latency, and cost. The missing ops layer between agent demos and deployable systems.

3. **Beyond Next-Token Prediction: RLVR for Tool-Use Agents on Atlassian Workflows** ([arXiv:2607.01465](http://arxiv.org/abs/2607.01465v1)) — Demonstrates RLVR (Reinforcement Learning from Verifiable Rewards) to specialize tool-use agents for enterprise SaaS. Addresses the mismatch between next-token pretraining and hitting exact endpoints/args/order — which otherwise manifests as silent failures. RLVR against verifiable workflow outcomes is a leading answer for reliable tool-use training.

4. **UA-ChatDev: Uncertainty-Aware Multi-Agent Collaboration** ([arXiv:2607.02186](http://arxiv.org/abs/2607.02186v1)) — Augments multi-agent software-development frameworks with explicit uncertainty awareness so role-based agents flag low-confidence steps, improving reliability across requirements analysis, coding, testing, and refinement. Reliability is the open problem in multi-agent coding pipelines.

5. **BOUNDARY_SYNC: Measuring Representational Coupling in Multi-Agent LLM Systems** ([arXiv:2607.01600](http://arxiv.org/abs/2607.01600v1)) — Introduces a protocol measuring how inter-agent communication couples agent representations, quantified by a Coupling Amplification Factor (CAF = JSD_cond / JSD_baseline) where CAF < 1 indicates homogenization. Gives practitioners a measurable signal for "are my agents actually diverse?" — rare and valuable for agent-architecture design.

### Notable Paper Mentions

- **MCP Server Architecture Patterns** ([arXiv:2606.30317](http://arxiv.org/abs/2606.30317v1)) — First systematic SE study of MCP: identifies 5 recurring architectural patterns and 4 anti-patterns through quantitative analysis of community-built MCP servers on GitHub. Directly actionable design vocabulary that didn't exist before.
- **MCP-Atlas: Benchmark for Tool-Use with Real MCP Servers** (Semantic Scholar) — First benchmark targeting real cross-server MCP orchestration and breadth. Addresses the gap between toy tool-use evals and production agent reliability.
- **SENTINEL: Failure-Driven RL for Tool-Using Agents** (Semantic Scholar) — Uses failure-driven reinforcement learning to train agents from their own environment-interaction failures. Learning from failure is the core loop for robust agent training.
- **Automating the Design of Embodied Agent Architectures** ([arXiv:2606.30111](http://arxiv.org/abs/2606.30111v1)) — Applies Agent Architecture Search (AAS) to automatically design agent architectures (where information is stored, how modules compose) rather than relying on researcher intuition. Companion AgentCanvas visual design platform.
- **LUMOS: Semantic OS Layer for AI Agents** ([arXiv:2606.30697](http://arxiv.org/abs/2606.30697v1)) — Argues current OS interfaces are a fundamental mismatch for agents — they need compact semantic state, grounded actions, and reliable feedback, not screenshots and mouse movements. Reframes computer-use agent design from "better vision models" to "better OS-level agent interfaces."
- **Entity Binding Failures in Tool-Augmented Agents** ([arXiv:2606.30531](http://arxiv.org/abs/2606.30531v1)) — Formalizes "entity binding failures" — the agent selects the correct tool but acts on the wrong external entity (wrong contact, wrong document). Identifies a failure mode that existing agent evals completely miss.
- **From Tool Connection to Execution Control: Security in MCP-Style Agent Runtimes** ([arXiv:2606.29073](http://arxiv.org/abs/2606.29073v1)) — Benchmarks security invariants in MCP-style runtimes, showing that as agents move from connection to execution, security decisions remain fragmented across clients, servers, prompts, and approval dialogs.
- **LAMP: Lean + MCP for Proof Repair** ([arXiv:2606.28841](http://arxiv.org/abs/2606.28841v1)) — Combines Lean 4 theorem proving with MCP to build an agentic framework that generates, verifies, and repairs mathematical proofs via kernel-checked feedback loops. A template for trustworthy agent output in high-stakes domains.
- **Model-Adaptive Tool Necessity** (Semantic Scholar) — Shows that whether a model should use a tool is itself model-dependent, revealing a "knowing-doing gap." Builders should tune tool-necessity thresholds per backbone.
- **Budget-Constrained Agentic LLMs** (Semantic Scholar) — Formalizes budget-constrained tool-augmented agents as sequential decision-making with priced, stochastic tool executions. Cost control as a principled framework, not just a hack.

---

## Chinese Community / 中文社区

本周中文 X 生态信号极强，主导了 harness 工程讨论：

- **Hugging Face Meta-Harness**（[@akshay_pachaar](https://x.com/akshay_pachaar/status/2072961737008336937)）：冻结模型权重，只改 harness 代码，就匹配了 Sonnet 4.6。harness 是新的竞争前沿。
- **Maka harness 工程**（[@jakevin7](https://x.com/jakevin7/status/2072923081463763342)）：同样的 DeepSeek Flash V4，纯靠 harness 工程，terminal-bench 打到 0.8，10 题花 4 块钱，接近 GLM-5.2 水平。
- **Browser Use CLI 3.0**（[@xiaohu](https://x.com/xiaohu/status/2072987979979837620)）：直接用 CDP 操作浏览器，不塞 DOM 进上下文；站点技能沉淀复用；缺函数当场自己写。
- **Superpowers vs Grill with Docs**（[@kasong2048](https://x.com/kasong2048/status/2072611852773920956)）：两种编排范式，对应 Agent 发展的不同阶段——一个假设 Agent 做不了长程任务需要详细计划，一个假设 Agent 有足够的 Goal 能力只需 5 行定义。
- **Codex 踩坑**（[@xiaogaifun](https://x.com/xiaogaifun/status/2072656279068463606)）：全员配 Codex 后，团队不再理解代码。"Codex 说行就行"——必须回头问为什么。
- **RAG 是死路**（[@lidangzzz](https://x.com/lidangzzz/status/2073028193783615527)）：正确用 memory、做好 indexing、给 agent 搜索工具、用 Groq/Cerebras 快推理。
- **Cursor/Claude Code/Codex 收敛**（[@grapeot](https://x.com/grapeot/status/2073076048342995050)）：竞争从模型转到 harness，三层自治：目标驱动循环、定时触发、多步规划。
- **Fable 5 禁令市场分析**（[@grapeot](https://x.com/grapeot/status/2072804509047488945)）：OpenRouter 数据显示 Anthropic 份额从 20.7% 降到 17.6%，GLM-5.2 两周内从 0 升到 6.7%。
- **Qwen 3.6 27B 本地通用**（[@Xudong07452910](https://x.com/Xudong07452910/status/2072898125027516832)）：256K 原生上下文，M5 Max 跑 30 tok/s，RTX 5090 跑 50 tok/s，创意写作+代码同时搞定。
- **Agent 工程全景**（[@snowboat84](https://x.com/snowboat84/status/2072840491314598244)）：八章完整拆解 Agent 架构，从 AutoGPT 到生产级 Agent 的三年工程进展。
- **给 Agent 搭工程环境**（[@RealCodedAlpha](https://x.com/RealCodedAlpha/status/2073045554654011521)）：Access（文档/CI/监控）、Knowledge（CLAUDE.md/Skills）、Tooling（Hooks/LSP/lint/test）三件套。记录你离开 Claude Code 去查什么。
- **快手技术：部门墙限制 Agent 提效**（[@quantum_soul1](https://x.com/quantum_soul1/status/2072938968942006613)）：新的生产力需要新的生产关系，扎克伯格说 Agent 没加速落地，原因在组织架构不在技术。

---

*Collected 2026-07-04. X "For You" feed + company/keyword search. Web/arXiv via search. Compiled with the research-digest pipeline (8 chunks, 131 candidates analyzed). Questions or corrections? Reply on [X](https://x.com/shuhangge).*
