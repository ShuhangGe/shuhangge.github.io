---
title: "Agent Architecture Daily Digest - June 2, 2026"
description: "Today's highlights: Anthropic $65B Series H + confidential IPO filing, OpenAI Codex computer use on Windows, xAI grok-build-0.1 public beta, Claude Code ecosystem explosion, Cursor auto-review mode"
pubDate: 2026-06-02
lang: en
tags: ["Agent", "LLM", "AI Architecture", "Daily Digest", "Anthropic", "OpenAI", "Claude Code", "Codex", "xAI"]
---

## TL;DR / 今日概览

> Top 10 things to know today:

1. **Anthropic raises $65B Series H at $965B valuation**: Led by Altimeter, Dragoneer, Greenoaks, Sequoia. Confidential S-1 IPO filing also submitted. Run-rate revenue crossed $47B/year — @AnthropicAI (22,263 likes)
2. **OpenAI Codex computer use lands on Windows**: Codex can now take action on Windows machines, with mobile ChatGPT control — @OpenAI (8,734 likes)
3. **xAI launches grok-build-0.1 public beta**: Same model powering Grok Build CLI, $1/M input, $2/M output. Available on Cursor, Hermes, OpenCode, OpenRouter — @xAI (5,036 likes)
4. **Claude Code lead engineer drops 28-min prompt engineering masterclass**: CLAUDE.md files, memory shortcuts, parallel sessions, prompting patterns — @AnatoliKopadze (24,438 likes)
5. **Claude Opus 4.8 arrives in Cursor**: Much more efficient than Opus 4.7 on CursorBench, more persistent on harder tasks — @cursor_ai (3,930 likes)
6. **OpenAI models + Codex land on AWS Bedrock**: Enterprise-grade OpenAI access through existing AWS security/compliance workflows — @OpenAI (3,036 likes)
7. **Anthropic engineer reveals production Agent Teams architecture**: brain + hands + sessions, server-side loops, 10-15x faster team shipping — @0xMovez (738 likes)
8. **"Harness Is Everything" follow-up study**: 116/126 model-environment setups improved by patching harness alone, model frozen, 88.5% mean lift — @rohit4verse (922 likes)
9. **Claude Code official plugin claude-code-setup released**: Auto-scans projects, recommends hooks, skills, MCP servers, subagents — @servasyy_ai (1,499 likes)
10. **Agents Best Practices open-sourced**: Provider-neutral Agent Skill for production-grade harness design — @Xudong07452910 (613 likes)

📊 Today's Numbers: 30 X detailed items | 10 Company Updates | 5 Industry Leaders | 35 total

---

## Key Themes

1. **Agent infrastructure enters "cloud-everywhere" phase**: OpenAI Codex on AWS Bedrock, xAI grok-build cross-platform, Anthropic's $65B raise targeting enterprise deployment. Agents are becoming enterprise infrastructure, not just dev tools.
2. **Harness/framework layer is the new competitive battleground**: After SkillOpt, now agents-best-practices, claude-code-setup, and the "Harness Is Everything" follow-up all confirm: models converge, runtimes differentiate.
3. **Chinese community Agent tutorial explosion**: Claude Code Chinese tutorials from beginner to advanced, Codex plugin ecosystem coverage in Chinese. Learning barriers dropping fast.

---

## X/Twitter Highlights

### 🏢 Company Updates

#### 1. Anthropic $65B Series H + Confidential IPO Filing
- **Source**: @AnthropicAI | ❤️ 22,263 likes | 🔁 1,714 | 👁️ 7.7M views
- **Summary**: Series H at $965B post-money valuation, led by Altimeter, Dragoneer, Greenoaks, Sequoia. Same day: confidential S-1 filing announced (18,511 likes, 13.4M views). Run-rate revenue crossed $47B/year, driven by organizations deploying Claude in core operations.
- **Why it matters**: Anthropic enters IPO preparation. Agent infrastructure companies are scaling to trillion-dollar valuations.

#### 2. OpenAI Codex Computer Use on Windows
- **Source**: @OpenAI | ❤️ 8,734 likes | 🔁 947 | 👁️ 1.3M views
- **Summary**: Computer use now works on Windows. Codex can take action on Windows machines. With ChatGPT mobile integration, you can start/review/steer tasks from your phone while work continues on your Windows machine. Early experience, but direction is clear: cross-platform, cross-device agents.
- **Why it matters**: Computer use expands from macOS to Windows, covering the vast majority of developer desktops.

#### 3. xAI grok-build-0.1 Public Beta
- **Source**: @xAI | ❤️ 5,036 likes | 🔁 1,465 | 👁️ 1.2M views
- **Summary**: Same model powering Grok Build CLI, priced at $1/M input, $2/M output — extremely cost-effective for agentic coding. Available via OpenRouter, Vercel AI Gateway, Cursor, Hermes Agent, OpenCode, Kilo Code. Same day: Composer 2.5 released (3,690 likes), excels at long-running tasks and complex instructions.
- **Why it matters**: xAI enters the agent coding model market with aggressive pricing and multi-platform distribution.

#### 4. Claude Opus 4.8 in Cursor
- **Source**: @cursor_ai | ❤️ 3,930 likes | 🔁 158 | 👁️ 291K views
- **Summary**: Claude Opus 4.8 is now available in Cursor. Much more efficient than Opus 4.7 on CursorBench, more persistent on harder tasks.

#### 5. OpenAI Models + Codex on AWS Bedrock
- **Source**: @OpenAI | ❤️ 3,036 likes | 🔁 333 | 👁️ 602K views
- **Summary**: OpenAI frontier models and Codex now generally available on Amazon Bedrock. Enterprises can build with OpenAI through existing AWS security/compliance governance workflows. Beginning of broader OpenAI-AWS expansion including cybersecurity capabilities (Daybreak).

#### 6. Cursor Auto-Review Mode
- **Source**: @cursor_ai | ❤️ 2,011 likes | 🔁 130 | 👁️ 249K views
- **Summary**: Auto-review mode allows agents to run tool calls with fewer approval prompts while maintaining safe execution. Agents evolving from "approve every step" to "autonomous execution within safety boundaries."

#### 7. Anthropic Revenue Crosses $47B Run-Rate
- **Source**: @AnthropicAI | ❤️ 1,667 likes | 👁️ 614K views
- **Summary**: Run-rate revenue crossed $47B/year, driven by organizations across many industries deploying Claude in core operations.

#### 8. Google DeepMind SynthID Watermarking Partnership
- **Source**: @GoogleDeepMind | ❤️ 1,268 likes | 👁️ 116K views
- **Summary**: SynthID has watermarked over 100 billion pieces of content. Now partnering with OpenAI, ElevenLabs, and Kakao to expand watermarking adoption.

### 🌟 Industry Leaders

#### 9. Claude Code Lead Engineer's 28-Min Prompt Engineering Masterclass
- **Source**: @AnatoliKopadze | ❤️ 24,438 likes | 👁️ 6.2M views
- **Summary**: Boris Cherny, who built Claude Code, released a complete video covering CLAUDE.md file design, memory shortcuts, parallel sessions, and prompting patterns. "I've seen $300 courses that don't cover what he shows in the first 10 minutes." He also shared: "I haven't hand-written code in months. 49 features shipped in 2 days, 100% AI-written."
- **Why it matters**: The definitive guide to agent-based programming, from the tool's creator.

#### 10. How Anthropic Engineers Stay in the Loop with Claude
- **Source**: @trq212 (Claude Code @AnthropicAI) | ❤️ 4,439 likes | 👁️ 251K views
- **Summary**: Anthropic engineers share how they keep up with Claude's full scope of work internally. Recommends Suzanne's method for understanding the breadth of Claude developments.

#### 11. Microsoft Senior Developer Demonstrates Building AI Agents with Claude
- **Source**: @servasyy_ai (shared) | ❤️ 810 likes | 👁️ 172K views
- **Summary**: Microsoft AI developer demonstrates internal workflow: Opus 4.7 + 1,400+ pre-built MCP tools. Connect Claude to agent → give it tools → ship directly to production. 34-minute free video. "More valuable than any $500 vibe coding course."

#### 12. Anthropic Engineer Reveals Production Agent Teams Architecture
- **Source**: @0xMovez (shared) | ❤️ 738 likes | 👁️ 161K views
- **Summary**: Three building blocks: brain (persona) + hands (environment) + sessions = production agent. Server-side loops prevent refresh-induced failures. Agent teams ship to production 10-15x faster. Key insight: most agents die before production because people babysit fragile scripts instead of building robust architectures.

#### 13. Boris Cherny's Mobile Agent Workflow
- **Source**: @dotey (shared) | ❤️ 715 likes | 👁️ 213K views
- **Summary**: At Sequoia AI Ascent, Boris Cherny shared: 5-10 sessions running on his phone, hundreds of active agents, thousands of deep tasks running overnight. Uses Loop (cron-like scheduling). TRAE SOLO Mobile now enables mobile/web/desktop synchronized agent use.

### 🔥 Trending

#### 14. "Harness Is Everything" Follow-Up Study
- **Source**: @rohit4verse | ❤️ 922 likes | 👁️ 121K views
- **Summary**: Author's follow-up to the viral 1.3M-view post. The Life-Harness paper: 116 of 126 model-environment setups improved by patching harness alone, with model weights frozen. 88.5% mean lift across 18 backbones. Further proof that framework layer matters more than model choice for agent performance.

#### 15. Claude Code Official Plugin: claude-code-setup
- **Source**: @servasyy_ai | ❤️ 1,499 likes | 👁️ 241K views
- **Summary**: Anthropic quietly released an official plugin that auto-scans your project and recommends: hooks, skills, MCP servers, subagents, and automations. Install: `/plugin install claude-code-setup@claude-plugins-official`. Transforms Claude Code from "decent tool" to "real AI development environment."

#### 16. Codex Auto-Loop Review Skill
- **Source**: @steipete | ❤️ 2,716 likes | 👁️ 401K views
- **Summary**: Built a skill that runs codex /review in a loop until there are no issues left. Caveat: "It won't fix system architecture for ya, so you still need BRAIN as master model."

#### 17. Agents Best Practices Open-Sourced
- **Source**: @Xudong07452910 | ❤️ 613 likes | 👁️ 32K views
- **Summary**: Provider-neutral Agent Skill for Claude Code/Codex. Core philosophy: "The model proposes actions; the Harness validates, authorizes, executes, logs, and returns observations." Includes Agentic Loop, narrow tools with permission checks, planning patterns, context management, skill connectors, prompt caching, observability, and evaluation systems.
- **Link**: github.com/DenisSergeevitch/agents-best-practices

#### 18. Obsidian + Claude Code Local Knowledge Base Build
- **Source**: @joshesye | ❤️ 533 likes | 👁️ 35K views
- **Summary**: Two-day local knowledge base build: personal biography extraction → 19 top articles style-quantified to JSON → WeChat-OBS sync → Web Clipper → Claude + Codex dual model → headline generation skill → batch Feishu document migration.

#### 19. Claude Code Financial Data MCP in Practice
- **Source**: @cyrilXBT | ❤️ 982 likes | 👁️ 89K views
- **Summary**: One command to connect 17,000+ stocks, crypto prices, and financial statements as real-time data. 60-second setup via MCP.

#### 20. Pi Agent From-Scratch Tutorial
- **Source**: @cellinlab | ❤️ 833 likes | 👁️ 206K views
- **Summary**: Used Codex to learn Pi Agent principles, then built a step-by-step tutorial: building an AI agent from zero. Online learning version + source documentation.

#### 21. Codex Essential Plugins Checklist
- **Source**: @Pluvio9yte | ❤️ 468 likes | 👁️ 41K views
- **Summary**: Chrome (browser connection), GitHub (one-click repo ops), Gmail (daily email summaries), Vercel (one-click deploy), HyperFrames (PPT-style video generation).

#### 22. Codex Content Creation Plugin Ecosystem
- **Source**: @Etudecn | ❤️ 476 likes | 👁️ 59K views
- **Summary**: PPT, Excel, video, UI design, scientific illustrations, marketing posters — all auto-generated. "These aren't tools, they're cheat codes."

#### 23. Codex Disconnection/Slow Inference Config Fix
- **Source**: @op7418 | ❤️ 307 likes | 👁️ 75K views
- **Summary**: Config file had hardcoded parameters and mandatory MCPs causing massive slowdowns. Recommendation: let Codex inspect its own config file.

#### 24. Canvas Mode Becoming Agent Product Standard
- **Source**: @yihui_indie | ❤️ 324 likes | 👁️ 35K views
- **Summary**: Used Codex /goal to build a Lovart-style canvas image tool from scratch with React Flow + Supabase. Canvas interaction is becoming the standard Agent product UI pattern.

#### 25. Claude Code Chinese Tutorial Collection
- **Source**: @SunNeverSetsX | ❤️ 5,821 likes | 👁️ 702K views
- **Summary**: A paid ($98) Claude Code tutorial is now free in Chinese. Covers AI workflow building, automation, and complex task handling.

#### 26. Claude Code Full Curriculum Update
- **Source**: @sanbuphy | ❤️ 1,040 likes | 👁️ 170K views
- **Summary**: Complete Claude Code tutorial system: quick start, MCP guide, Skills guide, Agent Teams guide, Superpowers engineering, workflow best practices, and long-running coding tool guide.

#### 27. Obsidian + Claude Code = 24/7 Personal Operating System
- **Source**: @eng_khairallah1 | ❤️ 519 likes | 👁️ 80K views
- **Summary**: "Works while you sleep. The people who build this tonight will never work the same way again."

#### 28. Non-Technical Founder Ships 2 Apps in 2 Weeks with Codex
- **Source**: @fankaishuoai | ❤️ 209 likes | 👁️ 48K views
- **Summary**: MCN founder, purely liberal arts background, used Codex for two weeks and shipped a meditation app and a vegetarian app. Investors are interested. Now building a hardware-software AI agent product for a KOL with 100K private domain followers. "People used to say 'I have the idea and funding, just need a CTO.' Now you really don't need a CTO."

### 🚀 Rising Stars

#### 29. NVIDIA N1X Chip and Local AI Agent
- **Source**: @mubeitech | ❤️ 516 likes | 👁️ 130K views
- **Summary**: NVIDIA N1X ARM processor + RTX 5070-class GPU + 128GB unified memory. Runs 200B parameter models locally, unplugged without frame drops. Can directly run AI agents on-device.
- **Why it matters**: The hardware foundation for local agent execution is arriving.

#### 30. Codex Content Creation Ecosystem
- **Source**: @servasyy_ai | ❤️ 604 likes | 👁️ 106K views
- **Summary**: "Already hearing people uninstalling OpenClaw and Hermes Agent. Codex is really that good." Combined with HyperFrames: motion effects, transitions, subtitles, dubbing — all automatic.

---

## Notable Mentions

- NVIDIA N1X/N1 ARM processors, 128GB unified memory runs 200B locally — @AYi_AInotes (2,639 likes)
- GitHub Student Pack 2026 upgraded to $3500+, includes 1yr Cursor Pro — @AYi_AInotes (1,191 likes)
- Claude Code lead hasn't hand-written code in months, 49 features in 2 days — @servasyy_ai (1,445 likes)
- Anthropic pays $750K+/year for engineers who can build LLM architectures from scratch — @hrswatigupta (2,448 likes)
- Free 1yr Cursor Pro for students — @AYi_AInotes (1,235 likes)
- xAI hiring Chinese-language role, remote $35-45/hr — @_FORAB (1,548 likes)
- NVIDIA Jensen Huang's 33-year chip runs full CUDA stack + AI agents — @mubeitech (516 likes)
- Claude Code financial data MCP: 17,000+ stocks real-time — @cyrilXBT (982 likes)
- OpenAI Rosalind Biodefense initiative — @OpenAI (2,161 likes)
- Gemini Embedding 2 white paper released — @GoogleDeepMind (180 RTs)
- grok-build-0.1 available on Cursor, Hermes, OpenCode — @xAI (408 likes)
- Microsoft internal Claude Agent building 34-min demo — @servasyy_ai (810 likes)
- Codex native PPT Skill for Chinese users — @grgerwcetwet (1,061 likes)
- Claude Code beginner resource collection — @jiroucaigou (1,755 likes)
- TRAE SOLO Mobile three-device synchronized Agent experience — @dotey (715 likes)
