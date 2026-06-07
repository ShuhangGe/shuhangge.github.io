---
title: "Agent Architecture Daily Digest — June 7, 2026"
description: "Supergoal adds a planning layer to Claude Code, Harness-1 externalizes search state, Boris writes loops not prompts, Anthropic verification stack revealed, Vercel's 4-layer agent infra, and MCP security surface mapped."
pubDate: "2026-06-07"
lang: en
tags: ["Agent Architecture", "AI Agents", "Harness Engineering", "MCP", "Skills", "Daily Digest"]
---

## TL;DR — Today's Overview

1. **Supergoal: planning layer for Claude Code/Codex**: A plugin implementing end-state conditions + per-iteration evaluation loops with persistent state (ROADMAP, STATE, specs) on disk, self-critique, and pre-flight smoke checks before execution. From the creator of umijs/dvajs at Ant Group. [X post by @chenchengpro](https://x.com/chenchengpro/status/2063280248591200267)

2. **Harness-1: 20B open-source search agent with state-externalizing harness**: Achieves frontier-level long-horizon search rivaling Opus-4.6 at Context-1-level cost by externalizing candidates, evidence, verification, and search history out of the model context. A major pattern shift for agent design. 802 likes. [X post by @patpcj](https://x.com/patpcj/status/2063298457398636570)

3. **Claude Code creator: "I write loops, not prompts"**: Boris demonstrates decomposing tasks into autonomous cycles of execution → verification → correction, not one-shot conversations. The "plan first, code last" pattern with parallel research agents is becoming canonical. 789 likes, 168K views. [X post by @Mikocrypto11](https://x.com/Mikocrypto11/status/2062792788706521333)

4. **Anthropic engineer reveals verification stack for production agents**: "Every agent in production lies. The good ones lie less, the great ones catch the lie before the user does." James Brady walks through the patterns Claude Code uses to keep agents honest at scale. [X post by @0x_rody](https://x.com/0x_rody/status/2063318596202242171)

5. **Vercel's 4-layer agent infrastructure stack**: AI Gateway + Sandbox + Workflow + MCP assembled as a complete agent platform, with zero switching cost as the core strategy. A significant platform play moving from frontend hosting to full agent infra. [X post by @grapeot](https://x.com/grapeot/status/2063068103265915171)

6. **NVIDIA's Polar: Agentic RL on any harness at scale**: Turns existing agent harnesses (Codex, Claude Code, Qwen Code, Pi) into RL training environments without modifying internals — training in the exact distribution of deployment. From a HuggingFace ML engineer. [X post by @SergioPaniego](https://x.com/SergioPaniego/status/2062911580564496576)

7. **Grok Build 0.1 vs Claude Code: parallel breadth vs deep sequential**: xAI's 8-subagent architecture with independent worktrees represents a meaningful alternative to Claude Code's single deep agent approach. Missing benchmarks are a red flag. [Analysis by @grapeot](https://x.com/grapeot/status/2062901506115055996)

8. **MCP fault taxonomy and description-code inconsistency mapped**: Two papers provide the first systematic analysis of MCP server reliability — runtime fault classification and the dangerous gap between tool descriptions and actual code behavior. Essential for MCP practitioners. [arXiv:2606.05339](http://arxiv.org/abs/2606.05339v1) · [arXiv:2606.04769](http://arxiv.org/abs/2606.04769v1)

9. **Agent evidence tracing and execution provenance**: Proposes frameworks for verifying, debugging, and auditing autonomous agent behavior beyond final-answer accuracy — directly addressing the trust gap for production deployment. [arXiv:2606.04990](http://arxiv.org/abs/2606.04990v1)

10. **Context compression via economic models for coding agents**: Dynamic programming meets cost-benefit analysis — only compress when the benefit-cost ratio is positive, factoring in distortion penalties, cache invalidation costs, and compression overhead. [X post by @aiandcloud](https://x.com/aiandcloud/status/2063136084603637767)

📊 Today's Numbers: **20 detailed items | 10 papers | 53 notable mentions | 155 total analyzed**

---

## Industry Leaders

### 1. Supergoal: A Planning Layer on Top of Claude Code
[X post by @chenchengpro](https://x.com/chenchengpro/status/2063280248591200267) · 46 likes · 4.3K views

Supergoal is a Claude Code / Codex CLI plugin that implements end-state condition + per-iteration evaluation as an auto-loop engine. When you type `/supergoal <task>`, it loads memory, parallel-scans the codebase, lists top-3 risks, then adaptively breaks the task into phases (2 for small changes, 8-12+ for greenfield). ROADMAP, STATE, and acceptance specs are written to `.supergoal/` on disk with self-critique and pre-flight smoke checks (deduplicated build/typecheck/lint/test) before execution begins.

**Why it matters**: The "write plan to disk → self-critique → pre-flight check → execute loop" pattern is a concrete agentic design pattern. From the creator of umijs/dvajs at Ant Group, this adds the persistent planning layer that many coding agents lack.

### 2. Harness-1: State-Externalizing Search Agent
[X post by @patpcj](https://x.com/patpcj/status/2063298457398636570) · 802 likes · 56K views

Harness-1 is a 20B parameter open-source search agent that externalizes candidates, evidence, verification, and search history out of the model context and into an external scaffold. The result: frontier-level long-horizon search rivaling Opus-4.6 and outperforming GPT-5.4, at Context-1-level cost and latency.

**Why it matters**: State-externalizing harness architecture is a major pattern shift. Moving search state out of context and into a structured external scaffold solves the context window bottleneck that plagues long-running agent sessions. The 802 likes and the author's credentials (CS PhD at UIUC, Research Fellow at Anthropic) validate this as a serious contribution.

### 3. Claude Code Creator: Write Loops, Not Prompts
[X post by @Mikocrypto11](https://x.com/Mikocrypto11/status/2062792788706521333) · 789 likes · 168K views

Boris, Claude Code's creator, demonstrated his daily workflow: decomposing tasks into autonomous cycles where the model continuously executes → verifies → corrects. The key quote: "I no longer write prompts for Claude. I write loops. Then the loops do the work." He runs dynamic workflows where the model progressively advances tasks rather than answering once.

**Why it matters**: The "write loops, not prompts" principle from Claude Code's creator validates autonomous agent loops as the canonical interaction pattern — a fundamental shift from chat-based AI usage.

### 4. Matt Van Horn's No-IDE Claude Code Workflow
[X post by @uniswap12](https://x.com/uniswap12/status/2063284267296461028) · 388 likes · 21.5K views

A detailed workflow using Claude Code without any IDE — only plan.md files and voice input. The `/ce:plan` command spawns parallel research agents: one analyzes the codebase, another reads accumulated experience docs, and others check external docs and best practices. All run simultaneously before any code is written.

**Why it matters**: The "plan first, code last" pattern with parallel research agents is a concrete agent architecture pattern showing progressive disclosure of context in practice.

### 5. Anthropic Engineer: Verification Stack for Production Agents
[X post by @0x_rody](https://x.com/0x_rody/status/2063318596202242171) · 221 likes · 36K views

Anthropic engineer James Brady reveals: "Every agent in production lies. We measured it." He walks through the verification stack the Claude Code team built and the patterns they adopted to keep agents honest at scale — detecting and catching agent hallucinations before users do.

**Why it matters**: First-party insight into production agent verification. The engineering patterns for catching agent fabrications are critical for anyone deploying agents in production.

### 6. Claude's Memory Architecture: Dreaming and Consolidation
[X post by @Av1dlive](https://x.com/Av1dlive/status/2063210013926269183) · 169 likes · 24K views

An Anthropic engineer breaks down Claude's internal memory management: why sessions start with zero memory, how memory stores enable cross-session read/write, and the "dreaming" process that organizes memory before it grows unbounded. Also covers building 5 task-specific assistants in one afternoon.

**Why it matters**: The "dreaming" process for memory consolidation is a novel architectural pattern worth studying for agent memory design — agents that self-organize their accumulated knowledge.

### 7. Vercel's 4-Layer Agent Infrastructure Stack
[X post by @grapeot](https://x.com/grapeot/status/2063068103265915171) · 2 likes · 906 views

Vercel has assembled AI Gateway + Sandbox + Workflow + MCP into a complete agent infrastructure stack. The core strategy isn't being the best at any single component — it's "every layer defaults to integration," making switching cost approach zero.

**Why it matters**: A significant platform play. Vercel is moving from frontend hosting to full agent development platform. The bundling strategy (AI Gateway + Sandbox + Workflow + MCP) represents the emerging "full-stack agent infra" category.

### 8. NVIDIA's Polar: Agentic RL on Any Harness
[X post by @SergioPaniego](https://x.com/SergioPaniego/status/2062911580564496576) · 148 likes · 15.5K views

NVIDIA's "Polar: Agentic RL on Any Harness at Scale" turns existing agent harnesses (Codex, Claude Code, Qwen Code, Pi) into RL training environments without modifying their internals. The key insight: frontier agents are so good partly because the model was trained inside the very harness it ships with.

**Why it matters**: Using the agent's own harness as the RL environment means training in the exact distribution of deployment. The "train in harness" pattern explains why frontier coding agents are so capable and brings this technique to the open ecosystem.

### 9. Grok Build 0.1: 8 Parallel Subagents vs Claude Code's Deep Sequential
[Analysis by @grapeot](https://x.com/grapeot/status/2062901506115055996) · [Full analysis](https://yage.ai/share/grok-build-0-1-20260605.html)

Grok Build 0.1 uses 8 parallel subagents with independent worktrees, Arena Mode auto-scoring, and a plan/search/build pipeline — a fundamentally different architecture from Claude Code's single deep agent with 1M context. API pricing: $1/$2 per million tokens. No public benchmarks yet.

**Why it matters**: The parallel breadth (Grok) vs deep sequential (Claude Code) tradeoff is the key architectural decision in coding agent design. This is the first detailed technical comparison with cost analysis and privacy policy differences.

### 10. Mirendil: Ex-Anthropic Leaders Build AI Scientist Models
[X post by @MaxForAI](https://x.com/MaxForAI/status/2063287063789945034) · 6 likes

Mirendil is a new lab founded by Anthropic's Discovery team lead and a Senior Research Scientist, building AI Scientist/Engineer models. $175M funding at $1B valuation from a16z and Kleiner Perkins. Karpathy reportedly taking over the Anthropic project they started.

**Why it matters**: Anthropic's automated pre-training R&D lead has left to build AI Scientist models. The focus on recursive self-improvement in AI R&D directly relates to agent architecture — agents that improve their own training.

### 11. Gemma 4 QAT: 256K Context on 16GB Mac
[X post by @mylifcc](https://x.com/mylifcc/status/2063104041358688536) · 535 likes · 90K views

Google's Quantization-Aware Training for Gemma 4 12B enables 256K context length on a 16GB Mac with only 1.5GB extra memory over standard Q4. Side-by-side test: same Mac, same prompt, regular Q4 at 32K vs Google QAT Q4 at 256K.

**Why it matters**: 256K context on consumer hardware directly impacts local agent deployment viability. QAT is a practical technique for making long-context agents accessible without cloud infrastructure.

---

## Trending

### 1. learn-claude-code: Build a Claude Code-Like Agent from Scratch
[X post by @Honcia13](https://x.com/Honcia13/status/2063274992976957461) · 58 likes · 65K GitHub stars

The most comprehensive hands-on guide to agent architecture patterns. 12 progressive sessions from "Bash is all you need": agent loop + bash tool, tool registration, todo planning, subagents, skills, context compression, persistence + dependency graph, async tasks, agent teams, team communication protocol, autonomous task claiming, and worktree + task isolation. Each session has standalone runnable Python files.

**Why it matters**: Covers every core concept from basic loop to multi-agent orchestration. 65,059 GitHub stars confirm this is the community's go-to resource for understanding agent internals.

### 2. Multi-Agent Orchestration with Git Worktree
[X post by @VincentLogic](https://x.com/VincentLogic/status/2063271705896972568) · 42 likes

A mature Claude Code workflow: split tasks across multiple agents (brainstorm, plan, implement, review, verify), each running in a separate git worktree. Run 4-8 parallel Claude Code sessions simultaneously, each in its own branch/directory.

**Why it matters**: Git worktree as the coordination mechanism for parallel AI coding agents is a practical, immediately applicable pattern. The multi-agent role separation (plan → implement → review → verify) maps directly to real engineering teams.

### 3. BrowserAct: Anti-Blocking Browser Automation for AI Agents
[X post by @GitHub_Daily](https://x.com/GitHub_Daily/status/2062746268993216772) · 440 likes · 23K views

BrowserAct open-sourced a CLI for AI agent browser automation with three-layer anti-blocking: fingerprint spoofing, CAPTCHA solving, and human-takeover fallback. Outputs LLM-optimized format. Skill Forge auto-explores site structures and generates reusable scrape scripts.

**Why it matters**: Solves the web automation reliability problem for agents. Skill Forge (auto-generating reusable scrape scripts) is an interesting agent self-improvement pattern.

### 4. "geju" (格局) Skill: Fighting Codex's Conservatism
[X post by @hylarucoder](https://x.com/hylarucoder/status/2062949336150073413) · 457 likes · 50K views

An open-source Codex skill that combats overly conservative, safe responses using techniques like "backcasting from endgame," "zero-legacy assumption," and "reverse constraints." After installing, using the keyword "格局打开" turns Codex into a bolder thinker.

**Why it matters**: Treating agent conservatism as a tunable parameter through skill-level prompting is a novel approach to shaping agent behavior. Skills modifying agent "personality" is an emerging pattern.

### 5. Obsidian + Codex + MCP + Skills Replaces 90%+ of Agent Products
[X post by @yihui_indie](https://x.com/yihui_indie/status/2063145601584464327) · 206 likes · 39K views

After one month of migrating from Notion to Obsidian, a developer found that Obsidian + Codex + API + MCP + Skills can replace 90%+ of AI Agent products. The combination of local knowledge base + coding agent + protocol + skills is emerging as a powerful agent stack.

**Why it matters**: An interesting data point on the "build vs buy" question for agent tooling. The local-knowledge + agent + protocol stack is a real alternative to SaaS agent products.

### 6. Hermes Agent Ecosystem Round-Up
[X post by @NFTCPS](https://x.com/NFTCPS/status/2063084253899071506) · 365 likes

Honcho (persistent memory backend), hermes-web-search-plus (multi-engine search), nemoclaw-community (NVIDIA enterprise extension), and hindsight (codebase memory) — the growing plugin/skill ecosystem around the Hermes agent framework.

**Why it matters**: Persistent memory, enterprise deployment, and intelligent search are key building blocks for production agents. The ecosystem growth indicates agent frameworks are maturing.

---

## Rising Stars

### 1. Context Compression via Economic Models
[X post by @aiandcloud](https://x.com/aiandcloud/status/2063136084603637767) · 117 likes

A novel approach combining dynamic programming and economic models for context compression in coding agents — only compressing when the benefit-cost ratio is positive, with distortion penalties, cache invalidation costs, and compression costs factored in.

**Why it matters**: The economic model framing (cost-benefit analysis for compression decisions) is a fresh architectural pattern for a core agent engineering problem: when and how to compress context in long-running sessions.

### 2. Platform Economics of AI Agent Tooling
[X post by @grapeot](https://x.com/grapeot/status/2063394756051525704)

Analysis of Cloudflare's VoidZero/Vite acquisition as a pattern: "Tool layer creates usage value, platform layer captures commercial value." Notes that AI agents are accelerating this by hardcoding platform preferences in system prompts.

**Why it matters**: Agents hardcoding platform preferences in system prompts is a real architectural phenomenon with implications for agent system design — the agent itself becomes a distribution channel.

---

## arXiv Papers

### 1. Beyond Tokens: Latent Communication in Multi-Agent Systems
[arXiv:2606.05711](http://arxiv.org/abs/2606.05711v1)

Proposes a unified framework for latent communication in LLM-based multi-agent systems, replacing token-by-token verbalization with more efficient inter-agent protocols. Directly challenges whether agents should communicate via natural language at all.

### 2. MCP Runtime Fault Taxonomy
[arXiv:2606.05339](http://arxiv.org/abs/2606.05339v1)

First systematic taxonomy of runtime faults in MCP servers, identifying reliability challenges in tool-augmented AI workflows. Configuration parameters accepted but not enforced lead to unintended defaults.

### 3. Description-Code Inconsistency in MCP Servers
[arXiv:2606.04769](http://arxiv.org/abs/2606.04769v1)

Identifies and measures description-code inconsistency in real-world MCP servers — where natural language tool descriptions don't match actual implementation, creating security vulnerabilities. A critical trust surface in the MCP ecosystem.

### 4. ADK Arena: Evaluating Agent Development Kits
[arXiv:2606.05548](http://arxiv.org/abs/2606.05548v1)

First systematic comparison of major agent frameworks (LangChain, CrewAI, AutoGen, etc.) using LLM-as-a-Developer methodology — holding developer skill constant and varying only the framework.

### 5. Evidence Tracing and Execution Provenance in LLM Agents
[arXiv:2606.04990](http://arxiv.org/abs/2606.04990v1)

Proposes frameworks for verification, debugging, and auditing of agent behavior beyond final-answer accuracy. Essential for production deployment trust.

### 6. Do More Agents Help? BenchAgent Evaluation
[arXiv:2606.05670](http://arxiv.org/abs/2606.05670v1)

Directly answers the key practical question: does scaling agents actually improve results? Introduces controlled evaluation with shared loaders, tool access, and trajectory logging.

### 7. ToolChoiceConfusion: Causal Minimal Tool Filtering
[arXiv:2606.06284](http://arxiv.org/abs/2606.06284v1)

Identifies causal confusion in LLM tool selection and proposes minimal tool filtering to reduce wrong-tool calls. Directly addresses a known pain point in agent engineering.

### 8. Self-Evolving Agents: Multi-Iteration Experience Internalization
[arXiv:2606.04703](http://arxiv.org/abs/2606.04703v1)

Discovers that multi-iteration experience internalization (not just single-iteration transfer) is critical for self-evolving agents, addressing catastrophic forgetting under continual learning.

### 9. CLI-Anything: Agent-Native Computer Use
[arXiv:2606.03854](http://arxiv.org/abs/2606.03854v1)

Argues for CLI over GUI as the primary computer-use interface for agents — more reliable, faster, and more auditable than visual screen interpretation. Challenges the dominant GUI-agent paradigm.

### 10. Grounded Interaction Synthesis for Agentic Tool Use
[arXiv:2606.02001](http://arxiv.org/abs/2606.02001v1)

Generates training data for agentic tool use by grounding synthetic interactions in real tool execution, rather than relying on LLM-generated trajectories that may not execute.

---

## Notable Mentions

- **Agent Reach (21.6K stars)**: Scaffolding tool for AI agents to read Twitter, Reddit, YouTube, Bilibili, Xiaohongshu with zero API costs — [X post by @xiaojianjian567](https://x.com/xiaojianjian567/status/2063100303684309174)
- **Claude Code + Codex side-by-side workflow**: Use Claude for planning, Codex for execution — [X post by @lxfater](https://x.com/lxfater/status/2063089110265516450)
- **Open-source model rankings**: Kimi 2.6 (all-round), DeepSeek v4 Pro (instruction following), Minimax M3 (OS coding agent), GLM 5.1 (long-horizon tasks) — [X post by @cjzafir](https://x.com/cjzafir/status/2062905703342420307)
- **200-page ML foundations guide**: Neural nets, transformers, agents, vision, hardware from scratch — [X post by @virkvarjun](https://x.com/virkvarjun/status/2062972725421854891)
- **"geju" skill for bold Codex responses**: Fight agent conservatism with structured prompting techniques — [X post by @hylarucoder](https://x.com/hylarucoder/status/2062949336150073413)
- **1000+ agent skills collection**: Curated skills for Codex, Claude Code, Gemini CLI, GitHub Copilot — [X post by @aronhouyu](https://x.com/aronhouyu/status/2063092531563549024)
- **Claude Code prompting patterns (Spanish)**: Summary of Boris's 28-minute video on CLAUDE.md, memory shortcuts, parallel sessions — [X post by @precisox](https://x.com/precisox/status/2063286718480949499)
- **Top 10 Claude Code research skills**: Nature-skills, PaperSpine, Cite Verify, LaTeX Writer, Survey Builder — [X post by @Phoenixyin13](https://x.com/Phoenixyin13/status/2063027389114827075)
- **Butler AI Agent maintains Shiji knowledge graph**: 12,000+ autonomous wiki updates, 14K entities, 126K annotations — [X post by @yhslgg](https://x.com/yhslgg/status/2063115765599891966)
- **Codex "Reconnecting" fix**: WebSocket protocol failure diagnosis and 3 solutions — [X post by @Lonely__MH](https://x.com/Lonely__MH/status/2063134508267012264)
- **Codex Product Design plugin**: Generates 3 high-fidelity mockups before coding — [X post by @BTCqzy1](https://x.com/BTCqzy1/status/2063165828208746639)
- **Agentic Workflow taxonomy**: Enterprise framework distinguishing augmented LLMs, workflow-with-LLM, workflow agents, and true agentic workflows — [X post by @teach_fireworks](https://x.com/teach_fireworks/status/2062721018809205018)
- **"The Office" as multi-agent system**: Michael Scott orchestrates, Dwight/Jim/Pam as separate Claude Code instances — [X post by @VincentLogic](https://x.com/VincentLogic/status/2063197701962092833)
- **Maple: Service dependency map with MCP code server**: Visualizes backend telemetry + AI agent system review — [X post by @VincentLogic](https://x.com/VincentLogic/status/2063279101264417082)
- **NLP researcher on AI-generated papers**: "As a carbon-based life, analyzing silicon-based ideas feels strange" — [X post by @dongxi_nlp](https://x.com/dongxi_nlp/status/2062871242453991483)
- **170 AI agents running a company**: Chinese professor deploys 170 parallel agents for management and development — [X post by @VincentLogic](https://x.com/VincentLogic/status/2063261749407883276)
- **Chinese desktop agent comparison**: Coze, Wukong, WorkBuddy, Mavis vs Codex — [X post by @AYi_AInotes](https://x.com/AYi_AInotes/status/2063317039259738448)
- **Codex mistake log pattern**: Persistent agent memory via error logs + AGENTS.md project conventions — [X post by @legacyvps](https://x.com/legacyvps/status/2063247378833191316)
- **Streaming Communication in Multi-Agent Reasoning**: Pipelines adjacent agents instead of generate-then-transfer — [arXiv:2606.05158](http://arxiv.org/abs/2606.05158v1)
- **Action-state communication for MAS**: Free-form natural language between agents inflates tokens and degrades performance — [arXiv:2606.05304](http://arxiv.org/abs/2606.05304v1)
- **MCP-native graph planning for biomedical agents**: Structured graph-based tool orchestration over flat prompt-retrieved descriptions — [arXiv:2606.04494](http://arxiv.org/abs/2606.04494v1)
- **Agent capability registries as "market for lemons"**: MCP/A2A registries need a trust verification layer — [arXiv:2606.03034](http://arxiv.org/abs/2606.03034v1)
- **MCP-Persona benchmark**: First benchmark for MCP-connected agents in personal productivity tasks — [arXiv:2606.02470](http://arxiv.org/abs/2606.02470v1)
- **SafeMCP: Power regulation for MCP agents**: Look-ahead reasoning to prevent power-seeking behavior — [arXiv:2606.01991](http://arxiv.org/abs/2606.01991v1)
- **Guardrail feedback for LLM agents**: Moves from binary allow/deny to actionable remediation plans — [arXiv:2606.05805](http://arxiv.org/abs/2606.05805v1)
- **MLEvolve: Self-evolving ML algorithm discovery**: Overcomes inter-branch information isolation in agent optimization — [arXiv:2606.06473](http://arxiv.org/abs/2606.06473v1)
- **MUSE: Unified agentic harness for multimodal LLMs**: Significant capability gains without retraining — [arXiv:2606.03005](http://arxiv.org/abs/2606.03005v1)
- **CollabSim: Measuring multi-agent collaboration failures**: CSCW methodology for controlled MAS experiments — [arXiv:2606.06399](http://arxiv.org/abs/2606.06399v1)
- **Computer-use agent safety reality check**: 42-98% attack success rates cluster on retired models — [arXiv:2606.05233](http://arxiv.org/abs/2606.05233v1)
- **OpenAgenet: Open infrastructure for trusted agent interconnection**: Identity, discovery, and authorization for multi-operator networks — [arXiv:2606.03161](http://arxiv.org/abs/2606.03161v2)
- **Multi-agent orchestration with hierarchical memory**: Bridges requirements and software architecture — [arXiv:2606.01385](http://arxiv.org/abs/2606.01385v1)
- **CLI-Anything: Agent-native computer use via CLI, not GUI**: More reliable, faster, and more auditable — [arXiv:2606.03854](http://arxiv.org/abs/2606.03854v1)
- **Synthesize and Reward: RL for multi-step tool use**: Grounding training queries in live server state — [arXiv:2606.03892](http://arxiv.org/abs/2606.03892v2)
- **Tool-aware optimization with entropy guidance**: Balancing tool reliance vs model capability in agentic RL — [arXiv:2606.03762](http://arxiv.org/abs/2606.03762v1)
- **Critic-guided heterogeneous multi-agent reasoning**: Different agents specialize, a critic guides — [arXiv:2606.05704](http://arxiv.org/abs/2606.05704v1)
- **Diagnosing knowledge gaps in LLM tool use**: How agents handle novel APIs absent from training data — [arXiv:2606.03657](http://arxiv.org/abs/2606.03657v1)
- **Trace-driven simulation for multi-model agentic systems**: Reproducible evaluation without expensive live execution — [arXiv:2606.01725](http://arxiv.org/abs/2606.01725v1)
- **Google's RNN-based Transformer alternative**: Potentially ending the quadratic complexity bottleneck — [X post by @HowToAI_](https://x.com/HowToAI_/status/2063249102067118353)
- **Value diversity in multicultural agent systems**: Framework for agent team composition across cultural contexts — [arXiv:2606.05985](http://arxiv.org/abs/2606.05985v1)
- **Thinking with Imagination**: VLMs + world simulators for visual spatial reasoning beyond observed images — [arXiv:2606.06476](http://arxiv.org/abs/2606.06476v1)
- **DragOn: Drag-based GUI interaction benchmark**: Fills the drag-and-drop capability gap in GUI agents — [arXiv:2606.06322](http://arxiv.org/abs/2606.06322v1)
- **LAP: Agent-to-Instrument Protocol for autonomous science**: Standardized interface from reasoning agent to physical instruments — [arXiv:2606.03755](http://arxiv.org/abs/2606.03755v1)
- **MedCUA-Bench: Clinical computer-use agent benchmark**: Highlights gap between general and specialized GUI navigation — [arXiv:2606.03203](http://arxiv.org/abs/2606.03203v1)
- **SHIELDS: Multi-agent OS hardening**: Iterative remediation against DISA STIG security standards — [arXiv:2606.05476](http://arxiv.org/abs/2606.05476v1)
- **"The End of Software Engineering" position paper**: Agents fundamentally restructuring the software paradigm — [arXiv:2606.05608](http://arxiv.org/abs/2606.05608v1)
- **EGTR-Review: Multi-agent teacher distillation for peer reviews**: Evidence-grounded with source traceability — [arXiv:2606.06025](http://arxiv.org/abs/2606.06025v1)
- **Serenity Skill + Codex for A-share analysis**: Real-world domain-specific Skill usage — [X post by @beefnoode](https://x.com/beefnoode/status/2063190596333015206)
- **Vibe-coded stock research website**: Ex-Meta AI engineer distills influencer tweets into searchable knowledge base — [X post by @qinbafrank](https://x.com/qinbafrank/status/2063134878888247354)
- **Claude Design tips**: Bilingual design principles from Anthropic's design tool — [X post by @dotey](https://x.com/dotey/status/2063291945972060525)
- **MIT LLM reasoning claim**: Announces breakthrough in real logical reasoning (no paper link, unverified) — [X post by @mdancho84](https://x.com/mdancho84/status/2062921178713411868)
