---
title: "Agent Architecture Daily Digest — June 25, 2026"
description: "Loop engineering is the new unit of agent design: the loop replaces the prompt as what builders optimize, and memory is what makes the loop not forget between runs. Sakana Fugu turns multi-agent orchestration into a single billable API with an RL-trained router. Claude Code ships first-class Agent Swarms. Claude Code Routines make the 'agent as cron job' pattern official. A new paper reverse-engineers the Claude Code harness into a taxonomy of agent design. And the computer-use research cluster (MACU, Agent Alpha, SHERLOC, SAFARI) converges on multi-agent task decomposition and tree search."
pubDate: "2026-06-25"
lang: en
tags: ["Agent", "LLM", "AI Architecture", "Daily Digest"]
---

## TL;DR — Today's Overview

> Top 10 things to know today:

1. **Loop engineering is the day's dominant pattern — the loop replaced the prompt as the unit builders optimize.** At least six independent posts converged on the same idea: design the loop that prompts the agent, don't prompt the agent directly. The discipline comes with concrete constraints (Evy Borov's "four layers to keep a loop from running away"), a cost model ("the costliest thing in AI coding is no longer writing code, it's managing the agent loop"), and a memory requirement ("the loop never forgets between runs"). — [Evy Borov](https://x.com/evyborov/status/2066905466853118376), [mem0](https://x.com/mem0ai/article/2067305118891163833)

2. **Sakana Fugu productizes orchestration as a single API.** Fugu routes across GPT-5, Claude, and Gemini behind one OpenAI-compatible endpoint using a 7B-parameter router trained with reinforcement learning to pick the right model per sub-task. Fugu Ultra reportedly matches or exceeds GPT-5.5, Gemini 3.1 Pro, and Opus 4.8 — but it isn't a foundation model, it's a coordinated multi-agent system charged by the top model used. Orchestration itself has become the product. — [Sakana AI](https://sakana.ai/fugu-release/)

3. **Claude Code ships first-class Agent Swarms ("Teams").** A lead agent can now delegate to multiple parallel teammates that research, debug, and build while coordinating with each other. The lead/teammate delegation pattern moves coding agents from single-agent to parallel coordinated agents — multi-agent orchestration is no longer something you bolt on, it's a first-class primitive inside the tool. — [@bcherny](https://x.com/bcherny/status/2019472394696683904)

4. **Claude Code Routines make the "agent as cron job" pattern official.** Trigger templated agents via GitHub events or API; Anthropic uses them internally for docs and backlog maintenance. Event-driven templated agents formalize scheduled, resumable agentic workflows as a supported primitive. — [@noahzweben](https://x.com/noahzweben/status/2044093913376706655)

5. **A paper reverse-engineers the Claude Code harness into a design taxonomy.** "Dive into Claude Code" (arXiv:2604.14228) analyzes the TypeScript source and cross-compares with OpenClaw and Hermes-Agent across permission systems, context management, and extensibility — producing thirteen design principles for agent harnesses. The most architecturally rigorous item in the day's collection. — [@burkov](https://x.com/burkov/status/2048233381305942381)

6. **The three-tier memory architecture that finally outgrows flat AGENTS.md.** A deployed system replaces a single AGENTS.md with hot-memory constitution, 19 specialized domain-expert agents, and a cold-memory knowledge base — mapping the CPU cache hierarchy onto agent memory. Directly actionable for anyone whose AGENTS.md has stopped scaling. — [@omarsar0](https://x.com/omarsar0/status/2027770787659464812)

7. **Agent Arena: real-world agentic evals with real tools.** Millions of live sessions where real users accomplish real tasks, with models given web search, filesystem, and terminal tools. This is the missing benchmark layer — agentic competence measured by actual tool calls in live sessions, not static Q&A. — [@arena](https://x.com/arena/status/2062566749418233981)

8. **Cua Driver brings background computer-use to Windows.** Any agent (Claude Code, Codex, custom loop) can drive real Windows apps through CLI or MCP. Another application surface becomes agent-addressable — and the CLI/MCP integration means the loop (see item 1) can now operate Windows software. — [@trycua](https://x.com/trycua/status/2059688960838828391)

9. **Agent skills are forming a new software supply chain — and a new attack surface.** New research reframes modular agent skills as a supply chain, surfacing risks (poisoned skills, dependency injection, trust boundaries) that builders haven't built tooling for yet. Security is moving up the stack alongside the loop. — [@FeiziSoheil](https://x.com/FeiziSoheil)

10. **Multi-Agent Computer Use (MACU): a DAG-revising CUA manager.** From CMU (Koh, Salakhutdinov, Fried), a manager LLM decomposes tasks into a DAG of subtasks, dispatches parallel computer-use subagents, and continuously revises the DAG as findings arrive. The DAG-revision loop is a practical advance over static task decomposition. — [arXiv (Semantic Scholar)](https://www.semanticscholar.org/paper/ff065ba9442e7dfd4c77e8d3d752b5697175875e)

📊 Today's Numbers: **6 company updates | 8 industry leaders | 5 trending | 1 rising star | 10 papers (5 detailed) | 36 notable mentions | 66 total items**

---

## The Pattern: From Prompts to Loops — and Orchestration as Product

Read today's collection as one signal and a cluster of supporting evidence. The signal is **loop engineering**: across at least six independent posts, the consensus is that the loop — not the prompt — is now the unit agent builders optimize.

Evy Borov names the discipline directly: [Master loop engineering](https://x.com/evyborov/status/2066905466853118376) — "the four architectural layers that keep a loop from running away." [mem0](https://x.com/mem0ai/article/2067305118891163833) connects loops to memory layers: "You shouldn't be prompting coding agents anymore. You should be designing loops that prompt them" — and the loop's state persists across iterations. [cyrilXBT](https://x.com/cyrilXBT/article/2068850474384609543) and [sairahul1](https://x.com/sairahul1/article/2064277888216555684) both frame loops as "the quiet skill behind every AI system that actually scales," with the loop running inside a framework you build and "never forgetting between runs." [HadrianVeidt0](https://x.com/HadrianVeidt0) reframes the cost model: "the costliest thing in AI coding is no longer writing code, it's managing the agent loop." And [titus_k](https://x.com/titus_k) captures the meta-trend: the question for 2026 agent builders is no longer *which model to call* but *how to architect the system around the model*.

What binds these together: **prompt engineering optimized a single call; loop engineering optimizes the structure that makes and learns from many calls.** Three concrete shifts fall out of it:

- **The loop needs guardrails, not just a good prompt.** Borov's "four layers" and the supply-chain framing of skills both point at the same production failure mode: an unbounded loop is destructive. Safety has moved from "is the prompt safe?" to "is the loop constrained?"
- **The loop needs memory.** mem0's whole pitch is that loop engineering without persistent memory means re-explaining everything every morning — exactly the pain [PrajwalTomar](https://x.com/PrajwalTomar_/status/2066195642997969255) names for session-based agents. The three-tier memory architecture (hot/wiki/cold) is the design answer.
- **The loop is becoming a primitive, not a hand-rolled script.** Claude Code Routines (event-triggered templated agents) and Claude Code Teams (built-in multi-agent delegation) both move orchestration out of ad-hoc shell scripts and into first-class tool features.

The second strand of the day is **orchestration becoming a product**. Sakana Fugu is the clearest example — a multi-agent system sold as a single model API, where the router is the novel component and you're billed on the top-tier model used. When orchestration is the product rather than a build-it-yourself layer, the competitive frontier shifts from "which foundation model" to "which orchestration quality," and the agent-OS abstraction layer above individual models starts to feel real.

The third strand — quieter but worth tracking — is the **computer-use research cluster**. MACU (DAG-revising multi-agent CUAs), Agent Alpha (tree search that reuses partial successes), SHERLOC (structured fault localization cutting the ~50% of agent budget wasted finding where to fix), and SAFARI (active-investigation fault attribution for long-horizon runs) all push CUA architecture toward multi-agent decomposition, test-time search, and better debugging. Watch this cluster — it's where the next practical CUA gains will come from.

---

## Company Updates

### Sakana Fugu: Orchestration as a Single Billable API

[**Fugu**](https://sakana.ai/fugu-release/) exposes a full multi-agent system through one OpenAI-compatible API. A 7B-parameter router, trained with reinforcement learning, decides which foundation model (GPT-5, Claude, Gemini) to call for each sub-task. Fugu Ultra reportedly matches or exceeds GPT-5.5, Gemini 3.1 Pro, and Opus 4.8 on benchmarks — but the key insight is that Fugu Ultra is *not* a foundation model; it's a coordinated multi-agent system. Sakana charges based on the top model used during coordination. [Early beta applications](https://sakana.ai/fugu-beta/) are open.

**Why it matters:** This is a commercial implementation of the "agent OS above individual models" thesis. The routing model itself — an RL-trained selector — is the novel architectural component. Self-reported benchmarks aside, the real bet is that for agent products, *orchestration quality may matter more than raw model scores.* (1,000+ likes)

### Claude Code Teams: First-Class Agent Swarms

Claude Code now supports ["Teams," aka Agent Swarms](https://x.com/bcherny/status/2019472394696683904): a lead agent delegates to multiple parallel teammates that research, debug, and build while coordinating with each other. This moves multi-agent orchestration from a bolt-on pattern to a first-class primitive inside the coding tool.

**Why it matters:** The lead/teammate delegation pattern is a concrete swarm architecture for software engineering — the shift from single-agent to parallel coordinated agents happens *inside* the tool rather than in an external orchestrator.

### Claude Code Routines: The Agent-as-Cron-Job Pattern, Made Official

[Claude Code Routines](https://x.com/noahzweben/status/2044093913376706655) let you trigger templated agents via GitHub events or an API. Anthropic uses them internally for documentation and backlog maintenance. This formalizes event-driven, scheduled, resumable agentic workflows as a supported primitive.

**Why it matters:** The "agent as cron job" pattern — scheduled, templated, event-triggered — has been a DIY hack for a year. Routines makes it official, and the GitHub-event trigger ties agent loops directly to the development workflow.

### Agent Arena: Agentic Evals with Real Tools

[Agent Arena](https://x.com/arena/status/2062566749418233981) measures agent performance through millions of live real-user sessions where real users accomplish real tasks. Models are given web search, filesystem, and terminal tools, enabling evaluation of actual agentic task completion rather than static benchmarks.

**Why it matters:** Static Q&A leaderboards don't tell you whether an agent can *do* things. Agentic evals that use real tool calls in live sessions are the missing benchmark layer — the gap between chatbot scores and real agent utility.

### Cua Driver: Background Computer-Use on Windows

[Cua Driver](https://x.com/trycua/status/2059688960838828391) brings background computer-use to Windows: any agent (Claude Code, Codex, or a custom loop) can drive real Windows apps through CLI or MCP integration.

**Why it matters:** Another application surface becomes agent-addressable — and notably via CLI/MCP, which means the loop (see the day's pattern) can now operate Windows software programmatically rather than only through screen interaction.

### mem0: Loop Engineering that Operates on Memory

[mem0](https://x.com/mem0ai/article/2067305118891163833) introduces "loop engineering" that explicitly operates on agent memory: design loops that prompt agents rather than manually prompting, with persistent memory across loop iterations.

**Why it matters:** Connecting loop engineering directly to a memory layer shows the architectural link between orchestration loops and state persistence — the loop never forgets between runs, which is exactly the gap session-based agents leave open.

---

## Industry Leaders

### Omar Khattab: Maximize Claude Code by Investing in Skills, Not Prompts

[Omar Khattab](https://x.com/omarsar0/status/2006390906371629222): to maximize Claude Code, invest time building subagents, skills, commands, planning, MCP tools, and context engineering patterns — they make a huge difference, and all workflows transfer to Codex.

**Why it matters:** From a well-known practitioner, the explicit message is that leverage in agentic coding comes from reusable skills/subagents and context engineering, not single prompts — and the patterns are cross-tool portable.

### The Three-Tier Memory Architecture That Outgrows AGENTS.md

[Omar Khattab](https://x.com/omarsar0/status/2027770787659464812): flat AGENTS.md files don't scale beyond modest codebases. The alternative, from a deployed production system (paper: "A Three-Tier Memory Architecture for Persistent AI Assistants"), is a hot-memory constitution, 19 specialized domain-expert agents, and a cold-memory knowledge base. The hot/wiki/cold split maps directly to a CPU cache hierarchy.

**Why it matters:** The most architecturally concrete item in the day's collection — a real deployment showing exactly how to move beyond flat instruction files to tiered memory plus specialized agents. Directly addresses context-window and session-compaction failures in long-running assistants.

### "Dive into Claude Code": An Academic Taxonomy of the Harness

[Andriy Burkov](https://x.com/burkov/status/2048233381305942381) surfaces [arXiv:2604.14228](https://arxiv.org/abs/2604.14228), which reverse-engineers Claude Code's TypeScript source and cross-compares it with OpenClaw and Hermes-Agent. The paper identifies five core values and thirteen design principles, examining permission systems, context management, and extensibility mechanisms. A companion repo (VILA-Lab/Dive-into-Claude-Code) maps the wider agent design space including memory systems, harness extensions, the MCP ecosystem, and specialized agents.

**Why it matters:** The deepest architecture content in the day — a rigorous taxonomy of agent-harness design (agentic loops, subagent context isolation, hook systems, tool/MCP scoping) directly useful to anyone designing their own harness.

### Sam Bhagowalia: Thousands of Hours with Claude Code and Codex

[calcsam](https://x.com/calcsam/status/2065222716609970680) distills thousands of hours building with Claude Code and Codex. The key lessons: run a long session without quality dropping, and extend your agent via MCP servers, skills, and plugins.

**Why it matters:** Practitioner emphasis on session durability and long-horizon quality, plus plugin/MCP/skill composition — the actual operating concerns of real agentic coding.

### The Three Action Modes: Tools, Bash, Codegen

[ethanolivertroy](https://x.com/ethanolivertroy/status/2030654868906459480) documents building a GRC (governance, risk, compliance) agent with the Claude Agent SDK using three action modes: **Tools** (atomic operations with typed schemas), **Bash** (composable, flexible, minimal context cost), and **Codegen** (dynamic logic generation).

**Why it matters:** The Tools/Bash/Codegen three-mode taxonomy is a genuinely useful design pattern for choosing agent execution strategies. Codegen especially — dynamic logic as an action type — is underexplored beyond fixed tools.

### Evy Borov: The Four Layers That Keep an Agent Loop from Running Away

[Evy Borov](https://x.com/evyborov/status/2066905466853118376): "Master loop engineering — the discipline of designing agent loops, including the four architectural layers that keep a loop from running away" (becoming unbounded or destructive).

**Why it matters:** Concrete architectural guidance for production safety. Preventing runaway loops is a real production failure mode that most builders haven't solved — naming the four constraint layers is immediately useful.

### Agent Skills as a New Software Supply Chain

[Soheil Feizi](https://x.com/FeiziSoheil) frames new research: as AI agents become modular, their "skills" are forming a new software supply chain — raising supply-chain security and dependency-management questions for agent frameworks.

**Why it matters:** Reframing agent skills as a supply chain surfaces an entire class of risks (poisoned skills, dependency injection, trust boundaries) that agent builders haven't yet built tooling for. A novel security-architecture angle.

### Priya Vergadia: A Full Hermes Agent Setup Guide

[Priya Vergadia](https://x.com/pvergadia/article/2066962737427841383) publishes a full setup guide for Hermes Agent — an AI agent that acts autonomously via markdown-based skills and cron schedules, so that "before you've had coffee, your AI Agent has already acted."

**Why it matters:** Hermes Agent's skill/cron/scheduled-task model is a concrete implementation of the "agent that acts while you sleep" pattern — the same primitive Claude Code Routines is now formalizing (see Company Updates).

---

## Trending

### Sakana Fugu "One Model to Command Them All"

[Trending at 1,000+ likes](https://sakana.ai/fugu-release/): Fugu is a multi-agent orchestration system delivered as a single OpenAI-compatible API that dynamically orchestrates frontier models. Fugu Ultra reportedly matches/exceeds GPT-5.5, Gemini 3.1 Pro, and Opus 4.8. Commentary notes that for agent products, orchestration quality may matter more than raw benchmark scores — and that Fugu Ultra isn't a foundation model but a coordinated multi-agent system. The most important release of the day.

### Every Agentic Engineering Hack I Know (June 2026) — 9,130 likes

[mvanhorn](https://x.com/mvanhorn/status/2061978364391592110) follows up on a 913K-view post with the June 2026 edition: a high-velocity workflow guide for steering Claude Code and Codex. The standout detail is every session being reachable from the Claude mobile app — start a live run at your desk, resume the same run on your phone mid-task. Highest-engagement item in the collection; documents the lived agentic-coding workflow many builders are moving toward.

### The Claude Code Config Bible (Anthropic Hackathon Winner)

[aiwithjainam](https://x.com/aiwithjainam/status/2028436830404944129): an Anthropic hackathon winner published a complete Claude Code configuration bible covering agents, skills, hooks, commands, rules, and MCPs — battle-tested over 10+ months, including PM2-managed multi-agent orchestration.

**Why it matters:** A battle-tested config reference for the full Claude Code extensibility surface — directly actionable for builders.

### Multi-Step Agentic Workflows on Hosted Runners

[DavidWells](https://x.com/DavidWells/status/2062338819178029306): a multi-step agentic workflow on Netlify Agent runners generates feature ideas by orchestrating Claude Code, Codex, and Gemini and synthesizing actionable next steps.

**Why it matters:** A concrete example of heterogeneous-model orchestration on hosted agent runners producing a structured output — orchestration moving onto managed infrastructure.

### The Session-Memory Gap in Coding Agents

[PrajwalTomar](https://x.com/PrajwalTomar_/status/2066195642997969255): session-based agents (Claude Code, Cursor, Codex, OpenClaw) force builders to re-explain context every morning; persistent cross-session context is the gap.

**Why it matters:** Names the exact pain point — ephemeral session memory — that drives the memory-layer and skill trends across coding agents.

---

## Rising Stars

### Palmier Pro: An MCP-Native Video Editor Agents Can Drive End to End

[so_sthbryan](https://x.com/so_sthbryan/status/2068067293599350954): Palmier Pro is a free, open-source macOS video editor with a built-in MCP server. Point Claude Code, Codex, or Cursor at it and the agent edits your timeline — the first real video editor an agent can drive end to end.

**Why it matters:** A clear example of MCP-as-application-interface: the app exposes itself as a tool surface that agents operate, extending agent-controllable surfaces into creative software.

---

## Papers

### Multi-Agent Computer Use (MACU) — DAG-Revising CUA Coordination

[Semantic Scholar](https://www.semanticscholar.org/paper/ff065ba9442e7dfd4c77e8d3d752b5697175875e) · CMU (Koh, Salakhutdinov, Fried) · code at [github.com/kohjingyu/multi-agent-computer-use](https://github.com/kohjingyu/multi-agent-computer-use)

A manager LLM creates a DAG of subtasks from the user request, dispatches parallel computer-use subagents for each subtask, and *continuously revises the DAG* as subagents report findings. The paper argues single-serial-agent CUAs are suboptimal for complex long-horizon tasks.

**Why it matters:** The DAG-revision loop — updating the plan based on real-time findings — is a practical advance over static task decomposition and a major architectural shift for computer-use agents.

### SHERLOC: Structured Fault Localization for Code Repair Agents

[arXiv:2606.24820](http://arxiv.org/abs/2606.24820v1)

SHERLOC is a training-free framework that reframes fault localization for code repair agents. Rather than treating localization as file retrieval, it produces actionable diagnostic output — addressing the observation that LLM agents solving repo-level coding tasks spend ~50% of their budget locating faults before editing.

**Why it matters:** If coding agents waste half their budget just finding *where* to fix, structured localization is a direct efficiency win. Turns localization from retrieval into diagnosis.

### SAFARI: Active-Investigation Fault Attribution for Long-Horizon Runs

[arXiv:2606.24626](http://arxiv.org/abs/2606.24626v1)

SAFARI replaces loading full agent trajectories into context with a tool-augmented diagnostic loop that actively investigates and localizes the failing step in long multi-step agent runs, beating prior methods by 20%.

**Why it matters:** As agents tackle tasks spanning hundreds of steps, debugging which step broke is a top engineering pain. SAFARI shifts fault attribution from engineer-hours to inference spend — essential agent-ops tooling for runs that exceed context windows.

### ToolPro: Tool Programs as an Interface for Flexible Agentic Web Services

[arXiv:2606.19992](http://arxiv.org/abs/2606.19992v1)

ToolPro represents an agent's tool intent as executable "tool programs" that compactly encode multi-step web service interactions with loops, conditionals, joins, and retries — replacing the current pattern of repeated single API calls.

**Why it matters:** Directly addresses a core agent architecture pain point: orchestrating multi-step tool workflows. Moving from static endpoint calls to executable tool programs could fundamentally change how agents interact with web services and APIs.

### Why Multi-Step Tool-Use RL Collapses — and How Supervisory Signals Fix It

[arXiv:2606.26027](http://arxiv.org/abs/2606.26027v1) · open-sourced as **Tool-RL-Box** (built on verl-tool and veRL)

Shows that multi-step tool-use reinforcement learning catastrophically collapses mid-training, and introduces supervisory (process-level) signals that stabilize learning and prevent collapse.

**Why it matters:** Training tool-using agents with RL is unstable — a core pain point. The supervisory-signal fix is actionable for anyone fine-tuning function-calling models. Code is released.

### More Papers Worth Tracking

- **[Agent Alpha](https://www.semanticscholar.org/paper/3d53bb6020cf87a11d34176517acfb3c5265b53c)** — tree search for GUI agents that unifies generation, exploration, and evaluation; enables regressive search to reuse partial successes and recover from early errors. Test-time compute scaling for agents.
- **[Beyond Function Calling / ToolBench-X](http://arxiv.org/abs/2606.25819v1)** — benchmarks tool-using agents under unreliable tool environments (broken tools, latency, adversarial responses) rather than clean, stable assumptions. Closes the lab-to-deployment gap.
- **[Automating SKILL.md Generation](http://arxiv.org/abs/2606.20363v1)** — a three-stage pipeline that mines skill libraries from GUI interaction trajectories, proving skills can be auto-generated rather than hand-authored. Validates the skill-library approach and signals convergence on the SKILL.md standard.
- **[Uncertainty Quantification for CUAs](http://arxiv.org/abs/2606.25760v1)** — benchmarks post-hoc UQ for computer-use agents across VLMs and GUI datasets, covering rejection, calibration, and spatial safety regions for clicks. Knowing when *not* to click matters as much as knowing where.
- **[Grading the Grader](http://arxiv.org/abs/2606.24839v1)** — methodology for evaluating agentic data analysis systems, distinguishing genuine disagreement from grader errors in LLM-based evaluation. Practical for anyone building agent eval pipelines with LLM judges.

---

## Notable Mentions

- **[OpenAI Codex agent improvement loop](https://developers.openai.com/codex)** — frames the traces → evals → refine loop as the canonical way to improve coding agents; Codex now on Plus/Pro/Business/Enterprise plans.
- **OpenAI Partner Network** — three delivery tiers, $150M ecosystem investment, and a Codex specialization track; signals OpenAI's enterprise agent-delivery bet.
- **[Global AGENTS file](https://x.com/linuz90/status/2021534838466175225)** — symlink a global AGENTS file to `~/CLAUDE.md` and `~/AGENTS.md` to keep behavior consistent across Claude Code, Codex, Gemini, and Cursor.
- **[Background agents will win](https://x.com/johnlindquist/status/1935714164028084719)** — container-per-task PR agents (Cursor/Codex) already handle most tasks; the unsolved layer is multi-agent PR/merge UX.
- **[GPT-5.5, GPT-5.4, Codex on Bedrock](https://x.com/awscloud/status/2061564484523524302)** — frontier models plus Codex now GA on Amazon Bedrock with automatic scaling.
- **[MCP as the cross-vendor connector layer](https://x.com/lenadroid/status/2064364987326550065)** — Codex and Claude Code both speak MCP, so connectors written for one usually work in the other; plugins bundle connectors and behaviors.
- **[Multi-agent orchestration layer over Claude Code](https://x.com/hasantoxr/status/2037963932204445836)** — 5 execution modes, 32 specialized agents, claims 3–5× faster output.
- **[The Claude Architect certification blueprint](https://x.com/sharyph_/status/2037393353478959336)** — weights Agentic Architecture (27%): stop_reason-driven loops, isolated subagent context, programmatic hooks; and Tool Design & MCP (18%) with 4–5 scoped tools per agent. Encodes consensus best practices into a formal curriculum.
- **[Subagent protocol in /agents](https://x.com/sethlazar/status/2006214936603844668)** — multi-agent orchestration with Claude Code via a subagent protocol that gives each agent the context it needs for its subtask.
- **[Visa gives AI agents card credentials](https://x.com/sytaylor/status/2064957014879420456)** — partnering with OpenAI for agentic commerce; the infrastructure prerequisite for agents that can autonomously transact.
- **[Fat skills, fat code, thin harness](https://x.com/garrytan/status/2045233931390484967)** — Garry Tan (YC) shares Steve Yegge's framing: push complexity into skills and generated code, keep the harness minimal. People using AI coding agents are "10× to 100×."
- **[Build your first AI loop for yourself](https://x.com/cathrynlavery/article/2069193102586474781)** — observe real tool calls, shell commands, failures, skill use, and correction moments to improve agent design. Template included.
- **[Loops: the quiet skill behind every AI system that scales](https://x.com/cyrilXBT/article/2068850474384609543)** — prompts are the wrong unit of measurement; loops are the key scaling unit, and the loop's memory persists across runs.
- **[Clint Gibler joins OpenAI Cyber](https://x.com/clintgibler/status/2064813665711444175)** — signals OpenAI investing in agentic security infrastructure (ties to the skills-as-supply-chain theme).
- **[Managing the agent loop is the costliest thing](https://x.com/HadrianVeidt0)** — reframes the cost model of AI engineering from code generation to loop management.
- **[The question is no longer which model to call](https://x.com/titus_k)** — for 2026 agent builders, the key question is how to architect the system around the model.
- **[Loops: what every AI engineer needs to know in 2026](https://x.com/sairahul1/article/2064277888216555684)** — agents loop inside a framework you build, and memory ensures the loop never forgets between runs.
- **[Beyond Static Leaderboards: Predictive Validity](http://arxiv.org/abs/2606.19704v1)** — aggregates 14 parallel studies of one MCP-based industrial-agent benchmark; no single benchmark touches more than 4–5 deployment dimensions.
- **[The Art of Building Verifiers for CUAs](https://www.semanticscholar.org/paper/bba585a56bf7c9a861de68165a30c57c3f5b9388)** — a "Universal Verifier" for web tasks; verification is the foundation of agent training and eval (Corby Rosset, Microsoft Research).
- **[CLI-Anything: Agent-Native Computer Use](https://www.semanticscholar.org/paper/59b5ad8c186160eb04660b435468e6b1eabb6060)** — argues CLI-based interaction is more reliable, faster, and cheaper than GUI-first approaches for agents; the CUA field may be over-indexing on visual methods.
- **[IntentCUA: Intent-Level Skill Abstraction](https://www.semanticscholar.org/paper/ac9625ec0bda72b4c2185e57f0dfbcb0d647c8bc)** — learning intent-level representations that enable skill abstraction and multi-agent planning, reducing error accumulation over long horizons.
- **[Model-Adaptive Multi-Agent RAG](http://arxiv.org/abs/2606.25191v1)** — training-free interventions on 7B–9B models make multi-agent RAG cost-efficient via isolate-vs-score strategies.
- **[Privacy-Preserving Multi-Agent RAG](http://arxiv.org/abs/2606.24623v1)** — a multi-agent framework sanitizes retrieved RAG content through semantic rewriting without sacrificing contextual fidelity.
- **[Fara-1.5: Scalable Learning Environments for CUAs](http://arxiv.org/abs/2606.20785v1)** — scalable environment generation plus automated verifiers for training computer-use agents (Ahmed Awadallah, Microsoft Research).
- **[The Blind Spot of Agent Safety](https://www.semanticscholar.org/paper/1849c0ea146198831e17f1b3ccde57493b980442)** — benign user instructions can cause CUAs to execute harmful actions when context is misinterpreted; expands agent safety beyond prompt injection.
- **[Quantization inflates reasoning tokens](http://arxiv.org/abs/2606.25519v1)** — low-bit quantization of reasoning models inflates token count, negating per-token savings; measure total tokens, not just per-token latency.
- **[Decoupling reconnaissance and exploitation in LLM pen-testing](http://arxiv.org/abs/2606.25332v1)** — end-to-end black-box evals mask true capability; stage-decoupled evaluation reveals where agents actually fail.
- **[Power Systems Agent Benchmark](http://arxiv.org/abs/2606.20950v1)** — executable agent evaluation in electric power engineering; the "check consequences of actions" paradigm spreading beyond software.
- **[MedGuards: Multi-Agent Medical Error Detection](http://arxiv.org/abs/2606.25651v1)** — a multi-agent verifier/corrector pattern for reliable output validation, generalizable beyond healthcare.
- **[Agentic evolution of physically constrained foundation models](http://arxiv.org/abs/2606.25532v1)** — a multi-agent discovery engine autonomously architects hardware-compatible designs.
- **[BrainAgent: Multi-Agent Brain Signal Understanding](http://arxiv.org/abs/2606.25400v1)** — a multi-agent LLM framework for autonomous brain-signal analysis in BCI applications.
- **[When Helpfulness Overrides Causal Caution](http://arxiv.org/abs/2606.24839v1)** — context-dependent suppression and recovery of causal reasoning in LLMs.

---

## Chinese Community / 知乎中文社区

From Chinese-language sources (collected via `site:zhihu.com` search):

- **[大模型智能体(Agent)全解析：原理、架构、框架与实操指南](https://zhuanlan.zhihu.com/p/1999522578218390118)** — A systematic walkthrough of agent core concepts, the PEAS model, the agent loop, and prompt engineering, with a horizontal comparison of the three mainstream architectures: ReAct, Plan-and-Solve, and Reflection.
- **[2026年AI Agent技术全景：12大主流框架深度解析与架构演进趋势](https://zhuanlan.zhihu.com/p/2026254728342905724)** — A framework survey covering 12 mainstream agent frameworks, core architecture, framework selection, and evolution trends. Frames 2026 as the "Year of the Agent" now that models (GPT-4o, Claude 3.5) have matured enough in reasoning and tool calling.
- **[一文讲清！AI智能体到底是什么？](https://zhuanlan.zhihu.com/p/1961862896981094827)** — A concept explainer clarifying whether AI agents are just "reskinned chatbots" and why they're called the next-generation AI paradigm.
- **[Agent架构设计全解析：9大核心技术](https://zhuanlan.zhihu.com/p/1935734877472420257)** — Systems-oriented architecture design for enterprise-grade agents: "the LLM is the engine; the agent architecture is the chassis and driving system that decides how far AI can go."
- **[AI Agent开发路线图2025](https://zhuanlan.zhihu.com/p/1985379253525697644)** — A learning roadmap including the L3 stage on multi-agent systems (LangChain, LlamaIndex, AutoGPT, MetaGPT).

---

*Collected via X keyword/company search and web search (arXiv, Semantic Scholar, Zhihu). Pipeline: collect → prefilter → analyze (5 chunks) → merge → synthesize.*
