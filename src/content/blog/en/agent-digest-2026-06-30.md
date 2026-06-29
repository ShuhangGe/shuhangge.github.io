---
title: "Agent Architecture Daily Digest — June 30, 2026"
description: "The agent stack converges on three primitives — Loops, Skills, and Memory — as Anthropic's leaked Loop Engineering playbook formalizes generator/evaluator separation, Microsoft's SkillOpt treats skills as trainable objects, and a burst of cross-agent memory layers (memorix, second-brain, agentmemory) land on MCP. Claude Code subagents go background-by-default, RepoPrompt open-sources an MCP-as-controller flip, GLM-5.2 + SKILL.md beats Opus 4.8, and Karpathy's LLM Wiki reframes knowledge from retrieval to compilation."
pubDate: "2026-06-30"
lang: en
tags: ["Agent", "LLM", "AI Architecture", "Daily Digest"]
---

## TL;DR — Today's Overview

> Top 10 things to know today:

1. **Anthropic's Loop Engineering playbook leaked — and it's now doctrine.** The internal guide (now an O'Reilly piece) formalizes the agentic loop as a layer *above* harness engineering. Boris Cherny, head of Claude Code: *"I don't prompt Claude anymore. I have loops running that prompt Claude... My job is to write loops."* Five moves (discovery, handoff, verification, persistence, scheduling), six parts (automations, worktrees, skills, connectors, sub-agents, memory), and one hard rule: never let the generator grade its own work. — [@milesdeutscher](https://x.com/milesdeutscher/status/2071528356152303770), [O'Reilly Radar](https://www.oreilly.com/radar/loop-engineering/)

2. **Claude Code subagents will run in the background by default.** Boris Cherny confirmed the next Claude Code release keeps your conversation live while subagents work asynchronously — just tell Claude if you want an agent in the foreground. Combined with the "ultracode" mode that spawns ~1,000 subagents, Claude Code is becoming a parallel orchestration runtime, not a chat box. — [@bcherny](https://x.com/bcherny/status/2071647677591466098)

3. **Microsoft's SkillOpt treats agent skills as trainable objects.** The text-space optimizer trains a natural-language `best_skill.md` like a neural network — epochs, (mini-)batches, learning rates, and held-out validation gates — without touching model weights. Trajectory-driven edits, reflection, and bounded updates. The implication: skills accumulate domain knowledge *outside* the model, and frozen agents get better without retraining. — [microsoft/SkillOpt](https://github.com/microsoft/SkillOpt), [@nash_su](https://x.com/nash_su/status/2071480871794999552)

4. **RepoPrompt open-sources an architecture flip: the MCP server becomes the controller.** RepoPrompt CE inverts its design — instead of the app driving agents, a built-in MCP server orchestrates while CLI tools (Claude Code, Codex, OpenCode, Gemini CLI) become swappable execution layers. One planner model decomposes; sub-tasks fan out to parallel agents that each see only their slice. Context engineering, now formally named. — [@dotey](https://x.com/dotey/status/2071348102263378085), [repoprompt-ce](https://github.com/repoprompt/repoprompt-ce)

5. **Karpathy's LLM Wiki reframes knowledge from retrieval to compilation.** The viral document argues you should compile knowledge *once* into interconnected Markdown and keep it current — instead of asking the LLM the same questions or running RAG every time. The human owns the spec; the model owns execution and bookkeeping. "Prompts are easy. Loops are hard." — [@hanakoxbt](https://x.com/hanakoxbt/status/2071327017161617843), [@polydao](https://x.com/polydao/status/2071105441698910415)

6. **GLM-5.2 + SKILL.md beats Opus 4.8, GPT-5.4, and Gemini 3.5 Flash in practice.** Zhipu/Z.ai's open-weight flagship (753B-param MoE, MIT, 1M-token context) scores 81.0 on Terminal-Bench 2.1 and 62.1 on SWE-bench Pro. Builders report that forcing GLM-5.2 to use skills unlocks a "massive upgrade" — and you can use Codex to *write* the skills for GLM. Skills are the great equalizer across open and closed models. — [@cjzafir](https://x.com/cjzafir/status/2071225619803750560), [zai-org/GLM-5](https://github.com/zai-org/GLM-5)

7. **A memory-layer gold rush landed on MCP.** At least four cross-agent memory projects shipped in weeks: **memorix** (one memory for 11 coding agents via MCP), **second-brain-cloudflare** (Cloudflare D1/Vectorize/KV, free-tier personal), **agentmemory** (iii engine, Hermes/Codex/Cursor), and **open-second-brain** (Hermes + nightly "dream passes" that turn repeat corrections into confirmed preferences). The pattern: store context once, recall it in any tool. — [@geekbb](https://x.com/geekbb/status/2071222081358799250), [memorix](https://github.com/AVIDS2/memorix)

8. **The full Chinese-model stack is 87% cheaper at ~4% quality cost.** One operator's 30-day swap report: Opus→Kimi K2.7 (reasoning), GPT-5.5→Qwen 3.7 Max (code), Sonnet→GLM 5.2 (agent loops), all routable, all self-hostable. DeepSeek also open-sourced **DSpark**, a portable inference-efficiency method described as a lossless boost. The frontier-per-dollar advantage is real and compounding. — [@DeRonin_](https://x.com/DeRonin_/status/2071561335234531578), [@manateelazycat](https://x.com/manateelazycat/status/2071247379580530854)

9. **Cognition's Scott Wu: software's entry point moves from writing code to commanding computers.** The Devin founder frames agents as a new human-computer interface — you state the goal, the agent generates the execution path. The PR-review workflow is "generation 1 of at least 10"; real innovation is ahead in task expression, context understanding, verification, and delivery. One-off software becomes economically viable. — [@teach_fireworks](https://x.com/teach_fireworks/status/2071618602923610179)

10. **The counter-current: "I stopped Claude Max."** A high-engagement post (528 likes, 142 replies) reflects burnout on vibe coding — "produced a pile of junk, got shallower, stopped thinking." A useful reminder that loop-first architecture only pays off when paired with the verification discipline the Anthropic playbook demands. — [@PandaTalk8](https://x.com/PandaTalk8/status/2071407431146680664)

📊 Today's Numbers: **136 X "For You" items collected | 44 relevant candidates after prefilter | ~30 detailed items | 30+ notable mentions | 6+ papers/research** — full X collection resumed (no AUTH_REQUIRED today).

---

## The Pattern: The Stack Is Three Primitives — Loops, Skills, Memory

Three forces converged this cycle, and the X discourse was unusually coherent about all of them.

**Force one: the loop becomes the unit of work.** The single most-circulated artifact was the Anthropic *Loop Engineering* playbook — leaked, then formalized as an O'Reilly piece. Its thesis reframes everything: the job is no longer to write prompts but to write loops that prompt the model. Boris Cherny's quote is the headline, but the substance is the taxonomy. A loop turn decomposes into five moves — *discovery, handoff, verification, persistence, scheduling* — and the system is built from six parts: automations (the timer), worktrees (safe parallelism), skills (permanent knowledge), connectors (GitHub/Linear/etc.), sub-agents (generator + evaluator), and memory (state that survives between runs). The non-negotiable rule — separate the generator from a *skeptical* evaluator, because agents systematically overpraise their own output — is the engineering version of "write a verifier," now with empirical backing. When Claude Code simultaneously announces subagents running *in the background by default*, the loop-first model stops being a blog post and becomes the product's default interaction. The implication for builders: your value moves up one layer, from writing the prompt to designing the control system around it.

**Force two: skills become trainable objects.** Microsoft's **SkillOpt** is the most architecturally significant release of the cycle. It treats a compact natural-language skill document as the *trainable state of a frozen agent*, then learns that document through rollouts, reflection, bounded edits, and held-out validation gates — importing the vocabulary of neural-network training (epochs, mini-batches, learning rates) into text space, without touching a single weight. This matters because it gives open-weight and frozen models a path to improve *outside* retraining: the domain knowledge lives in the skill, and the skill gets optimized. The @cjzafir field report — "GLM 5.2 + SKILL.md > Opus 4.8, GPT 5.4, Gemini 3.5 Flash," with Codex used to *author* the skills — is the proof: a well-scaffolded open model with trained skills can out-pace a frontier model prompting cold. This dovetails with Karpathy's LLM Wiki reframing (knowledge compiled once, not retrieved every turn) and with RepoPrompt's architecture flip, where an MCP server orchestrates and CLI agents become swappable executors. The throughline is identical: **stop managing prompts; start managing durable artifacts** (skills, wikis, memory files) that compound.

**Force three: memory consolidates on MCP.** Four cross-agent memory layers shipped within weeks of each other, and they all converged on the same design: store context once, expose it through MCP, recall it from any client. **memorix** covers 11 coding agents; **second-brain-cloudflare** self-hosts on Cloudflare's free tier with D1/Vectorize/KV; **agentmemory** runs an "iii engine" across Hermes, Codex, Cursor, and Copilot; **open-second-brain** adds nightly "dream passes" that promote repeated corrections into confident preferences. The reason this is a trend and not a novelty: as agents run in loops with background subagents, *state that survives between runs* becomes the difference between an agent that learns and one that restarts from zero every turn. Memory is the persistence primitive the loop taxonomy requires.

Put together, the message to agent builders is sharp: the frontier isn't a bigger model — it's the three durable layers (loop control, trained skills, persistent memory) you wrap around whatever model happens to be cheapest-per-token this week.

---

## X/Twitter Highlights

### Anthropic / Claude Code — Loop Engineering Goes Live

[**@bcherny**](https://x.com/bcherny/status/2071647677591466098) (Boris Cherny, head of Claude Code) confirmed the next Claude Code release makes **subagents run in the background by default**, so you keep talking to Claude while subagents work — foreground execution is opt-in by request. This is the productization of the loop-engineering thesis: long-running work is delegated and isolated, and the main thread stays interactive. Combined with the "ultracode" mode reported to spawn ~1,000 subagents, Claude Code is being rebuilt as a layered agentic runtime (memory, hooks, skills, subagents, plugins, MCP as distinct layers) rather than a single conversation.

[**@milesdeutscher**](https://x.com/milesdeutscher/status/2071528356152303770) surfaced what he calls Anthropic's leaked internal **loop engineering playbook** — "the most valuable AI guide I've read all year." His compression of it is the clearest public summary: structure every loop around Discovery → Handoff → Verification → Persistence → Scheduling; **always separate Generator from a skeptical Evaluator** (the evaluator must *act* — run tests, click buttons, take screenshots); build from six parts (automations, worktrees, skills, connectors, sub-agents, memory); and watch for four failure modes — verification debt, losing understanding of your own codebase, exploding token costs, and cognitive surrender. The token-cost section is notably practical, since loop engineering at scale is expensive.

[**@wangray**](https://x.com/wangray/status/2071531957360746768) (Anthropic partner, Upthos founder) continued the **Claude Architect certification** prep series with a deep dive on **Hooks** — framed as "the agent's security checkpoint," intercepting tool calls and normalizing data before execution. The framing matters: hooks are the policy-enforcement layer that makes autonomous loops safe to schedule while you sleep. If verification is the loop's immune system, hooks are its perimeter.

### Karpathy's LLM Wiki & Context Engineering

[**@hanakoxbt**](https://x.com/hanakoxbt/status/2071327017161617843) crystallized the week's mood: **"KARPATHY JUST KILLED THE PROMPT ERA WITH A SINGLE DOCUMENT."** The argument: prompts are easy, loops are hard, and writing fifty prompts a day is work nobody does twice. Karpathy's method shifts the burden to the harness — define the contract once, the model writes/reviews/restarts/reconciles, you keep judgment, it keeps the loop. "The human owns the spec and the boundary. The model owns the execution and the bookkeeping."

[**@polydao**](https://x.com/polydao/status/2071105441698910415) described the practical payoff: the LLM Wiki pattern turns the AI into a **full-time maintainer for your Zettelkasten** — it reads every new source and integrates it into a structured wiki, finds contradictions across notes, and the vault compounds without you typing a single link. Obsidian becomes the visual IDE; Claude is the backend.

[**@dotey**](https://x.com/dotey/status/2071348102263378085) delivered the most technically detailed post of the cycle on **RepoPrompt's open-source release (CE)**. The key architectural reversal: RepoPrompt no longer lets the app schedule agents — instead its **built-in MCP server becomes the controller**, and the CLI tools (Claude Code, Codex, OpenCode, Gemini CLI) become a swappable execution layer. One reasoning model plans and decomposes; sub-tasks fan out to parallel agents that each see only their files. The motivation is *context engineering* — dumping a whole repo degrades models past ~32K tokens, so you curate exactly what each agent needs. (Background: OpenAI's Romain Huet recruited RepoPrompt's author; he open-sourced only after ensuring paid users were taken care of.) Install: `brew install --cask repoprompt-ce`.

### Skills as Trainable Objects

[**@nash_su**](https://x.com/nash_su/status/2071480871794999552) flagged **Microsoft SkillOpt** with the right instinct: "use the model-training approach to optimize a skill." Each iteration adjusts the skill's Markdown, runs the result, and advances if it improves — epochs for a `.md` file. SkillOpt's own framing: train agent skills *like neural networks*, with validation gates, but without touching weights. The `best_skill.md` artifact is deployable and shareable.

[**@cjzafir**](https://x.com/cjzafir/status/2071225619803750560) offered the sharpest field report: **"GLM 5.2 + SKILL.md > Opus 4.8, GPT 5.4, Gemini 3.5 Flash."** Force a frozen model to use well-authored skills and you get a "massive upgrade" — and crucially, *you can use Codex to write the skills for GLM 5.2*. The takeaway: skill authoring is composable and model-agnostic, which is exactly what makes skills the great equalizer between open and closed models.

### The Memory Layer Gold Rush

[**@geekbb**](https://x.com/geekbb/status/2071222081358799250) highlighted **second-brain-cloudflare** — one memory layer for Claude, ChatGPT, Cursor, and Codex, self-hosted on Cloudflare Workers with memory in your own D1, Vectorize, KV, and Workers AI, exposed via MCP and built for *semantic* retrieval over literal matching. Personal scale fits Cloudflare's free tier. The broader category is the story: **memorix** ([GitHub](https://github.com/AVIDS2/memorix)) unifies memory across 11 coding agents (Cursor, Claude Code, Codex, Windsurf, Gemini CLI, Copilot, Kiro, OpenCode, Antigravity, Trae, Pi) via MCP; **agentmemory** ([GitHub](https://github.com/rohitg00/agentmemory)) runs an "iii engine" benchmarked for Hermes, OpenClaw, Codex, and more; and **open-second-brain** adds nightly *dream passes* that convert repeated corrections into confident preferences. The convergence on "store once, recall anywhere, over MCP" is unmistakable.

### The Chinese Open-Weight Stack & Cost Math

[**@DeRonin_**](https://x.com/DeRonin_/status/2071561335234531578) published a detailed 30-day stack-swap result that went viral: **operating costs dropped 87%, output quality dropped ~4% on average, revenue unchanged.** The routing: Opus 4.8 → Kimi K2.7 (reasoning, ~11× cheaper), GPT-5.5 → Qwen 3.7 Max (code, ~7× cheaper), Sonnet → GLM 5.2 (agent loops, ~5× cheaper on input), GPT-5.5 mini → MiMo V2.5 (bulk, ~12× cheaper), GPT-Image-2 → Wan 2.5, Sora 2 → Kling 3.0. The two stated advantages: models won't be banned mid-month, and data stays local.

[**@manateelazycat**](https://x.com/manateelazycat/status/2071247379580530854) (former Deepin CTO) noted that **DeepSeek DSpark**, open-sourced by 梁圣 (Liang Sheng), is a **portable, lossless inference-efficiency boost** transferable to other large models — and wondered aloud whether Anthropic and OpenAI would quietly "copy the homework."

### Agent Products & Tooling

[**@akshay_pachaar**](https://x.com/akshay_pachaar/status/2071509401224261823) showed **Google's Agents CLI** operationalizing Karpathy's "Agentic Engineering" — one setup command injects 7 ADK-specific skills into a coding agent, covering scaffolding, evals, deployment, and enterprise registration end-to-end. His demo: scaffolded a RAG agent from the `agentic_rag` template, generated 20 eval scenarios with LLM-as-judge scoring, deployed to Agent Runtime, and registered to Gemini Enterprise — all from Claude Code. The eval scorecard caught an instruction loophole before deployment.

[**@zjp1997720**](https://x.com/zjp1997720/status/2071129331699745263) recommended **Lazy Codex** (from the oh-my-openagent project) — a plugin that makes Codex *proactively* and *planned-ly* invoke an Agents Team for complex tasks (something Claude Code does by default but Codex doesn't), with a solid context-sharing mechanism. Useful beyond coding — the demo is a research task.

[**@indie_maker_fox**](https://x.com/indie_maker_fox/status/2071099277066272900) made a product-philosophy claim worth pinning: **"in the future, programmers won't mainly 'do requirements' — they'll 'make agents.'"** Agents distill your capability: state the need, delegate the rest. He recommends starting from **Pi agent** (open-source coding agent, à la Claude Code) or **Craft agent** (à la Claude Cowork, built on Pi + Claude Agent SDK) rather than from zero — and reports shipping a product ("Echo," with a skill marketplace) on top of them.

[**@teach_fireworks**](https://x.com/teach_fireworks/status/2071618602923610179) shared **Cognition/Scott Wu interview notes**: Devin isn't framed as a coding agent but as a new human-computer interface — you say the goal, the agent decides the code and scripts. One-off software (scan 15 LinkedIn profiles, fill forms, build an Excel analysis) becomes economically viable because the execution path is generated per-need. Wu sees the PR-review workflow as generation 1 of 10+, with real innovation ahead in task expression, context, verification, and delivery.

[**@Yangtze_Seventh**](https://x.com/Yangtze_Seventh/status/2071564088228888769) previewed **Raven Agent** — an agent designed around *you* as the core, iterating your own architecture and capability over time, eventually representing you and conversing with other personal agents. The framing: agent as a partner carrying your "clone memory," not a tool. Whether the product lands, the *personal-agent-that-compounds* concept is in the air.

[**@Easycompany333**](https://x.com/Easycompany333/status/2071204465168769451) pointed to **Google's 8-minute "minimal agent loop" walkthrough** as the fastest path to actually understanding loops — with the honest caveat that *understanding* a loop is easy but *using* one well is hard, just like skills. "Practice is the only test of truth."

### The Counter-Current: Vibe-Coding Burnout

[**@PandaTalk8**](https://x.com/PandaTalk8/status/2071407431146680664) posted the cycle's most-replied counterpoint (528 likes, 142 replies): **"I cancelled Claude Max."** Months of vibe coding "produced a pile of junk, made me shallower, floated my thinking on the surface." The return to reading great works and deep focus felt grounding. It's a useful corrective — and it maps directly onto the Anthropic playbook's "cognitive surrender" warning. Loop-first architecture only pays if the verification discipline is real.

### Rising Stars & Builders

[**@better_christal**](https://x.com/better_christal/status/2071247379580530854) showed **n8n + Modbus nodes reading PLC devices** in manufacturing — temperature/pressure/vibration → threshold alerts → automated workflows. A concrete agentic-automation case far from the coding-agent echo chamber, and a reminder that "tool use" includes industrial protocols.

[**@Lonely__MH**](https://x.com/Lonely__MH/status/2071423674683711776) shared a method to **query Codex's granted credit/reset expiry** via a prompt that reads `~/.codex/auth.json` and summarizes `available_count`/`expires_at` without leaking tokens — and flagged the **Hermes Bible**, a community tool for navigating Hermes Agent's fast iteration. Both signal a maturing builder ecosystem around agent platforms.

[**@Saccc_c**](https://x.com/Saccc_c/status/2071407431146680664) built a personal website with Codex (332 likes, 111 replies) and argued "in the AI era a personal site is the best resume" — a small but representative example of one-off software generated per-need, exactly the Cognition thesis from the other direction.

---

## Notable Mentions

- **@AlchainHust**: Both Claude Fable 5 and GPT-5.6 are required to pass White House review before release — and GPT-5.6's review came far later, narrowing its rollout window. The government-gating trend continues.
- **@canghe / @mylifcc / @dotey**: GPT-5.6 Sol is reportedly in **gray rollout** — the "Juice test" (gpt-5.5 on xhigh) returns 128 if you're routed to 5.6-Sol, 768 otherwise. Community probing model routing in real time.
- **@_avichawla**: Excellent explainer on **prefill vs. decode** in LLM inference — why the first token lags (compute-bound, parallel Q/K/V) while subsequent tokens stream (memory-bound). Foundational for understanding agent latency budgeting.
- **@Jolyne_AI**: Open-source **reinforcement learning textbook** recommendation — the practical middle ground between concept-only and formula-heavy materials.
- **@fiapp_pro**: Endorses a Codex plugin as "the most satisfying in half a year of Codex use" — context-sharing across an Agents Team.
- **@cjzafir** (thread): Uses **Codex to author skills for GLM 5.2** — skill authoring as a cross-model, composable workflow.
- **@PandaTalk8**: The vibe-coding-burnout thread (see Counter-Current) generated 142 replies of debate on whether agents make you shallower.
- **@geekbb**: second-brain-cloudflare (see Memory Layer section) — semantic retrieval over MCP on Cloudflare free tier.
- **MCP 2026-07-28 RC** continues to approach: **stateless protocol core** + Extensions framework. Already tracked June 29; remains the protocol-layer backdrop for every memory/skill project above.
- **Claude Architect certification** (@wangray): Hooks as the agent's security checkpoint — part 4 of the prep series.
- **@manateelazycat**: DeepSeek DSpark open-sourced — lossless, portable inference efficiency.
- **@DeRonin_**: Full Chinese-stack cost math (87% cheaper) — routing table by task type.
- **@indie_maker_fox**: Pi agent + Craft agent as open-source starting points for building coding/cowork agents.
- **@teach_fireworks**: Devin PR-workflow is "generation 1 of 10+."
- **@nash_su**: SkillOpt = train skills like models, via .md edits + validation gates.
- **@akshay_pachaar**: Google Agents CLI operationalizes the full agentic-engineering lifecycle end-to-end.
- **open-second-brain**: Hermes Agent memory with nightly "dream passes" — repeat corrections → confirmed preferences.
- **memorix**: Cross-agent memory for 11 coding agents via a single MCP server.
- **@Yangtze_Seventh**: Raven Agent — personal agent built around *you*, compounding over time.
- **@Lonely__MH**: Codex credit-expiry query method + Hermes Bible community tool.
- **@Saccc_c**: Personal website as resume, built with Codex.
- **@better_christal**: n8n + Modbus for industrial PLC automation.
- **@Easycompany333**: Google's minimal agent-loop video — fastest loop onboarding.
- **@PandaTalk8**: Claude Max cancellation — the cognitive-surrender warning in practice.
- **@zjp1997720**: Lazy Codex plugin makes Codex proactively use Agents Team.
- **@dotey**: RepoPrompt CE architecture flip — MCP server as controller.
- **@hanakoxbt / @polydao**: Karpathy LLM Wiki — compile once, don't retrieve every time.
- **@milesdeutscher**: Loop Engineering 5 moves, 6 parts, generator≠evaluator.
- **@bcherny**: Claude Code background subagents by default.

---

## Papers & Research

1. **Loop Engineering: The Anthropic Playbook** ([HyperAI/IEEE](https://hyper.ai/en/papers/Loop-Engineering-IEEE)) — Defines loop engineering as a fourth layer above harness engineering. Decomposes a loop turn into five moves and six parts, and introduces generator/evaluator separation with empirical evidence that agents overpraise their own output and that an independently tuned skeptical evaluator is far more tractable than a self-critical generator. The architectural centerpiece of this cycle.

2. **SkillOpt: Text-Space Optimization for Self-Evolving Agent Skills** ([microsoft/SkillOpt](https://github.com/microsoft/SkillOpt), [PyPI](https://pypi.org/project/skillopt/)) — Treats a natural-language skill document as the trainable state of a frozen agent; learns it via rollouts, reflection, bounded edits, and held-out validation gates. Imports epoch/batch/learning-rate vocabulary into text space without touching weights. 6.4K stars.

3. **GLM-5: From Vibe Coding to Agentic Coding** ([zai-org/GLM-5](https://github.com/zai-org/GLM-5), [openlm.ai](https://openlm.ai/glm-5.2/)) — Zhipu/Z.ai's open-weight flagship: ~753B-param MoE under MIT, 1M-token context, improved MTP layer for speculative decoding (+20% acceptance length). **Terminal-Bench 2.1: 81.0** (strongest open-source), **SWE-bench Pro: 62.1** — built for long-horizon coding agents, closing the gap with frontier closed models.

4. **Hidden Anchors in Multi-Agent LLM Deliberation** ([arXiv:2606.19494](https://arxiv.org/abs/2606.19494)) — Submitted June 17, 2026; 13 pages. Analyzes anchoring biases that distort multi-agent LLM deliberation — relevant to anyone designing generator/evaluator or multi-agent debate systems.

5. **Differentiable Mixture-of-Agents (MoA)** ([arXiv, May 2026](https://24-ai.news/en/news/2026-05-18/arxiv-differentiable-mixture-of-agents-swarm/)) — Introduces a differentiable routing mechanism for multi-agent LLM collaboration, reaching SOTA on 9 benchmarks. A step toward *learnable* agent orchestration rather than hand-wired graphs.

6. **Physics-Grounded Multi-Agent Architecture for Traceable Reasoning** ([arXiv:2605.04003](https://arxiv.org/html/2605.04003)) — Finds single-LLM baselines stay competitive on single-tool-call queries but degrade on dependent tool chains — empirical evidence for when multi-agent orchestration actually pays off.

---

## Chinese Community / 中文社区

本周中文 X 生态信号极强，几乎主导了架构讨论：

- **Karpathy LLM Wiki / RepoPrompt 开源**（[@dotey](https://x.com/dotey/status/2071348102263378085)）：RepoPrompt 社区版上线，架构做了反转——内置 MCP server 成为主控，Claude Code / Codex / OpenCode / Gemini CLI 变成可替换的执行层。上下文工程正式命名：超过 32K token 的 prompt 会让模型变笨，需精挑细选。
- **SkillOpt 训练 skill**（[@nash_su](https://x.com/nash_su/status/2071480871794999552)）：用训练模型的思路优化 skill 的 MD 内容，跑结果、好了就推进。
- **GLM 5.2 + SKILL.md 实战**（[@cjzafir](https://x.com/cjzafir/status/2071225619803750560)）：用 Codex 给 GLM 5.2 写 skill，效果超过 Opus 4.8 / GPT 5.4 / Gemini 3.5 Flash。
- **Agent Loop 入门**（[@Easycompany333](https://x.com/Easycompany333/status/2071204465168769451)）：Google 8 分钟最小 Agent loop 教程，理解容易用好难。
- **制造业 Agent 落地**（[@better_christal](https://x.com/better_christal/status/2071247379580530854)）：n8n 装 Modbus 节点接 PLC，读温度/压力/振动做阈值告警——工具调用不止是代码。
- **个人 Agent**（[@Yangtze_Seventh](https://x.com/Yangtze_Seventh/status/2071564088228888769)）：Raven Agent 以「你自己」为核心，不断迭代你的架构与能力。
- **Devin 访谈**（[@teach_fireworks](https://x.com/teach_fireworks/status/2071618602923610179)）：下一代软件入口从「写程序」走向「指挥电脑」，PR 工作流只是第一代产品形态。
- **Vibe Coding 反思**（[@PandaTalk8](https://x.com/PandaTalk8/status/2071407431146680664)）：停掉 Claude Max，反思为消耗 token 而 vibe coding 产出垃圾、认知浮于表面。

---

*Collected 2026-06-30, 02:00 CST. X "For You" feed via opencli (136 items). Web/arXiv via search. Official X API not configured — X posting skipped. Compiled with the research-digest pipeline. Questions or corrections? Reply on [X](https://x.com/shuhangge).*
