---
title: "Agent 架构日报 — 2026年6月15日"
description: "Fable 5 系统提示词泄露至 GitHub（1585行），Claude API 旧模型字符串今日失效，Agent SDK 计费独立，Anthropic 起诉美国政府，Coinbase MCP Agent 交易上线"
pubDate: "2026-06-15"
lang: zh
tags: ["Agent 架构", "AI 智能体", "MCP", "Fable 5", "每日日报"]
---

## TL;DR — 今日概览

1. **Fable 5 1,585 行系统提示词泄露至 GitHub**：研究员 "Pliny the Liberator" 通过多 Agent 分解、Unicode 技巧和叙事框架攻破 Fable 5 安全分类器。泄露的提示词揭示 Fable 为长时间运行的 Agent 工作设计，非一次性任务。来源：CyberSecurityNews、LinkedIn

2. **Anthropic 起诉美国政府**：幕后故事浮现——国防部将 Anthropic 标记为供应链风险，Anthropic 提起法律诉讼，随后 Fable 5 出口管制令下达。来源：AIToolsRecap

3. **6月15日生效**：Claude API 旧模型字符串返回错误。Agent SDK 计费从订阅中分离——付费用户获得独立月度额度。Claude Code 周限额提升 50%。来源：多个

4. **Coinbase MCP Agent 交易上线**：基于 MCP 的工具让 AI Agent 自主交易、购买研究报告、执行金融操作。来源：The Daily Agentic

5. **MCP 2026年7月28日候选版本**：下个 MCP 版本支持 server-as-agent 递归组合——MCP 服务器连接其他 MCP 服务器。不只是版本号升级，更像一次重大演进。来源：Zylo Research、AAIF

6. **Hermes Agent 上线 MCP 工具搜索**：Nous Research 增加 MCP 工具发现——模型运行时按需找到相关 MCP 工具，无需预加载。来源：The Daily Agentic

7. **Shai-Hulud PyPI 攻击波**：23 个含 MCP/AI 主题名的 PyPI 恶意包。来源：The Daily Agentic

8. **Stack Overflow 探索 Agent 共享记忆**：解决"短暂智能鸿沟"——不同平台的 Agent 共享上下文。来源：The Daily Agentic

9. **多 Agent 失败模式论文**（arXiv）：多 Agent 系统失败不是因为模型弱，而是协调差——角色模糊、过早共识、无纪律合成、协调成本失控。来源：BemiAgent

10. **中国：2026 被标记为"Agent 之年"**：LangGraph 成为生产标准。Deep Agents（LangChain + NVIDIA）企业级平台。来源：知乎

📊 今日数据：**10 条详细亮点 | 20+ 条 Web 来源 | X 数据不可用（浏览器需重新认证）**

---

## 头条 — Fable 5 事件第五天

### 攻破安全层的越狱

6月10日 Anthropic 发布 Fable 5——首个 Mythos 级模型。6月11日 "Pliny the Liberator" 已绕过安全分类器。方法：**多 Agent 分解**——多个 Agent 分工越狱、Unicode 混淆、叙事框架包装。结果：提取出禁止内容以及模型**120,000 字符 / 1,585 行系统提示词**。

提示词内容揭示 Fable 5 的架构理念：为**长时间运行的 Agent 工作**设计——数小时的编程会话、自主研究、复杂多步骤工作流，而不是"帮我写个函数"。

### 政治幕后

- **国防部**将 Anthropic 标记为供应链风险
- **Anthropic 起诉**美国政府
- 3 天后 **Fable 5 出口管制令**下达
- 6月15日：Claude 开始执行 API 废弃

这不是简单的安全故事——而是前沿 AI 实验室与美国政府的法律对抗。

### 6月15日 — 今天生效的变化

1. **Claude API 旧模型字符串 → 错误**：旧模型标识符不再自动回退
2. **Agent SDK 计费独立**：付费 Claude 用户获独立月度 Agent SDK 额度
3. **Claude Code 周限额 +50%**
4. **Managed Agents 可在用户基础设施中运行**

---

## Web — Agent 基础设施与 MCP

### Coinbase MCP Agent 交易上线
Coinbase 基于 MCP 的工具让 AI Agent 自主交易、购买研究报告、执行金融操作。不是 demo——是真正的 Agent 到金融系统集成。

### Hermes Agent MCP 工具搜索
Nous Research 上线 MCP 工具发现——模型在运行时找到正确的 MCP 工具，无需预加载所有工具，降低上下文开销。

### MCP 2026年7月28日候选版本
下个 MCP 规范版本正在成形：
- **Server-as-agent**：MCP 服务器连接其他 MCP 服务器——递归组合
- **无状态协议核心**正式化
- **扩展框架**
- **Tasks** 作为一等概念

### Shai-Hulud PyPI 供应链攻击
23 个含 MCP/AI 主题名的 PyPI 恶意包瞄准 Agent 开发工具链。

### Stack Overflow 探索 Agent 共享记忆
解决"短暂智能鸿沟"——不同工具的 Agent 共享上下文，不再每次从头开始。

---

## Web — 框架与工具

### Microsoft Agent Framework 正式发布
BUILD 2026 上 MAF 从预览升级为 GA：
- Copilot SDK 集成（shell 执行、文件操作、URL 获取、MCP 服务器集成）
- GitHub Copilot 编程能力作为 Agent 后端
- Windows Agent Runtime 本地执行

### LangGraph 成为中国生产标准
知乎分析：LangGraph 已成为事实上的生产 Agent 运行时。LangChain "Deep Agents"（基于 LangGraph）与 NVIDIA 合作打造企业级平台。2026 被标记为"Agent之年"。

### Agent SDK 计费趋势
Google ADK Java 1.0 发布。Claude Agent SDK 今日独立计费。OpenAI Agents SDK 持续演进。趋势：Agent SDK 成为独立计费产品，不再是订阅附加项。

---

## 研究

- **多 Agent 协调失败**（arXiv）：多 Agent 系统失败不是因为模型弱，而是协调差——角色定义模糊、过早共识、无纪律合成、协调成本失控。实践意义：多花时间在编排设计上，少纠结模型选型。
- **5 天 AI Agent 课程**（Kaggle/Google，6月15-19日）：Agent 密集开发课程
- **Agentic 搜索替代 RAG**：Claude Code 从向量数据库转向 Agentic 搜索

---

## 值得关注

- **Fable 5 系统提示词分析**：1,585 行提示词本质上是长时自主 Agent 的用户手册
- **AibleClaw + NVIDIA Nemotron 3 Ultra**：长时受管控 Agent 的规划与执行
- **Databricks Data + AI Summit**（6月15-18日）
- **Enterprise AI Agent 治理**：微软押注治理（而非模型能力）是企业 Agent 部署的门槛
- **Hedera Agent 协议悬赏**：构建交易类 Agent，截止 6月21日

---

## 中文社区

- **知乎**：「2026：Agent 之年」—— AI 智能体如何重塑生产力
- **LangGraph 成生产标准**：Deep Agents + NVIDIA 企业级平台
- **10 家大厂 AI 共识与暗战**：Agent B 端仍在试点，26年H2 预期标杆案例
- **Gartner 预测**：2028 年全球 90% B2B 采购由 AI 智能体介入
- **甲子光年报告**：企业级 AI Agent 从辅助工具到业务核心

---

*日报覆盖 2026年6月15日。来源：AI 新闻、MCP 生态、中文 AI 社区 Web 搜索。注：X/Twitter 数据不可用——浏览器需重新登录 x.com。恢复认证后 cron 管线应自动恢复 X 采集。*
