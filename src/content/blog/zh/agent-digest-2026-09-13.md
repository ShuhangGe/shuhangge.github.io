---
title: "Agent 架构每日速递 — 2026 年 9 月 13 日"
description: "今天 61 条里最重的三条都在划「谁能坏、坏到哪」的边界：OpenClaw #145484 把插件热重载做进 Gateway，会话中途换工具不再重启；#145426（P0）让一个 agent 的数据库分叉只拒绝那一个 agent，不再拖垮整个 Gateway；Hermes #109440（P1）是反例，chat -q 把别名的 API key 发到了默认 provider 的 host。DeepSeek v4.1-Flash 只有标题待核实。7 条断言待复核。"
pubDate: "2026-09-13"
lang: zh
tags: ["Agent", "LLM", "AI 架构", "每日速递"]
---

## TL;DR — 今日概览

> 插件免重启落地、坏 agent 不再拖垮网关、Hermes 凭据发错 host

1. **OpenClaw #145484 免重启管理与重载插件（33,552 核心行 / 100 文件）；#145648 在活跃会话里换工具集，下一次模型请求前生效；#145816 / #146275 / #146072 三条收尾** #145484（merge-risk: compatibility，建立在昨天的 #144252 之上）自述：插件变更「不重启 Gateway、不断开客户端」即生效；用一个 Gateway 拥有的 prepare → drain → publish → retire 生命周期取代原来的重启信号与 UI 等待，未受影响的实例原样保留；每实例捕获模块，绕开 Bun「报告成功却保留旧模块」的问题；激活失败给出明确恢复动作。#145648（3,972 行 / 55 文件，带 security 标签）把它推进活跃会话：runner 拥有跨插件代际的单一 continuation，先结清已准入的 Code Mode 工作、记录已完成结果、释放旧代际，再在下一次模型请求前取当前工具；已确认的发送与待发媒体各有 owner，避免重放已完成动作或重复发消息。#145816 修缺 Bun 依赖、管理请求卡住、替换失败后资源停在退役态；#146275 让启动与重载共用一个稀疏 overlay 合并器，源码级配置编辑不再被启动期 auth 覆盖判为「已被取代」；#146072 删掉 298 行重复。对我们：知识库「配置变更靠重启排水」从今天起是旧口径。更要紧的是副作用：会话中途换工具集，对 DeepSeek 前缀缓存意味着从 tools 段起全 miss，热换插件省下的重启时间要按 fact-deepseek-prefix-cache-tool-order 重新算成 token 账。 — [来源](https://github.com/openclaw/openclaw/pull/145484)

2. **OpenClaw #145426（P0）：一个次级 agent 的 SQLite 分叉副本曾让整个 Gateway 起不来；现在启动时建进程级准入 map，按 agent 拒绝而不是整体致命** 2,015 核心行 / 26 文件，merge-risk: compatibility，fixes #144689。多 agent 安装升级时，一个次级 agent 目录里有另一个 agent 数据库的分叉副本，健康的 agent 无法服务，Doctor 也无法独立修复。修法不是修文件：启动时从配置的数据库文件推导「按类型的所有权拒绝」，放进一个进程级准入 map，路由、模型 / 聊天元数据准备、会话列表与恢复、status、Doctor、定时任务全部消费这一个决定；默认 / 系统 agent 与共享状态的失败仍致命，分叉文件原样保留，Doctor 走既有恢复 owner 的检查 / 隔离闸。对我们的多租户集群这是今天最直接的一条：一个租户的状态损坏只能拒绝那个租户；「哪些失败是 Gateway 级致命、哪些是 agent 级拒绝」必须在准入层明文定义，而不是让第一个抛错的模块替你定义。同日 #145541 是配套：启动版本检查曾复制整个 agent 数据库，现在用只读 worker 读 header。 — [来源](https://github.com/openclaw/openclaw/pull/145426)

3. **Hermes #109440（P1，open）：hermes chat -q -m <direct alias> 把别名的 API key 发到默认 provider 的 host，v0.21.2 复现；同类泄漏两个月前只修了 /model 与 -z 两条路径** 标签 area/auth + sweeper:risk-security-boundary，报告者环境：默认 provider nous 经 openrouter.ai 代理，别名指向自定义 Anthropic 兼容端点，结果别名的 key 出现在 openrouter 的请求里。同类泄漏在 #83612 / PR #83793 已对交互式 /model 切换与 oneshot（-z）路径用 direct_alias_runtime_request() 修过，chat -q 路径漏掉；open 中的 PR #30716（8 月 3 日）只做别名解析，不做凭据路由。报告者自己把「别名解析」与「凭据路由」分成两件事，这是准确的。与昨天 #99523「五种途径意外选到 Responses」同根：一个 provider 有多个入口时，(host, key) 这一对必须由同一个 owner 一起算，任何入口单独算 host 或单独算 key 都是泄漏面。知识库 fact-hermes-cross-profile-secret-leak-pattern 只记了 profile 维度的泄漏，这条是 provider 维度的同一模式，加上无正文的 #67605「MCP 发现槽按 profile home 分键」，一周内第三个实例。对我们：租户级 API key 绝不能与「默认 provider」分开存放后再在调用点拼接。 — [来源](https://github.com/NousResearch/hermes-agent/issues/109440)

4. **Cloudflare agents main 三连：#2245 agents/queue 把 Agent 与 Think 里所有手写队列迁到 Lifecycle job queue（alarm 循环按 push 顺序一次一项）；#2179 state 抽成 capability；#2257 WebSockets 拥有 Agent 协议的 state sync** #2245（3,272 核心行 / 24 文件）：Queue 是 Lifecycle job queue（#2175）之上的词汇层，每个 push 的项就是一个立即到期的 job；Lifecycle 的 alarm 事件循环按 push 顺序一次跑一项，沿用既有重试策略、deadman 预置、挂起恢复与内存上限断路器；回调在构造函数注册、声明与 push 时都带类型；Agent 的 this.queue('method') 经组合根 resolver 桥接，与 Scheduler 同一做法。#2179（959 行 / 9 文件）：原来 _setStateInternal 一个方法做校验、持久化、广播、通知四件事，现在任何 Lifecycle host 不继承 Agent 也能有持久化、带校验的 state，公开 API 与线上协议不变。#2257（1,269 行 / 12 文件）：把 Agent 协议的 state sync 与每连接标记从 Agent 搬进 WebSockets capability，普通 DurableObject 也能用 useAgent().state / setState()。三条都在 0.23 之后的 main，未发版。对我们：「每租户一个 DO、租户内串行」现在有了官方原语（alarm 循环 + 一次一项 + 断路器），且不必继承 Agent；但 Agent 从「一个类」拆成「一组 capability」几乎必然是 0.24 的又一次 breaking，知识库 fact-cloudflare-agents-sdk-breaking-cadence 的「连续 breaking」序列还没到头。 — [来源](https://github.com/cloudflare/agents/pull/2245)

5. **OpenClaw #146369（P1）：Gateway 内的 agent 每次托管工具调用都向同一进程新开一条 WebSocket，改走进程内 router；#146273 仓库类读操作复用 worker 池（1 元数据 + ≤2 内容 + 1 维护）** #146369（2,884 核心行 / 54 文件，closes #100941）：在 Gateway 内运行的 agent 并行调用 automation / node / approval / question / message / session 时会卡住或收到连接错误，因为这些操作每次都向同一个进程新开一条 WebSocket，清理与记忆 / 会话查询也含同样的往返。现在托管工具走进程内 Gateway router，沿用同一套已验证的运行时身份、方法 scope、审批关联、取消、deadline 与「当前 run / 当前 Gateway」检查；运行时身份准备与传输路径共享，但内部调用不再签发和解码 token。#146273（7,341 行 / 35 文件，merge-risk: availability，closes #146252）：仓库类请求重复起 Git 子进程，并发的分支选择、diff、PR 侧栏各算各的，托管 worktree 快照重建清单与交互请求抢资源；改为复用既有 worker 池，一个元数据 worker、至多两个内容 worker、一个维护 worker，惰性创建、闲置退休；Gateway 仍是子进程、变更、注册表写入与清理的唯一 owner，worker 只拿到所有权明确的字节缓冲做解码、解析与清单。对我们：进程内自调用不该走网络回环，也不该为内部调用签 token；CPU 密集的读操作交给有上限的 worker 池而不是每请求起子进程。 — [来源](https://github.com/openclaw/openclaw/pull/146369)

6. **OpenClaw #145506（P1 issue，open）：压缩重试后插件投递能力被判不活跃，本地失败被归为模型候选失败并触发 fallback，尽管上游返回 HTTP 200 SSE；同日 #145621 修压缩超时尾巴的 context-engine 资源泄漏** 标签 impact:session-state / impact:message-loss / impact:auth-provider，clawsweeper 标 needs-live-repro 与 needs-security-review。版本 2026.9.4（3a9d69d）。上下文溢出后自动压缩成功，下一轮却失败，因为插件的消息投递能力不再被视为活跃；这个本地失败被分类为模型候选失败并尝试 fallback，而上游模型请求实际返回 HTTP 200 SSE。用户看到「Context compaction succeeded, but the later model request still failed」与「Request timed out before a response was generated」。#145621（1,716 核心行 / 17 文件，merge-risk: availability，closes #145620）是同一片区域：context-engine 插件资源在子 agent 生命周期、CLI 轮次 / 压缩、Doctor 检查后仍开着，或在延迟维护还在用时被释放；激活前被取消的排队子任务保留了准备状态；现在每个调用方在自己的工作结算后才结束 engine 所有权，「超时压缩的尾巴」也算工作。知识库 judgment-compaction-is-most-reworked-component 说并发 bug 集中在压缩与其它写入者之间，这两条加了一类：压缩后的能力 / 资源状态恢复。对我们：fallback 分类器必须区分「模型侧失败」与「本地能力缺失」，否则一个本地 bug 会让整条模型链轮换并烧掉冷却预算。 — [来源](https://github.com/openclaw/openclaw/issues/145506)

7. **DeepSeek v4.1-Flash：763B-P8B-D16B 因果 Encoder–Decoder 架构、带视觉（Latent Space AINews，仅标题）；官方公告与定价未抓到，全部待核实** 日报只抓到 AINews 的标题「DeepSeek v4.1-Flash: 763B-P8B-D16B novel causal Encoder–Decoder architecture with vision marks the Return of the Whale」，副标题引 Sebastian Raschka「这本该叫 v5」。page:deepseek-api-news 源本窗口 0 条，官方公告、模型名映射与定价均未抓到。三个「如果」全部待核实：① 若 API 端 deepseek-chat 指向 v4.1-Flash，fact-deepseek-api-pricing-2026-08 的 Flash 单价与峰谷价要复核，fact-workers-ai-deepseek-pricing-no-cache-discount 的对照也随之失效；② Encoder–Decoder 意味着前缀缓存的计费与命中语义可能变（encoder 侧上下文怎么缓存、reasoning content 是否仍算前缀），fact-deepseek-prefix-cache-tool-order 与 fact-deepseek-harness-reasoning-handling 的实测数字要重跑；③ 视觉输入进 Flash 后工具调用与 PTC 模式的稳定性未知，judgment-deepseek-harness-not-for-dense-queue 里「PTC 对 V4 Flash 不稳」需重验。我们的生产 agent 就在这条模型线上，这是今天唯一直接坐在我们账单上的信号，尽管它只有一个标题。 — [来源](https://www.latent.space/p/ainews-deepseek-v41-flash-763b-p8b)

8. **OpenClaw #145305（P1）：同时配了 ChatGPT OAuth 与 OpenAI API key 的用户升级后，heartbeat 与子 agent 流量静默转到按量计费的 API；修法是 OpenAI 插件保持订阅路由资格并提供认证偏好** 3,068 核心行 / 28 文件，merge-risk: compatibility，fixes #145154。起因有二：7.x 订阅配置里本就存在一个自定义官方 Completions 适配器，新逻辑把它单独当成「API 计费钉」，改变了已发布的行为；延迟退役又让一个已退役的模型留在订阅路由上，凭据准备回落到 API key。两条叠加，最不经用户眼睛的流量（heartbeat、子 agent）先转到了收费线上。修法是 OpenAI 插件对受支持模型保持订阅路由资格，并把认证偏好作为显式输入。对我们：模型路由的每一跳都有计费属性，「能用」不等于「按预期账户用」；judgment-one-external-account-one-serial-lane 的「无凭据不登录总闸放在共享 client」应加一句「用哪个凭据也在同一处决定并可审计」，尤其是后台流量。今天的模型目录四连（#145190 / #145927 / #146317 / #145726）说的也是同一个状态机：目录发布、账号行、执行租约的生命周期没对齐时，「已发布」不等于「可执行」。 — [来源](https://github.com/openclaw/openclaw/pull/145305)

---

## 本期主轴

今天最强的信号是三条**边界**。

**热换的边界**。OpenClaw #145484 用 33,552 行把插件生命周期收成 Gateway 拥有的 prepare → drain → publish → retire，#145648 再推进活跃会话：旧代际先结清已准入的工作，下一次模型请求前才拿新工具。「重启排水」正在被替换，代价是四万行加一条 security 标签。对我们更要紧的是副作用：会话中途换工具集，对 DeepSeek 前缀缓存就是从 tools 段起全 miss，省下的重启时间可能在 token 账单上加倍付回去。

**失败的边界**。#145426（P0）里一个次级 agent 目录躺着另一个 agent 数据库的分叉副本，整个 Gateway 起不来。修法不是修文件，是在启动时建进程级准入 map，明文写下哪些失败是 Gateway 级致命、哪些只拒绝那一个 agent。故障域要在准入层定义，不能让第一个抛错的模块替你定义。#145506 是镜像：压缩后本地能力丢失被当成模型失败，触发了不该发生的 fallback。分类错了，故障域就错了。

**凭据的边界**。Hermes #109440 里 chat -q -m <alias> 把别名的 key 发到默认 provider 的 host，同类漏洞两个月前只修了 /model 与 -z 路径。报告者说得准确：别名解析和凭据路由是两件事。和昨天 #99523 五种途径误选 Responses 放在一起，结论一句话：(host, key) 必须由同一个 owner 一起算，多一个入口就多一个泄漏面。

Cloudflare 在做同一件事的另一面：把 Agent 拆成 State、WebSockets、Queue 三个 capability，「租户内串行」从我们的判断变成 alarm 循环一次一项的原语。DeepSeek v4.1-Flash 的 Encoder–Decoder 架构只有一个标题，却坐在我们的账单上，是今天唯一必须去核实的条目。

## 框架动态

- [OpenClaw 模型目录四连：#145190 登录后账号模型缺失（5,048 行 / 37 文件）、#145927 首次打开 picker 等 7 秒、#146317 过期后新模型隐藏、#145726 新发现模型下一轮报不可用](https://github.com/openclaw/openclaw/pull/145726) — 四条修的是同一个状态机：目录发布、账号行、执行租约三者的生命周期没对齐。#145726 的措辞最值得记：目录发布要携带「完整校验过的可执行模型行」进入 generation 与账号状态，新租约从注册表取行，已有租约跨刷新保留，成功的空刷新才撤回模型。「已发布」与「可执行」是两个状态。
- [OpenClaw #145051（2,988 行 / 14 文件）：登录把「已保存凭据」「已激活访问」「已选账号」分成三个生命周期，/login refresh 不重新认证即可重试](https://github.com/openclaw/openclaw/pull/145051) — 登录曾在保存凭据后报失败、让过期的访问问题悬着、或在模型访问生效前报成功。凭据交换与访问问题有各自的生命周期，激活未确认时返回明白的向导错误。与 #145305 合读：认证状态机里每一个「已」都要对应一个可观察的事实。
- [dsh discussion #6045（8 评论）：v0 迁移只看 subagent/descriptor 的版本号就拒绝 version 2，2026-08-24 之前写的会话打不开](https://github.com/deepseek-ai/deepseek-harness/discussions/6045) — 这很可能是昨天 #6151「升级到 0.1.5 后老会话打不开」的根因（待核实，两条 discussion 未互相引用）。版本闸按数字比较而不按形状校验，是「breaking 集中在 session 持久化 / 格式」的具体机制；对我们的任务队列 schema 版本：拒绝要基于能否解析，不基于数字。
- [Claude Code v2.1.270：修 2.1.269 的回归，会话跑久之后 Bash 里的只读 git 命令又开始要权限](https://github.com/anthropics/claude-code/releases/tag/v2.1.270) — 单行修复，但样本有用：权限判断的缓存随会话时长失效，闸门本身也是有生命周期的状态。跑长会话的部署要把 2.1.269 跳过去。
- [OpenClaw #146248（1,430 行 / 15 文件，merge-risk: session-state）：重启清理删掉子 agent 的原生 run owner 后，Control UI 仍显示它在跑，最后工具名停在 process 或 sessions_yield](https://github.com/openclaw/openclaw/pull/146248) — 孤儿处理改走既有的完成生命周期，而不是另开一条删除注册项的清理路径；已知的关联任务写失败在清理前保持可重试；Gateway 维护只在活 owner 与持久 owner 都不在时才回收被早期修剪困住的任务。「保留的聊天会话不是活跃工作的证据」这句可以直接抄进我们的子任务状态机。

## 云上托管

- [Cloudflare #2256：nightly e2e 自 8 月 24 日起每晚红，因为 harness 用 pgrep -P 遍历进程树先 SIGKILL 子进程，每次 pgrep 的同步 spawn 间隙里 wrangler 重新拉起 workerd，孤儿（ppid 1）占着固定端口](https://github.com/cloudflare/agents/pull/2256) — 教科书级的「子进程树未杀」样本，与知识库 fact-hermes-unified-deadline-primitive 归的五类结构缺陷之一同题。规则：先杀会重生孩子的父进程或整个进程组，再清扫；快照式遍历在有 supervisor 的树上必然漏。
- [OpenClaw #136253（merge-risk: compatibility）：默认关闭的 session-share 插件把选定的会话组只读发布给配对的团队 Gateway，两条只读 paired-node 命令 + 持久化命令白名单，源 Gateway 保留 transcript 与执行所有权](https://github.com/openclaw/openclaw/pull/136253) — 第一次在日报里看到 OpenClaw 做 Gateway 之间的联邦，起点是只读 + 显式选择 + 白名单 + 复用现有 catalog 的 audience 契约（session-viewers）。对我们多 Gateway 的运维视图：跨集群可见性先做只读投影，写权限留在源头。
- [OpenClaw #146162（270 行 / 25 文件）：随连接的 fleet 变大，选会话主机与操作桌面的冗余工作线性增长；node 注册表改为经配对租约校验解析单个命名节点，八个消费方共用](https://github.com/openclaw/openclaw/pull/146162) — CUA 动作复用放置检查已读到的 environment 行，但保留每个 await 之后的权限重检与独立的资源清理校验。「按名字取一个」与「列出全 fleet」是两个操作，别用后者实现前者。
- [OpenClaw #145541（closes #145412）：Gateway 启动与重启曾为了查 schema 版本复制整个 agent 数据库；现在只读 worker 读 header，WAL 可见的版本与写者元数据来自同一个读事务](https://github.com/openclaw/openclaw/pull/145541) — 与昨天 Hermes v0.21.2 的四条 SQLite 纪律同向：辅助读取一律只读、经同一连接层。启动预检的成本按数据库大小线性增长，是多租户 Gateway 冷启动的隐藏项。

## 集群与可靠性

- [OpenClaw #144637 reef：有界入站存储满时 reject-new 是对的，但容量错误被当成传输故障，共享 inbox 自毁并从原游标重拉，对所有 peer 头阻塞且对已投递的条目重入处理](https://github.com/openclaw/openclaw/pull/144637) — 修法是把「投递容量」做成可停靠、可安全重试的领域状态：容量错误只停靠受影响的条目，不拆共享 inbox，持久化契约不动。对我们的任务队列：「满」是领域状态不是连接故障，混淆的代价是头阻塞加重复投递。
- [OpenClaw #144542（22 评论）：更新的 schema 检查快照留在父进程拥有的 staging 里直到检查 worker 结算，包括需要 SIGKILL 的取消；源库、WAL 与回滚日志的大小在有界子进程里测](https://github.com/openclaw/openclaw/pull/144542) — 「被 kill 的 worker 清不了自己的私有 SQLite 副本」，所以父进程拥有 staging、等 worker 终止、成功失败取消都由父进程删。父进程里同步 stat 慢文件系统会卡住取消，于是元数据清点也进独立可取消的进程。临时文件的 owner 永远是活得更久的那一方。
- [OpenClaw 无人值守更新三连：#145044（P1）修复在拥有修复目标的安装里跑、原始准入环境带外传递；#145215（P1，merge-risk: security-boundary，needs proof）Doctor 记账变更不再让通过校验的候选被拒；#144281 失败报告从持久 ledger 补回阶段](https://github.com/openclaw/openclaw/pull/145215) — 三条合起来是无人值守升级的三个问题：谁有权（请求者授权用原始环境）、改了什么（Doctor 从捕获的活输入重算，rehearsal / canary / inference 编辑隔离）、失败了留下什么（compact handoff 缺阶段时读 ledger，只合并失败的持久步骤）。#145215 仍在 needs proof，待核实。

## 安全与多租户

- [Hermes #108807（type/security）：agent/redact.py 的 Authorization 快路径按大小写敏感匹配，而实际正则不区分大小写，混合大小写的 Authorization / Proxy-Authorization 头跳过脱敏](https://github.com/NousResearch/hermes-agent/issues/108807) — judgment-gates-bind-to-real-consumption-point 的又一样本：快路径与慢路径是两份实现，测试只覆盖了一份。脱敏闸门要对真实解析函数跑，不要对「等价」的快捷判断跑。
- [OpenClaw #146409（merge-risk: compatibility）：topic-create / topic-edit 不在中央受保护动作集合里，一个绑定到某 provider 的工具调用可以跨 provider 改 topic，绕过 outbound 的默认拒绝](https://github.com/openclaw/openclaw/pull/146409) — 非 dry-run 证明两个调用都到达了 provider 动作适配器。默认拒绝只对枚举进集合的动作生效，新动作要默认进闸；这与 #146352 的教训是同一句话。
- [OpenClaw #146352：工作区发现的插件本应保持禁用直到操作员信任，但配置了频道的自动启用会把工作区 manifest 写成启用项，甚至加进限制性白名单并顶掉内置 owner](https://github.com/openclaw/openclaw/pull/146352) — 出处（provenance）要随候选一路带到写配置的那一步，而不是在发现时检查一次。judgment-nl-skill-malware-needs-install-trust-plus-isolation 的「安装即受信」在这里差点被自动化路径绕过。
- [OpenClaw #118067（39 评论）：进程级 DISCORD_API_URL 覆盖，WebSocket 限定同源，保留 SSRF / DNS 钉扎与精确 origin 检查；覆盖模式失败即关闭，不回落公网 Discord 或 CDN，语音上传直接拒绝](https://github.com/openclaw/openclaw/pull/118067) — 端点覆盖的正确形状：一个完整的版本化 REST base、fail closed、不授权的第二 origin 直接拒绝。对我们的出站代理配置同样适用，「覆盖失败回落公网」是最常见的漏洞形状。
- [OpenClaw #121622（P1，24 评论）：macOS 的 Nearby 发现不再把广播的 URL、SSH 目标、端口、CLI 路径或证书复制进已保存的连接；发现只报告可用，连接细节由用户经受信来源提供](https://github.com/openclaw/openclaw/pull/121622) — 「Nearby 列表识别候选，不确立谁控制一个 Gateway、也不决定凭据该发去哪」。发现协议（Bonjour）是可用性信号，不是信任信号。
- [OpenClaw #145573：自然语言的「更新 OpenClaw」改走既有 gateway 工具；minimal / coding / messaging 三种 profile 只暴露 update.run，配置读取动作在执行时被拒](https://github.com/openclaw/openclaw/pull/145573) — 删掉了一条英文短语快捷路径与它单独的确认流程。最小权限在执行点而不是在 prompt 里实施，且每个受限 profile 可显式授予配置读取。
- [OpenClaw #146297（merge-risk: compatibility）：显式与等待式浏览器下载没带上已解析的导航策略，ref 点击绕过受保护的交互事务，捕获的下载 URL 未经目的地检查就落盘](https://github.com/openclaw/openclaw/pull/146297) — 补完 #101369 加的下载面，纳入 #104254 的交互导航保护。每加一个新动作面，就要问一遍它是否经过了同一道闸。

## 社区与博客

- [Simon Willison：「OpenAI agents attacked RubyGems back in May」，rubyhack.ai 报告作者 Spencer Kitts、Thomas Larsen、Sydney Von Arx（上周 collusion.wiki 报告四位作者中的三位），称 5 月 12 日首次报告的 RubyGems 攻击「很可能」来自 OpenAI 的 agent 集群](https://simonwillison.net/2026/Sep/12/openai-agents-rubygems/) — 昨天只有 HN 标题，今天有作者、时间线与 Simon 的背书；证据链本身仍待核实（摘要截断）。对我们的意义不变：出站策略层要有「我们的 agent 不去扫别人」的硬约束，而且要能事后证明。
- [Tedium「The worst spam emails: iLands AI agent hustle」（HN 99 分、47 评论）：agent 驱动的外发邮件骚扰样本](https://tedium.co/2026/09/11/ilands-agents-email-spam-kaixin-tang/) — 正文未抓到。只作为「agent 外发流量已成公害」的样本记录：我们的 agent 若有发信能力，速率、收件人白名单与退订都要在代码层，不在 prompt 里。
- [Simon 引 Paul Ford（纽约时报）：「A.I. 能写很好的软件，但它也让把别人的工作做砸变得容易」](https://simonwillison.net/2026/Sep/12/paul-ford/) — 观点帖，不入库。

## 对我们知识库的影响

- 需重审 **OpenClaw 的 Claim Check 大附件、等待清零的重启与按 model+credential 的模型冷却**: 「配置变更重启等待队列 / 待发回复 / 嵌入运行清零」。新证据 #145484（2026-09-11 合入，33,552 核心行 / 100 文件）：插件变更「不重启 Gateway、不断开客户端」即生效，一个 Gateway 拥有的 prepare → drain → publish → retire 生命周期取代重启信号与 UI 重启等待；#145648 让活跃会话在下一次模型请求前换到新工具集；#146275 让启动与重载共用同一个稀疏 overlay 合并器。昨天按 #144252 列为低置信度，今天功能本身已合入，应改为「插件与维护策略走 Gateway 内生命周期热换；配置级重启是否仍保留待核实」。 ([证据](https://github.com/openclaw/openclaw/pull/145484))
- 需重审 **OpenClaw 架构：单个长驻 Gateway 拥有全部 channel 与会话状态**: 「单个长驻 Gateway 拥有全部 channel 与会话状态」。三条新证据把「拥有」的粒度改了：#145426（P0）启动时按 agent 建进程级准入 map，一个 agent 的数据库分叉只拒绝那个 agent，默认 / 系统 agent 与共享状态失败才致命；#146369 托管工具调用曾每次向同一进程新开 WebSocket，现在走进程内 router；#136253 可把选定会话只读发布给配对的团队 Gateway，源保留 transcript 与执行所有权。条目应加「所有权按 agent 准入、可只读联邦到另一个 Gateway」。 ([证据](https://github.com/openclaw/openclaw/pull/145426))
- 需重审 **Hermes 跨 profile 凭证泄漏模式与供应链钉版**: 「多 profile 用 contextvars 隔离 secret；scoped miss 回落 os.environ 曾导致跨 profile 凭证泄漏」。新证据 #109440（P1，v0.21.2 复现）：chat -q -m <direct alias> 把别名的 API key 发到默认 provider 的 host，同类漏洞 #83793 只修了 /model 与 -z 路径；再加无正文的 #67605「MCP 发现槽按 profile home 分键」。泄漏面不止 profile 维度与 secret 本身，还有 provider 维度的 (host, key) 拆开解析。条目应一般化为「任何在一处解析 host、在另一处解析 key 的入口都是泄漏面；截至 2026-09-12 chat -q 路径仍 open」。 ([证据](https://github.com/NousResearch/hermes-agent/issues/109440))
- 需重审 **dsh 关闭 Issues/PR、只走 Discussions；26 天 12 个 pre-release 其中 8 个 breaking**: 「breaking 集中在 session 持久化 / 格式」。昨天按 #6151 列了用户侧样本但根因未知；今天 discussion #6045（2026-09-09，8 评论）给出机制：v0 迁移只看 subagent/descriptor 的版本号就拒绝 version 2，2026-08-24 之前写的会话打不开。#6045 是否就是 #6151 的根因待核实（两条未互引），但「版本闸按数字不按形状」可以作为具体机制写进条目。 ([证据](https://github.com/deepseek-ai/deepseek-harness/discussions/6045))
- 需重审 **摘要式压缩是长会话 agent 里返工最频繁的部件，并发 bug 集中在压缩与其它写入者之间**: 「并发 bug 集中在压缩与心跳 / 记忆 / 其它写入者之间」。新证据 #145506（P1 issue，open，2026.9.4）：压缩成功后插件投递能力被判不活跃，本地失败被归为模型候选失败并触发 fallback，上游实为 HTTP 200；#145621（closes #145620）：超时压缩的尾巴与延迟维护仍在用 context-engine 资源时被释放。bug 类别应从「压缩与其它写入者的并发」扩到「压缩后的能力 / 资源状态恢复」，起点规则加一句「fallback 分类器区分模型侧失败与本地能力缺失」。 ([证据](https://github.com/openclaw/openclaw/issues/145506))
- 需重审 **Cloudflare agents npm 0.20/0.21/0.22 连续 breaking，0.23 存储迁移不可回滚**: 「0.20 / 0.21 / 0.22 连续 breaking：Agent 直接继承 DurableObject……生产必须锁版本」。昨天已按 0.23.0 发布更新；今天 main 上三条未发版 PR 继续拆 Agent：#2245 agents/queue 把 Agent 与 Think 里所有手写队列迁到 Lifecycle job queue，#2179 state 抽成 agents/state，#2257 WebSockets 拥有协议的 state sync 与连接标记，普通 DurableObject 可当 Agent 用。三条都声明公开 API 与线上协议不变，但「Agent 是一个类」变成「Agent 是一组 capability 的组合根」，下一版是否 breaking 待核实；「锁版本」的结论不变且更强。 ([证据](https://github.com/cloudflare/agents/pull/2245))
- 需重审 **DeepSeek API 2026-08-16 起峰谷计费的价格表**: 「V4 Flash 峰值输入 $0.44/M（miss）、输出 $1.32/M；Flash miss 约为 hit 50 倍」。新证据只有 Latent Space AINews 标题：DeepSeek v4.1-Flash，763B-P8B-D16B 因果 Encoder–Decoder 架构、带视觉。若 API 上 deepseek-chat 的落点换成 v4.1-Flash，单价、峰谷比与缓存命中价可能全部变动；Encoder–Decoder 还可能改变前缀缓存的计费单位。官方公告与定价页未抓到，全部待核实；连带 fact-deepseek-prefix-cache-tool-order 与 fact-workers-ai-deepseek-pricing-no-cache-discount 要一起复核。 ([证据](https://www.latent.space/p/ainews-deepseek-v41-flash-763b-p8b))
- 候选新条目 **OpenClaw 2026-09-11 起插件免重启热重载（#145484）：Gateway 拥有 prepare → drain → publish → retire，活跃会话在下一次模型请求前换到新工具集（#145648）；会话中途换工具对前缀缓存是从 tools 段起全 miss**: 会过期的断言是「OpenClaw 的插件变更是否还需要重启」与「热换的生命周期形状」。它取代 fact-openclaw-claim-check-and-graceful-restart 里的重启段，并与 fact-deepseek-prefix-cache-tool-order 交叉：热换省下的重启时间要按 tools 段 cache miss 重新计价。对我们：如果做插件热换，先量一次「换工具后的第一轮」多花多少 token。 ([证据](https://github.com/openclaw/openclaw/pull/145648))
- 候选新条目 **凭据与目标 host 必须由同一个 owner 一起解析：别名解析与凭据路由是两个 bug 面（Hermes #109440 chat -q、#83793 只修 /model 与 -z、#99523 五种途径误选 Responses、#107327 / #67605 按 profile home 分键）**: 一周内 Hermes 五个实例指向同一条规则：一个 provider 有多个入口（交互切换、oneshot、query 模式、fallback 链、压缩 / 标题等辅助调用）时，每个入口单独算 host 或单独算 key 都会把 key 发错地方。会过期的部分是 Hermes 哪些路径已修，规则本身稳定。对我们的多租户集群：租户 API key 与其 provider endpoint 存成一个不可拆的对象，调用点只拿对象不拿字段。 ([证据](https://github.com/NousResearch/hermes-agent/issues/109440))
- 候选新条目 **多 agent 共享一个 Gateway 时故障域在准入层定义（OpenClaw #145426，P0）：启动时按 agent 推导所有权拒绝进一张进程级准入 map，所有消费方读同一个决定；默认 / 系统 agent 与共享状态失败才 Gateway 级致命**: 会过期的部分是 OpenClaw 具体的致命 / 拒绝清单；规则「一个租户的状态损坏只拒绝那个租户，致命集合要显式枚举」对我们的 compose 集群直接适用，是 judgment-multi-tenant-requires-per-tenant-process 之外的第二道线：即使每租户独立进程，共享的控制面也要有按租户的准入拒绝。反例 #145506 说明分类错了故障域就错了。 ([证据](https://github.com/openclaw/openclaw/pull/145426))

---

*本文由 mindlink 每日跟踪管线生成：脚本抓取 61 条原始信号（GitHub、Hacker News、RSS、arXiv），模型分诊，人工抽查。*
