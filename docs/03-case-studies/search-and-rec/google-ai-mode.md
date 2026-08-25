---
title: google-ai-mode
type: note
permalink: aipmwiki/docs/03-case-studies/search-and-rec/google-ai-mode
---

# Google AI Overviews 与 AI Mode:传统搜索的答案层防御

**分类**:搜推
**体验时间**:2026 年 7 月

## 一、产品背景

Google Search 面对的不是"要不要做 AI 搜索",而是**如何在保留蓝链索引与广告机器的同时,把答案层嵌回自己的结果页**。Perplexity、ChatGPT Search 把"直接给答案"做成独立入口后,用户不必再点十条链接——这对 Google 既是流量分流,也是广告库存被绕开的风险。

Google 的应对不是另起一个答案引擎品牌,而是在既有 Search 上叠两层产品:

| 产品面 | 定位 | 触发方式 |
|--------|------|----------|
| **AI Overviews** | 结果页顶部的 AI 摘要层 | 系统判断"生成式回答特别有帮助"时自动出现,用户通常无法关闭 |
| **AI Mode** | 对话式、可追问的"最强 AI 搜索" | 主动进入 Search 里的 AI Mode Tab / 应用内入口;可多轮追问、更复杂推理 |

时间线(以官方博客为准):

- **2024 I/O**:AI Overviews 正式走向主流搜索体验。
- **2025-03**:AI Overviews 升级 Gemini 2.0;Labs 引入 AI Mode 实验([Expanding AI Overviews and introducing AI Mode](https://blog.google/products-and-platforms/products/search/ai-mode-search/))。
- **2025-05 I/O**:AI Mode 在美国向用户开放(无需 Labs);Deep Search、Live、购物与 agentic 能力路线图公开([AI in Search: Going beyond information to intelligence](https://blog.google/products-and-platforms/products/search/google-search-ai-mode-update/))。
- **2025-05**:官方开始测试/扩展 Ads in AI Overviews,并宣布测试 Ads in AI Mode([New ways AI in Search helps your business](https://blog.google/products/ads-commerce/google-search-ai-brand-discovery/))。
- **2026-05 I/O**:官方称 AI Mode 已超过 **10 亿月用户**(monthly users),默认模型升级为 Gemini 3.5 Flash;并推进 Overviews 与 Mode 的无缝衔接([100 things we announced at Google I/O 2026](https://blog.google/innovation-and-ai/technology/ai/google-io-2026-all-our-announcements/))。
- **2026-05 Marketing Live**:在 AI Mode 内测试 Conversational Discovery ads、Highlighted Answers 等 Gemini 驱动广告形态([A new generation of ads for the AI era of Search](https://blog.google/products/ads-commerce/google-marketing-live-search-ads/))。

**可核实的规模口径**(不要与第三方"覆盖率/渗透率"混用):

- Google 称全球搜索量超过 **5 万亿次/年**(内部数据,2025-01 口径,见广告商业博客脚注)。
- AI Overviews:2025-03 博客写"used by more than a billion people"(使用过的人数口径,不是月活定义);美/印等大市场,对会展示 Overview 的那类查询,实验显示 Google 使用量提升超过 **10%**。
- AI Mode:2026-05 I/O 官方清单写明 **surpassed more than 1 billion monthly users**;并称上线以来查询量每季度翻倍以上。Labs 早期反馈称 AI Mode 查询平均长度约为传统搜索的 **2 倍**。

一句话定位:这是**垄断搜索入口上的答案层防御**——先用 Overviews 守住"第一屏答案",再用 Mode 承接长问与多轮,同时把广告库存迁进答案面,而不是像 Perplexity 那样用弃广告换信任定位。

## 二、核心功能拆解

### 2.1 双产品面如何分工

| 环节 | AI Overviews | AI Mode |
|------|----------------|---------|
| 入口 | 普通搜索结果页顶部 | Search 内独立 Tab / 对话式界面 |
| 典型问题 | 需要综合多源的"概览型"问题 | 多部分、比较、推理、需追问的复杂问题 |
| 交互深度 | 单次摘要 + 链回网页 | 多轮追问、Deep Search、多模态/Live 等能力先发 |
| 与网页关系 | 摘要下方/旁侧保留网页探索 | 官方强调仍提供"helpful links to the web" |
| 广告位置 | 可出现在 Overview **上方、下方或内部** | 相关时广告可出现在回答**下方或嵌入回答** |

主流程(Overviews):用户键入查询 → 排序系统判定是否展示生成式概览 → Gemini 定制版本综合多源 → 顶部给出摘要并链到网页 → 若有商业意图且广告质量达标,Text/Shopping 广告可出现在 Overview 上下或内部。

主流程(AI Mode):用户主动进入 Mode → 用自然语言提出更长问题 → **query fan-out**(把问题拆成子题、并行发出大量相关检索)→ 综合 Knowledge Graph、实时信息、购物图谱等 → 生成可追问的回答 → 用户继续追问或跳转网页/购物/预订类动作。

### 2.2 官方已点名的关键能力

- **Query fan-out**:AI Mode 把复杂问题拆成子主题并并发检索,官方称这比"一次传统搜索"能覆盖更深更广的网页内容。
- **Deep Search**:在 Mode 内把 fan-out 推到更极端(官方描述可发起数百次检索),生成带引用的研究报告级输出。
- **多模态与 Live**:Lens / Project Astra 路线进入 Search——对所见画面实时问答(能力按市场分阶段推出)。
- **购物与 agentic**:结合 Shopping Graph 做灵感浏览、虚拟试穿、条件满足时用 Google Pay 代购等;票务/餐厅等场景与 Ticketmaster、Resy 等合作做填表与履约辅助(路线图能力,落地节奏因市场而异)。
- **个人上下文**:可选择连接 Gmail 等应用,让行程、偏好进入回答;官方强调可开关、可见提示。

对 PM 而言,真正重要的产品决策不是"会不会聊天",而是:**答案层默认自动出现(Overviews) vs 用户主动切换(Mode)**。前者守分发与广告位,后者承接"愿意多写几句"的高意图用户,并把前沿 Gemini 能力先放在 Mode 再回灌核心 Search。

## 三、技术方案推测

> 以下基于 Google 官方博客的公开描述 + 行业通行做法进行**推测**,内部索引/拍卖细节未完整公开。

- **生成式层叠在经典检索之上(部分已知)**:官方反复强调 AI Mode / Overviews"built right into Search",并接入 Knowledge Graph、实时源、购物数据。可推测流水线是:查询理解 →(Mode)规划与 fan-out → 多源检索与片段排序 → 定制 Gemini 生成 → 网页链接/卡片回填;置信不足时退回传统结果列表(官方在 Labs 阶段即写明这一点)。
- **模型侧(部分已知)**:Overviews/Mode 使用"定制版 Gemini"(2025 先后出现 2.0 / 2.5;2026-05 起 Mode 全球默认 Gemini 3.5 Flash)。推测存在按查询难度/模式的路由,而非单一模型包打天下。
- **广告匹配(Ads Help 已披露逻辑)**:Overview 内广告同时参考**用户查询**与 **Overview 正文内容**;需能赢拍卖且与两者都相关。因此更依赖 broad match、AI Max、Performance Max、Shopping 等"意图覆盖型"投放,而不是只买精确关键词。敏感垂类(成人、酒精、博彩、金融、医疗、政治等)当前不在 Overview 内投广告。
- **事实性与质量控制(推测)**:官方提到用模型推理能力改善 factuality,并在低置信时不硬出 AI 回答。具体如何把"检索证据"与"生成句"对齐,公开材料不足以还原,只能视为与 RAG + 置信度门槛相近的工程组合。

## 四、商业模式:广告迁入答案面

Google Search 的商业内核仍是广告。答案层若只摘要、不点击,会压缩自然结果与部分广告的曝光路径——因此 Google 的选择与 Perplexity **相反**:不是撤广告,而是**把广告库存搬进答案面**,并宣称这能缩短"发现 → 决策"路径。

### 4.1 Ads in AI Overviews(Ads Help,核实截至 2026-07)

据 [About ads and AI Overviews](https://support.google.com/google-ads/answer/16297775):

- 广告可出现在 AI Overviews **上方、下方或内部**。
- **上下方广告**:在已上线 Overview 的 200+ 市场可用;沿用既有拍卖与信号;Text / Shopping / Local / App 等既有广告形态可参与。
- **Overview 内部广告**:英文、移动+桌面,覆盖澳大利亚、加拿大、印度、印尼、肯尼亚、马来西亚、新西兰、尼日利亚、巴基斯坦、菲律宾、新加坡、美国等(以 Help 页当前列表为准)。
- 合格广告类型:现有 Search、Shopping、Performance Max 活动中的 Text 与 Shopping 广告。
- 广告主**不能**单独定向"只要 Overview 内广告",也**不能** opt-out;报表上 Overview 内广告计入 Top Ads,细分报表仍在演进。

### 4.2 Ads in AI Mode

- 2025-05 商业博客:开始测试 AI Mode 内广告,相关时可出现在回答下方或嵌入回答;已使用 PMax / Shopping / broad match Search(含 AI Max)的广告主自动具备资格。
- 2026 Marketing Live:测试 **Conversational Discovery ads**(广告直接回答具体问题)与 **Highlighted Answers**(推荐列表中的赞助项);广告旁有 Gemini 生成的独立解释层,并继续标注 Sponsored。另有 AI-powered Shopping ads、Business Agent for Leads、Direct Offers 等与对话式搜索衔接的试点。

### 4.3 与 Perplexity"弃广告"的对照

| 维度 | Google AI Overviews / AI Mode | Perplexity(本目录姊妹案例) |
|------|-------------------------------|------------------------------|
| 产品命题 | 守住搜索入口与广告机器 | 做可信答案引擎 |
| 答案与广告 | 广告迁入答案面,标 Sponsored,强调"helpful next step" | 2026-02 起公开放弃广告,理由是损害答案信任 |
| 变现主轴 | 搜索广告(+购物广告新形态) | 订阅 + 内容分成探索 |
| 信任策略 | 用标注、敏感垂类限制、"独立 AI 解释"降低广告污染感 | 用无广告本身做差异化卖点 |
| 分发 | 默认嵌在全球最大搜索入口 | 独立站 + Comet 浏览器抢入口 |

这对做搜推的 PM 是一条清晰分叉:**入口方**(Google)必须回答"答案层如何继续卖广告";**信任方**(Perplexity)必须回答"没有广告时收入从哪来"。两边都合理,但不可互相抄作业而不改 KPI。

## 五、PM 视角的启示

1. **答案层防御的第一原则是"嵌回入口",而不是另开品牌。** Google 把 Overviews 自动塞进结果页、把 Mode 做成 Search 内 Tab,本质上是用分发特权对冲"答案引擎"分流。做平台型搜推时,先问自己有没有可嵌的超级入口;没有入口,再谈引用体验与订阅。
2. **广告能否进答案面,取决于你卖的是"点击"还是"信任"。** Google 的 KPI 是发现到转化的商业路径与广告主库存;Perplexity 的 KPI 是用户是否相信这句话。同一交互(答案旁出现商品)在两边含义完全不同——写 PRD 前先写清北极星,再决定广告是一等公民还是禁区。
3. **双表面比单表面更适合渐进迁移。** Overviews 守覆盖与广告位,Mode 做能力试验田再回灌核心 Search——这是大公司改交互范式时常见的"默认层 + 进阶层"组合。中小团队若只有一个表面,就要更狠地做场景取舍,否则既不像链接搜索也不像对话助手。

## 相关阅读

- [Perplexity 案例拆解:AI 搜索的答案引擎范式](perplexity.md)
- [什么是 RAG:检索增强生成入门](../../01-ai-basics/llm/what-is-rag.md)
- [AI 搜索的北极星指标怎么定](../../04-interview/case-analysis/ai-search-north-star-metric.md)
- [如何分析一个优秀的 AI 产品](../../04-interview/case-analysis/analyze-a-good-ai-product.md)

## 参考资料

- [Expanding AI Overviews and introducing AI Mode](https://blog.google/products-and-platforms/products/search/ai-mode-search/) — Google Blog,2025-03;2026-07 访问
- [AI in Search: Going beyond information to intelligence](https://blog.google/products-and-platforms/products/search/google-search-ai-mode-update/) — Google Blog / I/O 2025,2025-05;2026-07 访问
- [New ways to interact with information in AI Mode](https://blog.google/products-and-platforms/products/search/ai-mode-updates-may-2025/) — Google Blog,2025-05;2026-07 访问
- [100 things we announced at Google I/O 2026](https://blog.google/innovation-and-ai/technology/ai/google-io-2026-all-our-announcements/) — Google Blog,2026-05;含 AI Mode 10 亿月用户口径;2026-07 访问
- [New ways AI in Search helps your business](https://blog.google/products/ads-commerce/google-search-ai-brand-discovery/) — Google Ads Blog,2025-05;2026-07 访问
- [A new generation of ads for the AI era of Search](https://blog.google/products/ads-commerce/google-marketing-live-search-ads/) — Google Ads Blog / Marketing Live,2026-05;2026-07 访问
- [About ads and AI Overviews](https://support.google.com/google-ads/answer/16297775) — Google Ads Help;2026-07 访问
- [Ads in AI Mode](https://business.google.com/us/accelerate/announcements/ads-in-ai-mode/) — Google Business / Accelerate;2026-07 访问