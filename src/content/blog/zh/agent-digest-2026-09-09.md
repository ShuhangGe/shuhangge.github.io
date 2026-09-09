---
title: "Agent 架构每日速递 — 2026 年 9 月 9 日"
description: "今天 71 条信号里最重的三件事：OpenClaw 四个互不相关的模块（长会话清理、OAuth 刷新、语音委派、云 worker 输入）同一天用同一种修法收口，给可变对象配 generation、写入前 compare-and-swap；exec 自动评审员从「allow / ask」升级为三态裁决并拿到带来源标签的有界 transcript；dsh 0.1.5-alpha.1 宣布系统提示词可动态修改且不破坏 KV Cache，session 格式四天内从 v2 跳到 V3。另有 6 条知识库断言需要复核。"
pubDate: "2026-09-09"
lang: zh
tags: ["Agent", "LLM", "AI 架构", "每日速递"]
---

## TL;DR — 今日概览

> 代数围栏成为 OpenClaw 的默认修法，dsh 打破 system prompt 字节稳定不变量

1. **OpenClaw #137381：长会话 sessions_yield 清理曾让 transcript 短暂不可用，改为 writer-fenced 后缀突变 + generation 旋转** P2 但标了 merge-risk: availability + compatibility，核心路径 6,036 行 / 34 文件，304 条评论是今天最多的。原来的尾部清理是「删掉整份 SQLite transcript 再重插」，重建投影期间历史与有界上下文对模型不可见；现在改成一次精确的、writer-fenced 的后缀突变，同一事务里旋转 transcript generation 让旧游标失效，并修复 identity、active-branch、FTS、watermark、parent、leaf-control 状态；compare-and-swap 输给并发写时 SQLite 与内存里的 SessionManager 一起放弃（PR 正文截断处）。对我们：会话日志的「压缩 / 裁剪」也是写入者，必须走和普通 append 一样的 writer 校验，不能拿删表重插当原子操作。 — [来源](https://github.com/openclaw/openclaw/pull/137381)

2. **OAuth 刷新同日三处竞态：OpenClaw 把刷新所有权改为共享凭据代数（#141477 / #142628），Hermes 修 refresh_token 被响应省略时丢失（#62333）** #141477（P1，核心路径 11,453 行 / 71 文件，merge-risk: compatibility）：OAuth 刷新的所有权是进程本地的，而凭据代数是共享的，超时或失败的刷新者会让复制出的 agent、排队写入者、登录持久化和回滚路径重放或恢复一个已消费的 refresh token；provider I/O 与 secret / 配置持久化跨越不同的所有权窗口，迟到的结算能覆盖更新的凭据或复活旧凭据。修法是把所有权归到 SQLite auth-profile 生命周期这一个 owner。#142628（P1）是同族：Codex 外部认证只等 10 秒刷新回调，轮换在超时后才完成，下一请求又强制刷新一次；现在成功登录后记录无 secret 的 access 指纹 + 精确账号 ID，只复用该账号 access bearer 已变的凭据。Hermes #62333 只有标题：刷新响应不带 refresh_token 时不能把旧的丢掉。对我们：串行车道解决「谁在刷新」，不解决「刷新完成时世界是否已变」，refresh token 一次性消费的语义要求按凭据代数 fence。 — [来源](https://github.com/openclaw/openclaw/pull/141477)

3. **OpenClaw exec 自动评审员从「allow / ask」变三态裁决，并拿到带来源标签的有界 transcript（#141987 / #142279）** #141987（4,074 行 / 22 文件）承认在 tools.exec.mode=auto 下自动评审员「实践中几乎没用」：裁决只有 allow / ask，ask 就是人工审批卡，任何非 low 风险的 allow 也被转成人工；而且大多数命令根本到不了模型，因为 Gateway 要求先凑出「可执行计划」（每个可执行文件解析、路径钉死），未加引号的 glob、带展开的链式命令全部跳过模型直接找人。现在评审员可 allow / deny / escalate。#142279（1,087 行 / 20 文件）再给它一段有界 transcript：user / assistant / tool_call / tool_result 条目带截断文本、工具名和 origin 标签（operator / channel / inter_session / internal_system / unknown），并强调标签「由记录的用户轮次出处派生，绝不由 role 推断」；PR 直接对标 Codex 的 Guardian reviewer。对我们：代码层闸门仍是底线，但如果要加模型评审，只看命令不看意图与来源的评审员只是人工审批的前置过滤器。 — [来源](https://github.com/openclaw/openclaw/pull/141987)

4. **OpenClaw #141799（impact:security，open）：嵌套 scope 收窄用字面成员过滤，丢掉了 operator.write 蕴含的 read 权限** 进程内合成客户端的 scope 收窄（src/gateway/server-plugin-in-process-dispatch.ts:207-217）对「已准入 scope 只含 operator.write、嵌套请求要 operator.read」返回空列表，而权威策略 authorizeOperatorScopesForRequiredScope（method-scopes.ts:266-289）明确 write 蕴含 read 与 talk。两处对同一策略各写了一份，结果一份比另一份严。这是知识库「闸门必须直接跑真实消费方的代码路径，不手抄镜像清单」判断的活样本，方向恰好相反（更严而不是更松），说明镜像清单的问题不是松紧而是漂移。对我们的多租户权限层：scope 蕴含关系只能有一个实现，嵌套 / 委派调用必须回调它，不能在派发点再抄一个集合运算。 — [来源](https://github.com/openclaw/openclaw/issues/141799)

5. **OpenClaw #141451（P1）：升级曾先迁库改配置再拒绝启动，旧包也起不来；现在只读准入跑在迁移租约之前** 评级 diamond lobster，来自一位用户在 X 上的报告。故障形状：Gateway 升级先迁移数据库、重写配置，然后才发现自己不能启动，而这时上一版包已经读不懂新数据，用户两头都起不来。修法：只读准入先跑一遍（状态库与 agent 库、旧 sessions 与 workspaces、Gateway 就绪、完整的 dry config-repair 结果），再拿持久迁移租约，租约下重复一遍，全部通过才允许迁移写入；直接启动的 server 消费同一份快照。这是昨天 supersede 候选「doctor --fix 已升级为演练 + 自动回滚」的第三块拼图：2026.9.1 回滚 npm 候选、2026.9.3 隔离候选状态演练、现在再加「写之前先证明能起」。对我们 compose 集群的 schema 迁移：迁移脚本的第一步应该是只读地验证新版本能在当前数据上就绪，而不是 ALTER TABLE。 — [来源](https://github.com/openclaw/openclaw/pull/141451)

6. **dsh 0.1.5-alpha.1：系统提示词可动态修改且不破坏 KV Cache（模型须显式声明），session 格式四天内 v2 → V3，插件 Agent / Inbox API 再改** 发布说明（09-08）三条会改知识库的内容：① 「支持动态修改系统提示词且不破坏 KV Cache，模型需显式声明支持」——知识库里「三家都把 system prompt 字节稳定当不变量」和 dsh README 的「任何 prompt 变化都从首个受影响 token 起失效」对 dsh 不再成立，机制（是模型侧能力还是 harness 把 system prompt 挪进消息历史）待核实；② 「会话格式升级至 V3：恢复历史会话时生成新版日志并保留原文件，系统提示词纳入消息历史，旧 PTC 事件与 code 预设引用自动迁移，自定义日志读取器需适配，升级后不支持降级读取」——v2 是 09-04 引入的；③ 「移除 ctx.agent，调用方需显式传递 Agent；Inbox 改为类型接口，hasPending 与 claim 不再是公共接口；修正可继续对话的子代理归属，避免被当作根会话参与定时调度」。另：可选子代理运行时升级到 Codex 0.153.4 与 Claude Code 2.1.263；macOS / Linux 不再需要本地编译 fs-ext。「模型可自行恢复用户已暂停目标」这条修复值得注意：暂停是人的决定，模型不该能撤销。 — [来源](https://github.com/deepseek-ai/deepseek-harness/releases/tag/dsh-v0.1.5-alpha.1)

7. **Cloudflare Agents #2216：流式输出改为 256 KB 滚动 block 日志，stream → session 切换变成一个事务** 3,471 行 / 19 文件。cf_agents_stream_chunks（每次 append 一行）改为 cf_agents_stream_blocks：append 用 UPDATE 把当前 block 行撑到 256 KB 再开下一块，「仍是每次 append 计一次行写，但几千个 chunk 只占几行，删除时是几次写而不是几千次」；schema v2 在启动时把旧 chunk 行折叠成 block。临时流块与最终 session 消息在同一个 Durable Object 存储里，「流块 → session 消息 → 删除流块」是一个事务，writer.close({ commit, discard }) 与 error(reason, ...) 负责结算。这直接对应知识库「DO 按写行数计费、schema 要按写行数设计」的断言，也是 0.23 之前又一次启动时自动迁移。对我们：如果在 DO 上做流式回传，chunk 不是行，block 才是行。 — [来源](https://github.com/cloudflare/agents/pull/2216)

8. **Claude Code 2.1.265 / 2.1.266：resumed 子 agent 曾打破 prompt cache、工具结果落盘加 1 GB 上限、进程死亡后恢复保留被打断的工具调用；2.1.265 让代理 / 网关配置全部请求失败** 2.1.265 修了两个直接影响子 agent 成本的缺陷：恢复前台生成的子 agent 时它的工具列表与 system prompt 前缀会变，打破 prompt cache 复用；agent teammates 与 resumed 子 agent 在后续轮次把 SubagentStart hook 上下文与预加载技能移出前缀，同样打破 cache。知识库「fork 继承 prompt cache」的表述没错，但「resume」这条路径直到 2.1.265 都在漏 cache。同版：工具结果落盘加 1 GB 上限并在会话内提示截断；上一个进程在工具运行中死亡后恢复，不再改写最后一条 prompt，被打断的工具调用保留并标记 interrupted（与知识库「回灌历史尾部不能丢」同向）；--plugin-dir 可指向插件文件夹并热感知增删；插件路径含反斜杠曾绕过 symlink 包含检查。2.1.266 是紧急修复：2.1.265 让 CLAUDE_CODE_USE_GATEWAY 单独就强制 Cloud gateway 登录，所有搭配 API key、apiKeyHelper 或自定义 auth 头的代理配置每个请求都报「Not signed in to the Cloud gateway」。把 Claude Code 当 headless 运行时跑在自建网关后面的人，这一版要跳过。 — [来源](https://github.com/anthropics/claude-code/releases/tag/v2.1.265)

9. **OpenClaw #137576（P1）：云 workspace 同步期间提交的输入等 15 秒后显示中断，现在排队输入在 claim 结算期间保持已准入并重读权威 placement** 核心路径 2,440 行 / 24 文件。故障：健康的云 workspace 对账进行中时提交后续消息，会等 15 秒、显示中断、红色报错，用户只能复制重发；初始 worker 搭建期间同样，消息在同步中被接受，同步完成后却变成中断而不是开始执行。修法：Gateway 在上一个 workspace-result claim 结算期间保持持久待处理输入的已准入状态，然后重读权威 placement，让同一个 run 走既有的 local、worker 或 redispatch 路径继续；初始搭建只加入精确的活跃派发或恢复过程。PR 正文里的 placement / redispatch 词汇再次说明 OpenClaw 的 cloud worker 已经有调度层，比知识库「只外派 exec / 文件系统」的描述多出一截（昨天已列 supersede 候选，今天是第二份证据）。对我们：任务队列里「已接受但 worker 还没就绪」是一个必须持久化的状态，超时不能把它降级成失败。 — [来源](https://github.com/openclaw/openclaw/pull/137576)

---

## 本期主轴

今天 OpenClaw 的 38 条里最值得抄的不是某个功能，而是一个在四个互不相关模块里同时出现的修法：**给每个可变对象配一个 generation，写入前 compare-and-swap，输了就整体放弃**。#137381 把长会话清理从「删掉整份 transcript 再重插」改成一次 writer-fenced 的后缀突变，并旋转 transcript generation 让旧游标失效；#141477 把 OAuth 刷新的所有权从进程本地改为共享凭据代数，超时的刷新者不能再回放已消费的 refresh token；#129186 / #129535 把语音委派的回调绑到 session、requester、run、lifecycle generation 四元组，过期回调在 provider 输出前就被拒；#137576 让排队输入在 workspace claim 结算期间保持已准入，再重读权威 placement 继续同一个 run。对我们这种 Postgres + 任务队列的集群，含义很直接：租约只回答「谁在跑」，不回答「这次写入是否基于最新状态」；后者需要一列 generation 和一条带 WHERE 的 UPDATE。OpenClaw 用了几个月的事故才把它推广到每个写入点，我们可以一开始就当默认。

第二个信号是闸门形态在变。#141987 承认 exec 自动评审员「实践中几乎没用」：只能 allow / ask，大多数命令因为凑不出「可执行计划」根本到不了模型；#142279 再给它一段有界 transcript，每条带 origin 标签，且明确「由记录的出处派生，绝不由 role 推断」。这不否定代码层证据闸门，但补了一课：模型评审员如果只看命令不看意图和来源，只是人工审批的前置过滤器。#141799 从反面印证同一件事：同一条 scope 蕴含策略写了两份，字面过滤那份比权威那份严，镜像清单的问题是漂移而不是松紧。

第三个信号来自 dsh。0.1.5-alpha.1 宣布「支持动态修改系统提示词且不破坏 KV Cache」，前提是模型显式声明，同时 session 格式四天内从 v2 跳到 V3。知识库里「三家都把 system prompt 字节稳定当不变量」要改写成「Hermes 与 OpenClaw 仍是，dsh 改为模型能力声明」，而 dsh 的 breaking 计数还在涨。

## 框架动态

- [OpenClaw #131805 / #142751：子会话显示的是默认模型，实际路由的是父会话继承的模型](https://github.com/openclaw/openclaw/pull/131805) — #131805（35 条评论，closes #86174）：WebChat 显示配置的默认模型，请求却走父会话继承的模型；选「Default」也没记录足够意图阻止再次继承。修法复用 modelOverrideSource 字段写入 default 作为继承屏障，投影暴露 inherited 来源。#142751 是同题：继承模型在 agent 级列表、全局列表、详情读取、历史元数据与变更事件之间不一致。「显示的」与「路由的」必须来自同一个投影，我们的多租户模型选择也一样。
- [OpenClaw #142158 / #142620：隐式模型目录按 provider 端点收窄；上一 PR 让公开 SDK 的目录读取意外变成被动，插件拿不到发现的模型](https://github.com/openclaw/openclaw/pull/142620) — #142158：配了自定义端点的 provider 仍会宣告端点可能不支持的原生模型，现在一条 provider 拥有的资格规则覆盖清单规划、隐式发现、静态准备、运行时增补与恢复。#142620 修 #142557 引入的公开 SDK 回归：内部目录读取本应变被动，但重导出让普通 SDK 调用也变被动。同一个 loader 在内部与公开面要两个默认值，是插件 SDK 常见的兼容陷阱。
- [OpenClaw #142909：从历史对话学技能不再走隐藏扫描面板，改为一个可见的普通会话](https://github.com/openclaw/openclaw/pull/142909) — 4,268 行 / 38 文件。此前的历史扫描器在模型看到之前就过滤对话，Auto 模式下也强制挂起建议，用户看不见也不能干预；现在「Learn from past conversations」创建一个带可见挖掘指令的普通 agent 会话，复用正常准入、配置模型与允许工具。把后台学习变成可审计的会话，与知识库「记忆投毒防线在写入路径」同向。
- [Hermes 三条只有标题的 agent 修复：后台评审的记忆访问限定到触发者、继承并冻结空的父工具列表以保 cache 一致、切换 surface 不得重新预填整个请求](https://github.com/NousResearch/hermes-agent/pull/103579) — #105921、#103579、#104414 均无正文，细节待核实。三条合起来看是同一个主题：后台评审 / 派生请求要与父请求前缀一致、权限不得放大。#103579 的「冻结空工具列表以保 cache parity」是 prompt cache 条目的又一个实例。

## 安全与多租户

- [OpenClaw #141972：mcp.servers.*.env 与 .headers 不接受 SecretRef，MCP server 的凭据只能明文放配置](https://github.com/openclaw/openclaw/issues/141972) — 2026.9.2 的配置 schema 里 env 值只允许 string / number / boolean；通过环境变量或 bearer 头认证的 MCP server 秘密都要操作员手工管理。反差在于 acpx 面上的 mcpServers.*.env 已支持 SecretRef。对我们：给租户挂 MCP server 时，凭据注入面必须是统一的，不能按接入路径分两套。
- [OpenClaw #142271（P1，open）：secret 出口代理开启时，cron agentTurn 在 Claude CLI 后端上无法执行任何 Gateway 主机 exec](https://github.com/openclaw/openclaw/issues/142271) — CLI 后端以 mcp__openclaw__exec、caller 为 direct 回调 Gateway，到达时没有运行中的 run 实例，exec 在 shell 启动前就抛错；同一任务换 codex / OpenAI 后端成功，2026.9.3 仍复现。昨天 #135331 是出口代理与容器沙箱互斥，今天是与 CLI 后端互斥：凭据代理注入这一层每接一种执行后端都要单独验一次。
- [OpenClaw #143003：已准入的非 owner WhatsApp 用户能聊天却不能 /new 重置自己的隔离 DM 会话](https://github.com/openclaw/openclaw/issues/143003) — enforceOwnerForCommands: true 一刀切拒绝非 owner 命令；提议只放开「重置自己的会话」。标签 needs-security-review + needs-product-decision + impact:session-state。这是昨天「共享 Gateway 走向多用户」的下一层：一旦有了按身份的会话，就要有按身份的会话控制命令，而不是把命令面整体留给 owner。
- [Hermes #105532：Windows Desktop 把默认根证书与系统根证书直接拼接，不过滤过期、不去重，对有效网关报 CERT_HAS_EXPIRED](https://github.com/NousResearch/hermes-agent/issues/105532) — Electron 40.10.2 / Node 24.15.0，Windows curl 对同一网关返回 200。Desktop 把它显示成通用的后端连接超时。自建远程网关 + 桌面客户端的团队要注意：信任库合并逻辑本身是故障点。
- [OpenClaw #141876：发布验证按精确 chunk 文件名 + SHA-256 记录插件所有权，扫描真实安装后的 import](https://github.com/openclaw/openclaw/pull/141876) — 此前靠生成的 region 注释和包级依赖豁免判断 chunk 归属，能藏住根级 createRequire() 导入或来自另一个构建输出的导入；现在验证器要求拥有该 chunk 的插件清单声明依赖，并按 Node 解析规则跟随根相对导入。供应链闸门再次落到「真实消费方的代码路径」上。

## 集群与可靠性

- [OpenClaw #142135：Gateway deadline 在任何回复之前结束轮次却不留持久通知，重载后历史里出现连续两条无解释的用户请求](https://github.com/openclaw/openclaw/pull/142135) — 1,092 行 / 19 文件，fixes #141839。现在两个终止阶段都用既有的 failure-report 写入器落一条「轮次在回复前结束」的通知，穿过分页、游标更新、重置导航与归档保留；提交时重查精确的当前所有权。与知识库「尾部没回复的 user 消息不能丢」同题，且补了一条：没回复这件事本身也要有记录。
- [OpenClaw #142991：已接受的 image_generate 任务在返回 started 后可能失去插件资源，现在资源所有权随任务活到清理结束](https://github.com/openclaw/openclaw/pull/142991) — 2,329 行 / 15 文件。已退役的来源不能再接新任务，但已接受的任务保留捕获的 provider 直到工作与清理完成。和上周 #139657「没有清理证据不释放所有权」是同一条规则的另一个模块。
- [OpenClaw #142278：嵌入运行以超时 / 空闲超时断路器结束时，模型回退链停下却不说原因](https://github.com/openclaw/openclaw/pull/142278) — closes #141838。终态结果按设计不再尝试下一个模型，但缺诊断；现在每个终态生产者携带有界原因到既有的回退观察者，对多候选链只记录一次。我们的模型回退（DeepSeek 主 + 备）也要区分「可回退的失败」与「终态」并把后者写进日志。
- [OpenClaw #129186 / #129535（均 P1）：语音委派绑定到 session、requester、run 与 lifecycle generation，过期回调在 provider 输出前拒绝；yielded 子 agent 结果回到语音](https://github.com/openclaw/openclaw/pull/129186) — #129186（2,674 行 / 18 文件）：新的语音委派可能启动竞争的嵌入工作，或在权威 Talk owner 被取消、替换、撤销后送达迟到结果；现在保持一个规范父 run，把新请求导入其精确后端，并用一次性 claim 投递完成 / 失败。#129535（1,516 行 / 19 文件）：委派结果此前只回到聊天，不回语音，且保留的回调能活过被替换的 owner。知识库里「完成通告发错会话」的第四种形态：不是发错人，是发给了已经不存在的 owner。
- [Hermes #102574：周期调度器的回调与会阻塞的兄弟回调隔离](https://github.com/NousResearch/hermes-agent/pull/102574) — 仅标题，无正文，待核实。字面意思是一个阻塞的定时回调会拖住同一调度器里的其它回调，属于知识库 Hermes deadline 条目里「事件循环阻塞」缺陷类的又一实例。
- [Hermes #101420：跨 OS 安装 / 更新 E2E 矩阵，41 种安装到更新组合，12 小时定时跑](https://github.com/NousResearch/hermes-agent/pull/101420) — Windows 18、macOS 15、Linux 8 种组合，按用户真实方式安装（Setup.exe GUI 自动化、dmg、安装脚本）再走每条更新路径，断言已安装 checkout sha、更新标记生命周期与应用重启；不在 PR 上跑，靠 12 小时定时与发版 tag 触发，作者称 #100803 就是这样在几小时内被抓到的。「升级路径要有自己的持续验证」比 OpenClaw 的冻结候选验证走得更早一步。
- [OpenClaw #141107：托管 Responses 代理拿不到请求的会话亲和头，Azure 兼容路由还丢了 session 与 cache-retention 值](https://github.com/openclaw/openclaw/pull/141107) — fixes #140918。执行器现在为 OpenAI、Azure 与续作身份统一准备一次 HTTP 头，WebSocket 请求沿用同一策略；关闭 cache retention 时省略相应头。经代理访问模型时，会话亲和头直接决定 prompt cache 命中，值得在我们的 LLM 网关链路上核对一次。

## 社区与博客

- [Latent Space：OpenAI 称用 Astra-next、约 10,000 个 agent、130B token（超过 4,000 万美元）在 88 小时内找到 Navier-Stokes 奇点](https://www.latent.space/p/ainews-openai-reports-navier-stokes) — 数字全部来自 AINews 标题，待核实。若属实，这是目前公开的最大规模单任务 agent fan-out：平均每个 agent 1,300 万 token。对知识库「多 agent 3–15× token」条目不构成反例（没有单 agent 基线），但给「可并行搜索类任务」这一收益类别加了一个上限样本。同期还有 Cognition 480 亿美元 E 轮、Mistral 240 亿美元 D 轮。
- [Simon Willison：On the Navier–Stokes Millennium Prize Problem](https://simonwillison.net/2026/Sep/8/on-navier-stokes/) — OpenAI 用未发布模型给出 Navier-Stokes 存在性与光滑性问题的一个解法；Simon 指出成果被一些指控盖过（摘要截断处）。与 Terence Tao 同日的评论合看：「有人在做某题的传闻就会触发大规模 AI 算力去碾平它」，研究方向的公开分享激励正在改变。
- [Muse：Meta 的个人 AI agent（HN 540 分、592 评论）](https://ai.meta.com/muse/) — 日报只抓到标题与热度，无正文。又一家平台级个人 agent 入场，与 OpenClaw / Hermes / dsh 这类开源个人 agent 的差异在托管方与数据归属；对我们的托管多租户集群是竞品线索而非技术信号。
- [OpenAI：GPT-5.6 Sol 与 Codex 自主运行量子计算实验并校准量子比特](https://openai.com/index/codex-quantum-computing-experiments) — MIT 研究者的案例稿，无工程细节；记录在此只为跟踪「agent 直接操作实验硬件」这一类工作负载的出现，对可靠性要求与我们的外部写闸门同构。

## 对我们知识库的影响

- 需重审 **dsh 关闭 Issues/PR、只走 Discussions；26 天 12 个 pre-release 其中 8 个 breaking**: 「开源 26 天发 12 个 pre-release，其中 8 个含 breaking」与「session format v2 截至 2026-09-08 也才 4 天」。新证据：dsh-v0.1.5-alpha.1（2026-09-08）再发一版，含三处 breaking：会话格式升级至 V3（升级后不支持降级读取，自定义日志读取器需适配）、插件 Agent API 移除 ctx.agent、Inbox 改为类型接口且 hasPending / claim 退出公共接口。计数应更新为 27 天至少 13 个 pre-release、至少 9 个含 breaking（0.1.4 系列是否存在待核实，日报未抓到），breaking 清单表要加一行；v2 只活了 4 天就被 V3 取代，「session 格式相对稳定」的说法要更谨慎。 ([证据](https://github.com/deepseek-ai/deepseek-harness/releases/tag/dsh-v0.1.5-alpha.1))
- 需重审 **三个 harness 的压缩参数：OpenClaw 三层、Hermes 网关 85% / agent 50% 双阈值、dsh 80% 触发保留 16%**: 摘要与正文「共同不变量」段：「三家都把 system prompt 字节稳定当不变量……dsh 把同一点写成 KV cache 视角」，且表头 dsh 列标 0.1.3-alpha.2。新证据：dsh 0.1.5-alpha.1 发布说明「支持动态修改系统提示词且不破坏 KV Cache，模型需显式声明支持」，同时 V3 格式「系统提示词纳入消息历史」。dsh 已把字节稳定从不变量降为「模型未声明支持时的默认」；Hermes 与 OpenClaw 的表述不受影响。实现机制（模型侧能力还是 harness 把 system prompt 挪到历史里）待核实。 ([证据](https://github.com/deepseek-ai/deepseek-harness/releases/tag/dsh-v0.1.5-alpha.1))
- 需重审 **dsh 的 Model-visible ⟺ logged 不变量 + append-only session 日志是可回放证据链范式；OpenClaw 同样每 session FIFO lane + writer id 校验**: 正文 dsh 段「截至 0.1.3-alpha.2……Session format v2（0.1.3-alpha.1，2026-09-04）……旧日志以相邻不可变 generation 迁移」。新证据：0.1.5-alpha.1 把格式升到 V3：恢复历史会话时生成新版日志并保留原文件（迁移模式不变），但系统提示词纳入消息历史、旧 PTC 事件与 code 预设引用自动迁移、自定义日志读取器需适配、升级后不支持降级读取。「模型可见 ⟺ 已记录」不变量本身更强了（system prompt 也进日志），但版本号、事件族是否变化、以及我们若做日志读取器要对的格式都要更新。 ([证据](https://github.com/deepseek-ai/deepseek-harness/releases/tag/dsh-v0.1.5-alpha.1))
- 需重审 **DeepSeek 自动前缀缓存把 tools 放在 system prompt 之后、历史之前；会话中途改工具集会让此后整段历史 miss**: 正文引 dsh llm-deepseek README：「任何路由 / prompt / schema / 历史 / 图片预算的变化都从首个受影响 token 起失效」。新证据：dsh 0.1.5-alpha.1 声称在模型显式声明支持时可以动态修改系统提示词而不破坏 KV Cache。若属实，「任何 prompt 变化都失效」对声明支持的模型不成立；这可能意味着 DeepSeek 服务端有新的缓存能力，会直接影响我们「固定工具面」策略的成本模型。仅有发布说明一句话，README 是否已改与哪些模型声明支持均待核实。 ([证据](https://github.com/deepseek-ai/deepseek-harness/releases/tag/dsh-v0.1.5-alpha.1))
- 需重审 **Cloudflare agents npm 0.20/0.21/0.22 连续 breaking，0.23 存储迁移不可回滚**: 表中「0.23（main）」行只列了 ai-chat / Think 消息表首次唤醒即单向迁移到 cf_agents_session_*。新证据：#2216（2026-09-08）把 cf_agents_stream_chunks 改为 cf_agents_stream_blocks，「Schema v2 在启动时把现有 chunk 行折叠成 block」，属于同一批未发布变更里的第二个启动时自动迁移；writer.close 的签名也改为 { commit, discard }。0.23 的迁移面比条目描述的更宽，「main 已堆 17 个未发布 changeset」的计数也需刷新。是否可回滚待核实。 ([证据](https://github.com/cloudflare/agents/pull/2216))
- 需重审 **Claude Code subagent 机制（截至 2.1.263）：后台默认、并发 20、深度 3、fork 继承 cache**: 标题「截至 2.1.263」与正文「2.1.232 subagent_type:fork 继承完整对话与 prompt cache」。新证据：2.1.265（2026-09-08）说明「恢复前台生成的子 agent 时其工具列表与 system prompt 前缀会变，打破 prompt cache 复用」以及「agent teammates 与 resumed 子 agent 在后续轮次把 SubagentStart hook 上下文与预加载技能移出前缀」——在 2.1.265 之前，resume 路径上的子 agent 实际并不保 cache。同版新增工具结果落盘 1 GB 上限与 --plugin-dir 目录加载，2.1.266 回滚了 CLAUDE_CODE_USE_GATEWAY 的行为变化。条目的版本锚点与 cache 继承的适用范围需要更新。 ([证据](https://github.com/anthropics/claude-code/releases/tag/v2.1.265))
- 候选新条目 **共享凭据的 OAuth 刷新要按凭据 generation fence，租约与进程锁不够：OpenClaw #141477 / #142628 与 Hermes #62333 同日修同类竞态**: 三条可直接写进我们共享 client 的规则：① 刷新所有权与凭据代数绑定，超时 / 失败的刷新者不得回放已消费的 refresh token，也不得让迟到的结算覆盖更新的凭据；② 刷新回调超时后，若轮换实际已完成并持久化，下一请求要复用而不是再刷一次（#142628 的 10 秒回调窗口）；③ 刷新响应不带 refresh_token 时保留旧的（#62333）。它补上 judgment-one-external-account-one-serial-lane 的失效条件：车道保证串行，不保证「基于最新代数」。 ([证据](https://github.com/openclaw/openclaw/pull/141477))
- 候选新条目 **exec 审批里的模型评审员要三态裁决 + 有界 transcript + 按出处派生的 origin 标签，否则只是人工审批的前置过滤器（OpenClaw #141987 / #142279）**: OpenClaw 自己的生产观察（team.openclaw.ai）：只有 allow / ask 且大多数命令因凑不出可执行计划而跳过模型时，评审员「几乎没用」。#142279 的设计要点可复用：origin 标签（operator / channel / inter_session / internal_system / unknown）从记录的用户轮次出处派生而非 role；transcript 有界；评审员判断的是「用户是否授权了这件事」而不是命令本身危不危险。与 judgment-high-risk-tool-two-phase-confirm-in-code 互补：代码闸门管不可逆动作，模型评审管中风险命令的意图对齐。 ([证据](https://github.com/openclaw/openclaw/pull/142279))
- 候选新条目 **Cloudflare Agents 流式输出的行预算：append 用 UPDATE 撑大 256 KB block 而不是每 chunk 一行，stream → session 切换是单事务（#2216，0.23 前未发布）**: 把 fact-durable-object-hard-limits 里「按写行数设计 schema」落成一个可量化的实现样本：每次 append 仍计一次行写，但几千 chunk 只占几行，删除成本从几千次写降到几次；schema v2 启动时折叠旧 chunk 行。会过期的地方：256 KB 阈值、表名、writer.close 签名都在 experimental 阶段，0.23 发布时需复核。 ([证据](https://github.com/cloudflare/agents/pull/2216))

---

*本文由 mindlink 每日跟踪管线生成：脚本抓取 71 条原始信号（GitHub、Hacker News、RSS、arXiv），模型分诊，人工抽查。*
