---
title: "AI 股票专业分析报告：为什么买 CRDO、ANET、ETN、COHR，为什么卖出 LRCX、KLAC、AMAT、MRVL"
description: "一份面向学习和复盘的专业分析报告：从 AI 产业链瓶颈、ROCKET 领导者信号、候选股证据强度、买入理由、卖出理由、仓位上限和退出规则，解释本次 AI 股票调仓。"
pubDate: "2026-06-06"
lang: zh
tags: ["AI股票", "投资研究", "产业链", "交易复盘", "ROCKET策略"]
---

## 0. 阅读定位

这篇不是 Telegram 快讯，而是完整的专业分析报告。目标不是只告诉你“买什么、卖什么”，而是解释：

1. AI 行业现在的钱流向哪里；
2. 为什么这些公司属于 AI 产业链里的真实瓶颈；
3. 为什么现在买，而不是等证据完全确定；
4. 为什么卖出的公司并不一定是坏公司，而是当前机会成本更高；
5. 以后如何自己判断买入、卖出、加仓、减仓。

本报告基于 stock-agent 的严格 readiness 验证运行：

- Run ID: `rocket-eb93bb319d6f`
- 数据库: `data/smoke/early_buy_validation_20260606_105926.db`
- Readiness: `FULL_TRADE_READY`
- Evidence backlog: `48/48 complete`
- Report status: `READY`
- 关键限制: 所有 EARLY_PARTIAL 买入都受 `15%` 仓位上限约束。

---

## 1. Executive Decision / 执行结论

本次建议是一次 **从较弱的半导体设备敞口，轮动到 AI 网络、光互连和数据中心电力瓶颈** 的交易。

核心动作：

- 卖出 / 减仓：`AMAT`, `KLAC`, `LRCX`, `MRVL`
- 买入 / 加仓：`CRDO`, `ANET`, `ETN`, `COHR`

一句话解释：

> MRVL 和 HPE 这类 AI 基础设施/网络方向的 ROCKET 信号，说明市场正在重新定价 AI 数据中心的网络、互连、光模块和电力瓶颈。CRDO、ANET、COHR、ETN 更接近这次被验证的瓶颈链条；LRCX、KLAC、AMAT 仍是好公司，但在本次模型里不是最强的近端 catch-up 机会。

最重要的风险控制：

- 这些买入不是“满仓确定性买入”；
- 它们多数是 `EARLY_PARTIAL`；
- 所以每个新买入受到 15% cap；
- 这代表“早期证据可以买，但不能按完全确认的证据给满仓”。

---

## 2. AI Industry Map / AI 产业链地图

### 2.1 当前 AI 资金流向

过去一轮 AI 交易，市场最先定价的是 GPU 和核心算力，例如 NVDA、AMD、AVGO、MRVL 这类更靠近芯片和 AI 网络核心的公司。

但 AI 数据中心不是只有 GPU。真正的基础设施链条是：

```text
AI 模型需求
→ GPU / 加速器集群
→ 高速网络与交换机
→ 光互连 / AEC / CPO / 光模块
→ 数据中心电力 / 变压 / UPS / 冷却
→ 机柜、施工、运维、融资
```

当 GPU 集群规模变大，瓶颈会从“有没有 GPU”扩散到：

- GPU 之间能不能高速通信；
- 数据中心网络能不能承载更高东西向流量；
- 光模块和高速互连能不能跟上；
- 电力容量、配电、冷却能不能支持新机房上线。

这就是本次报告选择 CRDO、ANET、COHR、ETN 的产业逻辑。

### 2.2 ROCKET leader 的意义

我们不只是看某个股票涨了多少。ROCKET 股票的作用是提供产业信号。

本次关键 leader / signal node：

- `MRVL`: AI networking / optical / custom silicon 方向的信号；
- `HPE`: AI server / data center buildout 方向的信号。

正确理解方式不是：

> MRVL 涨了，所以只买 MRVL。

而是：

> MRVL 的 AI 网络逻辑被市场重新定价，所以寻找仍未完全补涨、但能从同一瓶颈受益的 Level 1/2/3 公司。

这就是 ROCKET catch-up 策略的核心：

```text
ROCKET leader
→ 识别热的 AI area
→ 找到真实瓶颈
→ 找到受益但尚未完全补涨的供应链公司
→ 用证据质量决定仓位大小
```

---

## 3. Current Thesis / 当前投资主线

### 3.1 本次主线

本次主线不是“泛 AI”。泛 AI 太宽，容易把任何电力、芯片、软件公司都硬套成 AI 概念。

本次主线更具体：

1. **AI networking**：AI 集群需要更高带宽、更低延迟的网络；
2. **AI interconnect / optical**：GPU 集群扩大后，互连和光模块成为瓶颈；
3. **AI data center power**：AI 机房耗电和供电复杂度提升，电力基础设施成为约束；
4. **Catch-up gap**：leader 已经体现趋势，部分供应链公司还没有完全补涨。

### 3.2 本次交易的核心判断

我们买的不是“便宜股”，而是 **被 AI 基建需求拉动、并且还存在补涨空间的瓶颈股**。

这类股票通常有四个条件：

- 产业链关系真实；
- 有 source-backed evidence；
- 当前价格还没有完全反映 leader 的涨幅；
- 风险没有大到抵消 upside。

本次 CRDO、ANET、COHR、ETN 通过了这一套逻辑，但证据还不是 full confirmed，所以仓位被限制在 EARLY_PARTIAL cap 内。

---

## 4. Why Buy / 为什么买入

## 4.1 CRDO — BUY_CANDIDATE

### AI role / AI 产业角色

CRDO 是高速互连和连接方案公司，重点受益于 AI 数据中心内部更高带宽、更低功耗、更高可靠性的连接需求。

在 AI 集群里，GPU 不是孤立工作的。GPU、交换机、服务器、机柜之间需要大量高速连接。模型越大，通信越重要。CRDO 的投资逻辑就来自这个“AI 集群互连瓶颈”。

### Why buy now / 为什么现在买

CRDO 的最终评分是本轮最高：

- Score: `85`
- Action: `BUY_CANDIDATE`
- Evidence stage: `EARLY_PARTIAL`
- Allocation cap: `15%`

买入理由：

1. **直接靠近 AI 网络瓶颈**：它不是远端泛科技公司，而是连接层受益者；
2. **和 MRVL 的 AI networking ROCKET area 匹配**：MRVL 的强势说明市场在重估 AI 网络链条；
3. **catch-up gap 明显**：leader 先动，CRDO 仍有补涨空间；
4. **成长性和毛利证据较强**：这让它不仅是概念股，而是有商业质量支撑；
5. **模型认为它是本轮最强 BUY_CANDIDATE**。

### Evidence / 证据强度

证据不是空白，但还不是完整确认。

- 正面：AI interconnect / networking 方向明确；
- 正面：source-backed evidence 支持它是瓶颈受益者；
- 限制：证据状态仍为 `EARLY_PARTIAL`；
- 限制：客户集中和估值波动风险需要控制。

### Why capped / 为什么只能 capped buy

CRDO 不是“不确定所以不买”，而是：

> 证据足够让我们早买，但还不够让我们满仓买。

因此使用 15% cap。这个规则很重要：早期机会的收益来自“市场还没完全定价”，但早期证据也意味着错误概率更高。

### Sell / kill trigger / 什么时候卖出或认错

如果出现以下情况，应减仓或退出：

- AI 互连需求没有转化为订单/收入；
- 大客户集中风险恶化；
- 竞争对手压价导致毛利率下滑；
- 股价快速补涨后，risk/reward 不再优于其他候选；
- evidence profile 从 PARTIAL 改为负面或 kill trigger 被触发。

---

## 4.2 ANET — BUY_CANDIDATE

### AI role / AI 产业角色

ANET 是 AI 数据中心网络交换机的重要受益者。AI 训练和推理集群会产生大量东西向流量，数据中心网络从传统 enterprise networking 变成 AI 基建核心层。

如果 AI 算力继续扩张，网络不是可选项，而是基础设施瓶颈。

### Why buy now / 为什么现在买

ANET 的本轮评分：

- Score: `84`
- Action: `BUY_CANDIDATE`
- Evidence stage: `EARLY_PARTIAL`
- Allocation cap: `15%`

买入理由：

1. **AI 网络需求是直接需求，不是叙事外溢**；
2. **和 MRVL 的 AI networking 信号同方向**；
3. **ANET 拥有高质量业务和强利润率**；
4. **AI Ethernet / switching 方向有持续重估空间**；
5. **相对 leader 仍有 catch-up 机会**。

### Evidence / 证据强度

ANET 的优势是业务质量更强、确定性更高；不足是短期弹性可能不如 CRDO。

- 正面：AI networking 逻辑直接；
- 正面：利润率和客户质量较好；
- 正面：模型给出 BUY_CANDIDATE；
- 限制：仍为 EARLY_PARTIAL，说明具体客户/订单级证据不够完整；
- 限制：大盘网络设备估值可能已经部分反映 AI 预期。

### Why capped / 为什么 capped buy

ANET 质量高，但这次不是 full-confirmed trade。证据仍然处于 early partial，因此不能突破 cap。

这里的核心不是“ANET 不好”，而是：

> 好公司也不能无视证据等级和仓位纪律。

### Sell / kill trigger / 什么时候卖出或减仓

- AI 网络订单增长低于预期；
- 云厂商 capex 放缓；
- 毛利率被竞争或产品组合拖累；
- 估值过高而增长没有同步上修；
- CRDO/COHR/ETN 等更高弹性的瓶颈股出现更强 risk/reward。

---

## 4.3 ETN — EARLY_BUY

### AI role / AI 产业角色

ETN 是数据中心电气基础设施受益者。AI 数据中心不是普通机房，它对电力密度、配电、保护、电源管理和系统可靠性要求更高。

AI capex 的一部分最终会流向电力基础设施，而 ETN 处在这个链条里。

### Why buy now / 为什么现在买

ETN 的本轮评分：

- Score: `79`
- Action: `EARLY_BUY`
- Evidence stage: `EARLY_PARTIAL`
- Allocation cap: `15%`

买入理由：

1. **AI 数据中心电力是现实瓶颈**；
2. **HPE 的 AI server / datacenter buildout 信号支持电力基础设施需求**；
3. **ETN 不是直接服务器供应商，而是 parallel bottleneck beneficiary**；
4. **20d catch-up gap 大，说明还存在补涨空间**；
5. **低稀释风险让它适合做更稳健的 AI 基建敞口**。

### Evidence / 证据强度

ETN 的证据质量不如直接 AI networking 股票强，但产业逻辑扎实。

- 正面：数据中心电力需求是真瓶颈；
- 正面：AI capex 对电气设备有持续拉动；
- 限制：缺少足够明确的 named AI customer proof；
- 限制：不是纯 AI 股票，弹性可能较低；
- 限制：如果市场重新偏好高 beta AI 股，ETN 可能跑输 CRDO。

### Why capped / 为什么 capped buy

ETN 是 EARLY_BUY，不是 full BUY_CANDIDATE。它是 AI 基建电力方向的合理早期配置，但不能当成最高弹性的 AI 网络股处理。

仓位上限保护我们避免把“基础设施受益”误判成“直接 AI 爆发”。

### Sell / kill trigger / 什么时候卖出或减仓

- 数据中心订单增长没有兑现；
- 电力设备周期见顶；
- AI capex 转向但没有传导到电气设备；
- 更直接的 AI 瓶颈股出现更高评分；
- 股价补涨后不再有 catch-up gap。

---

## 4.4 COHR — EARLY_BUY

### AI role / AI 产业角色

COHR 受益于 optical / photonics 方向。AI 集群规模扩大后，光互连、光模块、激光器和相关材料/组件的重要性上升。

如果 AI 数据中心需要更高带宽和更长距离连接，光学链条会成为关键受益环节。

### Why buy now / 为什么现在买

COHR 的本轮评分：

- Score: `82`
- Action: `EARLY_BUY`
- Evidence stage: `EARLY_PARTIAL`
- Allocation cap: `15%`

买入理由：

1. **AI optical / photonics 是 MRVL networking 逻辑的自然延伸**；
2. **COHR 与光模块、激光和光通信瓶颈相关**；
3. **有 Nvidia-linked strategic evidence 支撑关注度**；
4. **20d catch-up gap 仍有吸引力**；
5. **适合作为 CRDO/ANET 之外的光学链条补充仓位**。

### Evidence / 证据强度

COHR 的产业链方向对，但风险也更明显。

- 正面：光互连是 AI 数据中心真实需求；
- 正面：模型给出 EARLY_BUY；
- 正面：相对 MRVL 方向有补涨逻辑；
- 限制：商业合同和收入拆分证据不够完整；
- 限制：估值和周期性风险不能忽略。

### Why capped / 为什么 capped buy

COHR 是值得买的早期光学瓶颈股，但不是无上限加仓。

原因：

- 证据仍为 EARLY_PARTIAL；
- 光学链条弹性大，但波动也大；
- 合同质量和收入兑现还需要继续跟踪。

### Sell / kill trigger / 什么时候卖出或减仓

- 光学订单没有兑现为收入增长；
- AI optical 叙事降温；
- 毛利率被竞争拖累；
- 出现更直接、更强证据的 optical/interconnect 标的；
- 股价补涨后 cap 已满且 risk/reward 下降。

---

## 5. Why Sell / 为什么卖出或调仓

卖出部分最容易误解。这里不是说 LRCX、KLAC、AMAT、MRVL 是坏公司。

更准确的说法是：

> 在当前 run 的证据、评分和仓位约束下，它们的边际资金吸引力低于 CRDO、ANET、ETN、COHR。

投资组合不是选“好公司”，而是选“当前最值得占用资金的机会”。

---

## 5.1 LRCX — SELL / ROTATE

### Why sell / 为什么卖

LRCX 是优秀的半导体设备公司，也长期受益于存储、晶圆制造和先进制程投资。但本次交易里，LRCX 不是最靠近当前 AI 网络/光互连/电力瓶颈的标的。

卖出理由：

1. **当前 AI bottleneck 不在传统设备链条最前端**；
2. **CRDO/ANET/COHR/ETN 更直接对应本次 MRVL/HPE 信号**；
3. **资金有限，必须从低评分机会转向高评分机会**；
4. **LRCX 当前不是本次 run 的 top buy candidate**。

### What changed / 什么发生了变化

不是 LRCX 基本面突然变差，而是本次 AI 行业信号更集中在：

- AI networking；
- interconnect；
- optical；
- data center power。

因此 LRCX 的机会成本上升。

### What would make us buy again / 什么情况下重新买

- 存储/先进制程设备出现新的 ROCKET leader；
- LRCX 出现明确 AI-driven order/backlog evidence；
- 半导体设备链条相对 AI 网络股重新出现更大 catch-up gap；
- 模型评分重新超过当前候选股。

---

## 5.2 KLAC — SELL / ROTATE

### Why sell / 为什么卖

KLAC 是高质量的半导体检测/量测设备公司，商业质量很强。但本次交易目标不是长期质量因子，而是短中期 AI bottleneck catch-up。

卖出理由：

1. **KLAC 更偏半导体资本开支周期，不是本次最直接 AI 网络瓶颈**；
2. **当前资金需要用于更高评分的 AI catch-up 标的**；
3. **KLAC 的相对 upside 不如 CRDO/ANET/ETN/COHR**；
4. **如果继续持有，会挤压更强机会的仓位**。

### Important nuance / 重要区别

卖 KLAC 不是因为它差，而是因为组合里每一美元都应该给当前 expected return 更高的机会。

这就是专业投资和“喜欢好公司”的区别：

> 好公司不等于当前最好的仓位。

### What would make us buy again / 什么情况下重新买

- AI 芯片制造/先进封装检测成为新的市场焦点；
- KLAC 订单或指引明显受 AI capex 拉动；
- 当前 networking/power 标的补涨后，KLAC 出现更好的相对 risk/reward；
- 回调后估值更有吸引力。

---

## 5.3 AMAT — SELL / ROTATE

### Why sell / 为什么卖

AMAT 是半导体设备龙头之一，长期 AI 资本开支会间接受益。但本次报告聚焦的是更近端、更直接的 AI 基建瓶颈。

卖出理由：

1. **AMAT 的 AI 关系更宽、更间接**；
2. **本次买入候选的因果链更具体**；
3. **AMAT 没有进入本轮最高评分机会组**；
4. **卖出 AMAT 可以释放资金给 CRDO/ANET/ETN/COHR**。

### Opportunity cost / 机会成本

如果资金留在 AMAT，就不能买更多当前评分更高的 AI 网络和电力瓶颈股。

本次组合优化的核心是：

```text
卖出较宽泛 AI 受益
→ 买入更直接瓶颈受益
```

### What would make us buy again / 什么情况下重新买

- AI 设备 capex 重新成为最强主线；
- AMAT 出现强劲订单/指引上修；
- 网络/光学/电力股估值过热，而设备股重新出现补涨空间。

---

## 5.4 MRVL — TRIM / ROTATE

### Why trim / 为什么减仓

MRVL 是本次重要的 AI networking signal node，但 signal node 不一定总是最好的新增买入对象。

MRVL 的作用是帮助我们发现：

- AI networking 正在被市场重估；
- 光互连和高速连接链条值得研究；
- 下游/平行供应链可能存在 catch-up 机会。

但本次模型对 MRVL 本身的处理更谨慎：

- MRVL 已经是 leader / self-rocket；
- 它的部分机会可能已经被市场定价；
- 更强的新增资金机会在 CRDO、ANET、COHR、ETN；
- 因此不是清仓，而是 trim/rotate。

### Why not fully sell / 为什么不是全卖

MRVL 仍然是 AI networking 的核心信号股。完全卖出可能错过 leader 继续上涨。

更合理的是：

> 保留部分 MRVL 作为 Level 0 / signal exposure，同时把一部分利润或资金转向更有 catch-up gap 的 Level 1/2/3 受益者。

### What would make us add again / 什么情况下重新加仓

- MRVL 的 AI revenue/backlog 继续上修；
- 股价回调但 AI thesis 没坏；
- MRVL 相对 CRDO/ANET 重新出现更好 risk/reward；
- evidence profile 从 early/partial 变成更完整确认。

---

## 6. Exact Trades / 精确交易

### SELL

| Ticker | Shares | Price | Amount | Rationale |
|---|---:|---:|---:|---|
| AMAT | 1.6129 | 458.17 | 739.00 | 从较宽泛设备敞口转向更直接 AI 瓶颈股 |
| KLAC | 0.5252 | 1940.04 | 1019.00 | 释放资金给更高评分的 AI catch-up 候选 |
| LRCX | 3.9323 | 317.12 | 1247.00 | 从非最高评分设备仓位轮动到网络/光学/电力瓶颈 |
| MRVL | 2.7606 | 219.43 | 605.75 | trim leader/signal node，转向补涨空间更大的候选 |

SELL total: **3610.75**

### BUY

| Ticker | Shares | Price | Amount | Rationale |
|---|---:|---:|---:|---|
| CRDO | 5.0135 | 206.89 | 1037.25 | 最高评分 AI interconnect / networking catch-up 标的 |
| ANET | 6.7236 | 154.27 | 1037.25 | 高质量 AI networking / switching 受益者 |
| ETN | 2.6197 | 395.94 | 1037.25 | AI 数据中心电力基础设施 early buy |
| COHR | 1.3702 | 362.90 | 497.25 | optical / photonics 方向 capped early buy |

BUY total: **3609.00**

Net idle cash created: **约 1.75**

---

## 7. Decision Rules / 如何判断买卖

### 7.1 Buy rules / 买入规则

我会买入一只 AI 股票，通常需要满足以下条件：

1. **真实 AI 因果链**

不是“公司提到了 AI”就买，而是要能画出链条：

```text
AI demand
→ infrastructure requirement
→ bottleneck
→ company product
→ revenue / margin / valuation impact
```

2. **ROCKET leader 提供方向信号**

先找已经被市场重估的 leader，再找还没有完全补涨的供应链/平行瓶颈公司。

3. **公司处在 Level 1/2/3 catch-up 位置**

优先买：

- 直接供应商；
- 平行瓶颈受益者；
- 光学、网络、电力、冷却、材料、先进封装等真实约束环节。

4. **Evidence source-backed**

必须有来源支撑。没有来源的 AI 故事只能 monitor，不能 buy。

5. **价格还没有完全补涨**

如果 leader 已经涨了，但 candidate 还没有完全跟上，才有 catch-up return。

6. **仓位由证据质量决定**

- COMPLETE evidence: 可以更大仓位；
- EARLY_PARTIAL evidence: 可以早买，但必须 capped；
- UNSOURCED / UNVERIFIED: 不买。

### 7.2 Sell rules / 卖出规则

卖出不是只因为亏损，也不是只因为公司不好。专业组合管理里，卖出通常有五种原因：

1. **Opportunity cost / 机会成本**

如果出现更高 expected return 的股票，就应该从低评分仓位释放资金。

2. **Thesis weakened / 逻辑变弱**

如果 AI demand 没有转化为订单、收入、backlog 或毛利，说明 thesis 可能只是叙事。

3. **Position above evidence cap / 仓位超过证据上限**

早期证据只能支持早期仓位。仓位超过证据等级，就是风险失控。

4. **Leader priced in / leader 已经充分定价**

ROCKET leader 很重要，但如果 leader 已经涨很多，新增资金可能应该去 Level 1/2/3 catch-up。

5. **Better bottleneck found / 找到更直接瓶颈**

如果从“泛 AI 受益”切换到“直接 AI 瓶颈”，组合应该跟着切换。

---

## 8. Portfolio Logic / 本次组合逻辑

本次不是简单买四只股票、卖四只股票，而是一次结构升级：

```text
旧组合重点：
半导体设备 + MRVL leader exposure

新组合重点：
AI networking + interconnect + optical + data center power
```

旧组合问题：

- 好公司多，但不一定都是当前最强 AI 瓶颈；
- 部分仓位偏泛半导体 capex；
- MRVL 作为 leader 已经部分反映 AI networking 预期。

新组合优势：

- 更贴近当前 AI infrastructure bottleneck；
- 更符合 ROCKET leader → catch-up candidate 的策略；
- 用 capped sizing 控制早期证据风险；
- 买入标的之间覆盖不同瓶颈：interconnect、networking、optical、power。

---

## 9. Ranking / 本次候选排序

| Rank | Ticker | Action | Score | Evidence Stage | Cap | 核心原因 |
|---:|---|---|---:|---|---:|---|
| 1 | CRDO | BUY_CANDIDATE | 85 | EARLY_PARTIAL | 15% | AI 高速互连，弹性最高 |
| 2 | ANET | BUY_CANDIDATE | 84 | EARLY_PARTIAL | 15% | AI 网络交换机，高质量业务 |
| 3 | COHR | EARLY_BUY | 82 | EARLY_PARTIAL | 15% | optical / photonics 补涨 |
| 4 | ETN | EARLY_BUY | 79 | EARLY_PARTIAL | 15% | 数据中心电力基础设施 |
| Watch | VRT | EARLY_BUY | 83-84 | EARLY_PARTIAL | 15% | 已高于 cap，不继续加仓 |

注意：VRT 仍是强 AI power/cooling thesis，但已有仓位高于 early partial cap，所以本次不新增买入。

---

## 10. Key Lessons / 这次你应该学到什么

### Lesson 1: AI 股票不是一个板块，而是一条瓶颈链

“AI 相关”太宽。真正重要的是：AI 需求是否制造了真实的供需瓶颈。

CRDO、ANET、COHR、ETN 的共同点不是行业分类相同，而是都连接到 AI 基建扩张的不同瓶颈。

### Lesson 2: ROCKET leader 是信号，不一定是最终买入对象

MRVL 强，不代表只买 MRVL。它告诉我们 AI networking 被重估，然后我们寻找更有补涨空间的供应链或平行瓶颈公司。

### Lesson 3: 早期证据可以买，但必须控制仓位

如果等证据完全确认，市场可能已经涨完；如果完全不等证据，容易买到叙事股。

正确做法是：

```text
早期 source-backed evidence
→ capped buy
→ 后续证据增强再加仓
→ 证据失败就退出
```

### Lesson 4: 卖出好公司是正常的

LRCX、KLAC、AMAT 都不是垃圾公司。卖出它们的原因是：当前资金应该给更贴近 AI 网络/光学/电力瓶颈的机会。

组合管理的关键不是“我喜不喜欢这家公司”，而是：

> 这家公司现在是不是组合里每一美元的最佳用途？

### Lesson 5: 买卖决策必须同时看产业、证据、价格和仓位

只看产业会买到故事；只看价格会错过结构性机会；只看证据会太晚；只看 momentum 会追高。

更好的框架是：

```text
产业链因果
+ source-backed evidence
+ catch-up gap
+ 风险/估值
+ 仓位 cap
= 交易决策
```

---

## 11. Next Monitoring Checklist / 后续跟踪清单

后续重点看这些信号：

### CRDO

- 是否继续出现 AI interconnect / AEC 订单证据；
- 是否有大客户集中风险恶化；
- 毛利率是否保持强势；
- 股价补涨后是否接近 cap 上限。

### ANET

- 云厂商 AI networking capex 是否继续上修；
- Ethernet AI networking 叙事是否增强；
- 毛利率和订单是否支撑估值。

### ETN

- 数据中心电力订单、backlog 是否继续增长；
- AI 数据中心供电瓶颈是否继续成为新闻和财报主题；
- 是否有更直接 power/cooling 标的超过 ETN。

### COHR

- 光模块、激光、photonics 需求是否转化为收入；
- Nvidia-linked / hyperscaler-linked 证据是否增强；
- 毛利率和债务/周期风险是否可控。

### 卖出标的

- LRCX / KLAC / AMAT 是否重新出现 AI 设备链条的 ROCKET 信号；
- MRVL 是否继续上修 AI revenue/backlog；
- 如果旧标的重新超过新标的评分，可以再买回来。

---

## 12. Final View / 最终观点

我支持这次轮动。

原因不是“CRDO、ANET、ETN、COHR 一定会涨”，而是：在当前 AI 基建信号下，它们比 LRCX、KLAC、AMAT 和部分 MRVL 仓位更接近被市场重新定价的瓶颈链条。

本次最强买入是 CRDO 和 ANET：

- CRDO 更有弹性；
- ANET 质量更稳；
- ETN 提供电力基础设施敞口；
- COHR 提供 optical / photonics 敞口。

本次最重要的纪律是：

> 这些买入是 early evidence trade，不是 full conviction all-in trade。我们买，是因为不想等市场完全定价；我们 capped，是因为证据还没有完全确认。

这就是本次交易的核心。
