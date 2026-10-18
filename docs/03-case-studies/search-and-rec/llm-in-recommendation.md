---
title: llm-in-recommendation
type: note
permalink: aipmwiki/docs/03-case-studies/search-and-rec/llm-in-recommendation
---

# 推荐系统中大模型的实际落地位置

**分类**:搜推
**体验时间**:2026 年 7 月（历史案例）；**动态核验**:2026-09-14

## 一、产品背景

「大模型要不要接管推荐」这类问题,容易把讨论拉成二选一:要么整条漏斗换生成模型,要么 LLM 只是聊天气泡。产业公开材料给出的答案更务实——**LLM / 生成式模型很少整条替换经典推荐漏斗**;更常见的是嵌进漏斗的特定层,或在电商场景做成**导购 / 解释 / Agent 动作层**。

本篇不是单品传记,而是**落地位置地图**。默认框架应写成:

> **经典检索 / 推荐中台 + LLM 决策 / 解释 / Agent 层**;另有一条工业线是 **generative retrieval / 整页生成** 进入召回或首页构建。

两条叙事线不要混成同一产品形态:

| 叙事线 | 典型场景 | 公开证据更硬的代表 | 北极星大致是什么 |
|--------|----------|--------------------|------------------|
| **线 A:对话导购 + 解释 + Agent** | 电商站内问购、对比、盯价、代购 | Amazon Alexa for Shopping(Rufus 演进) | 转化、增量销售 |
| **线 B:生成式召回 / 整页生成** | 内容 / 社交 feed、首页行构造 | Pinterest PinRec;Netflix GenPage;Spotify Semantic IDs(研究偏重) | engagement、延迟、跨任务表征 |

开放 AI 搜索(如 Perplexity)与传统搜索的答案层防御(如 Google AI Mode)回答的是「先给答案还是先给蓝链」;电商助手回答的是「能不能促成下单」。推荐链路里的 LLM 落点,同时横跨这两种逻辑——有时是答案式解释,有时是召回候选生成,有时是可执行 Agent。写 PRD 时先分清自己在哪一层,比先选「用哪个大模型」更重要。

## 二、核心功能拆解:按落地层看 LLM 做什么

### 2.1 落地层总览

| 落地层 | 典型做什么 | 公开证据强度 | 代表与备注 |
|--------|------------|--------------|------------|
| **对话导购 / 意图理解** | 自然语言问购、多轮澄清、搜索框「问题 vs 关键词」分流 | 高 | Amazon:主搜索框问答、专用聊天 |
| **解释 / 决策辅助** | 品类/商详 AI Overview、评论摘要、多 SKU 对比、「Help Me Decide」 | 高 | Amazon 搜推表面;不是核心排序替换 |
| **Agent 动作闭环** | 盯价、加购、Scheduled Actions、Buy for Me | 高 | Amazon agentic shopping |
| **候选生成(generative retrieval)** | 用 Transformer 生成 item Semantic ID / 候选序列,补强或替换部分 ANN/双塔召回 | 中高 | Pinterest PinRec;Spotify Semantic IDs 研究 |
| **排序侧增强** | LLM 文本特征蒸馏、统一 retrieval+ranking、序列 foundation model | 中 | 工程论文可见;「LLM 当实时精排」少见同级公开声明 |
| **冷启动 / 内容理解** | 文本/多模态 embedding → Semantic ID,提升未见 item 泛化 | 中 | 研究为主;生产常作召回输入而非对话 LLM |
| **经典召回 / 精排主体** | 协同过滤、双塔、LTR、业务规则 | 仍占主流(公开叙事 + 推测) | Amazon 明示传统搜索/推荐仍在演进;LLM 是叠加体验 |

### 2.2 线 A:电商导购层(以 Amazon 为硬证据)

站内专文 [Amazon Alexa for Shopping](amazon-alexa-shopping.md) 已拆过产品面与漏斗;此处只抽**与推荐分工相关**的结论,数字一律引用该文已有公司/IR 口径:

- **2024** 推出 Rufus → **2025** 能力与财报口径挂钩 → **2026-05-13** 更名为 Alexa for Shopping(Rufus 能力 + Alexa+ 上下文)。
- 公司/IR 口径:2025 年使用过的顾客 **3 亿+**;增量年化销售近 **120 亿美元**(incremental annualized sales,勿改写成全年 GMV);购物旅程中使用助手的顾客,完成购买可能性高 **60%+**。
- LLM 主要落在:主搜索框问答、聊天窗、搜索/商详 AI Overview、多商品对比、价格历史解释、Scheduled Actions、Buy for Me 等。
- 与经典推荐的分工(该文明确):LLM 负责需求理解、对比叙述、个性化理由;**召回与排序仍重度依赖**既有商品索引、评论、价格、履约与推荐系统——「大模型是导购层,不是从零替换协同过滤」。

一条可感知的转化链路是:模糊自然语言需求 → 意图判为「问题」→ 生成对比与推荐叙述 → 用户设盯价/加购条件 → Agent 执行并经用户确认。漏斗下半段的曝光与排序,公开材料并不宣称已由对话 LLM 接管。

### 2.3 线 B:生成式召回与整页生成(内容 / feed)

电商对话助手把 LLM 放在**决策与动作表面**;内容平台的另一条线是把生成式能力压进**召回或页面构造**:

- **Pinterest PinRec**(arXiv:2504.10507):工业级 generative retrieval,outcome-conditioned 生成以对齐 click/save 等多目标。论文报告线上约 **+2% sitewide clicks**、**+4% search repins** 等影响——以论文原文为准,**勿外推全站 GMV**。
- **Netflix GenPage**(TechBlog,2026-06):用单一生成模型自回归构建整页 homepage(行 + 实体 + 布局),相对成熟多阶段系统有显著 engagement 提升,端到端 serving 延迟降低约 **20%**。这里 LLM 式生成更接近「整页编排」,而不只是单条 item 打分。
- **Spotify Semantic IDs**(Research,2025-09):在 LLM 式生成框架下用 Semantic IDs 统一搜+推的 item 表征;任务专用 ID 难跨任务泛化,multi-task bi-encoder + RQ-KMeans 可折中。偏研究结论,**未**写成 Spotify 全量生产替换声明。
- **LIGER**(arXiv:2411.18814):学术侧对比 generative vs dense sequential retrieval 并提出混合方案,强调冷启动——适合写「召回层技术方向」,**不宜**写成某 App 已上线事实。本次调研亦**未**找到 Instagram/TikTok 与 Amazon/Pinterest/Netflix 同级的「全站 LLM 精排」官方生产声明;若谈 Meta 生态,应落到研究并标明非生产确认。

### 2.4 两线对照:别用同一张产品草图

| 维度 | 线 A(电商导购/Agent) | 线 B(生成式召回/整页) |
|------|----------------------|------------------------|
| 用户感知 | 聊天、Overview、对比、盯价按钮 | feed/首页「更准/更快」,未必看到「大模型」 |
| LLM 主要产出 | 自然语言解释 + 工具调用 | item ID 序列 / 行布局 / Semantic ID |
| 与经典系统关系 | 叠加在检索与推荐中台之上 | 补强或局部替换召回/页面组装阶段 |
| 失败形态 | 答错、误导购买、Agent 误操作 | 候选质量掉点、延迟飙升、多目标失调 |
| KPI 语言 | 转化、增量销售、加购/盯价成交 | clicks、repins、engagement、serving latency |

## 三、技术方案推测

> 以下区分「公开可见的高层架构」与「细节推测」。排序公式、特征交叉、广告库存与助手推荐的边界等,公开材料通常不完整。

### 3.1 电商导购侧(部分公开 + 推测)

- **购物域定制 LLM + RAG(公开叙事)**:Amazon Science 将 Rufus 描述为购物域定制 LLM + RAG(目录/评论/社区 Q&A + Stores API)+ RL 反馈 + Inferentia/Trainium 推理;定位是回答问题与推荐,**不是**宣称替换全站协同过滤。
- **多模型路由(公开工程叙事)**:AWS 博客提到自研购物 LLM + Bedrock 多模型路由,按质量/延迟/成本选模型;上下文可注入订单史或 tool 取数——支撑「导购层 + 工具调用」架构。
- **与经典推荐的分工(推测)**:首页/相似商品等位置大概率仍由长期推荐模型主导;LLM 更常出现在高认知负荷节点(品类教育、多属性对比、模糊需求澄清)。详见站内 Alexa 案例的技术方案节。
- **Agent 层(部分可见)**:Scheduled Actions、Buy for Me 等是带确认节点的 tool-calling;关键购买步骤仍由用户复核——公开产品行为支持这一判断。
- **模型来源混合路由(推测,非官方证实)**:可能按任务类型在自研与外部模型间切换;延迟与成本约束决定能否进默认搜索框(参见站内[模型选型框架](../../02-pm-skills/cost-and-tech/model-selection-framework.md)与 [LLM 成本入门](../../02-pm-skills/cost-and-tech/llm-cost-101.md))。

### 3.2 生成式召回 / 表征侧(工程论文 + 研究)

- **Generative retrieval**:模型直接生成候选 Semantic ID 或 token 序列,再映射回 item;可与 dense/ANN 召回并存(LIGER 一类混合叙事)。
- **整页自回归生成(Netflix GenPage 公开)**:把「多阶段行推荐 + 布局」收成端到端生成,用 engagement 与 latency 验证,而不是用对话满意度验证。
- **排序侧「LLM 实时精排」(证据弱)**:公开材料更多见到特征蒸馏、统一 retrieval+ranking、序列 foundation model 叙事;**缺少**与 PinRec/GenPage 同级的「全站实时 LLM 精排打分」一线 TechBlog。写排序层时宜偏特征/蒸馏/统一模型,并标**推测**。
- **广告与有机推荐边界(评估提醒)**:Amazon 公开材料未把助手标成独立广告产品;答案里出现具体 ASIN 会改变品牌曝光,但「被说到」≠ Sponsored 库存——需单独建模,勿谈。

## 四、商业模式与 KPI:转化线 vs engagement 线

### 4.1 钱从哪来(公开能说清的部分)

- **电商导购**:助手对消费者通常免费,ROI 嵌在零售转化与履约里。Amazon 用「3 亿+ 顾客」「近 120 亿美元增量年化销售」「转化倾向高 60%+」自我证明——口径是公司/IR 披露,不是第三方估 DAU/GMV。
- **内容 / feed 生成式推荐**:商业模式仍是广告或订阅时长;公开论文/博客验证的是 clicks、repins、engagement、latency,而不是把 lift 直接翻译成收入百分比。
- **不要混用北极星**:把「引用信任」塞进电商助手,或把「增量销售」硬套到 Netflix 首页生成,都会写歪 PRD。开放 AI 搜索与电商助手的指标对照,可参考站内 [AI 搜索北极星指标](../../04-interview/case-analysis/ai-search-north-star-metric.md) 与 Alexa 案例第四节。

### 4.2 指标体系速查

| 落地层 | 更该盯的指标 | 常见误用 |
|--------|--------------|----------|
| 对话导购 / Overview | 助手渗透、进入商详、转化、退货是否恶化 | 会话轮次当唯一成功 |
| Agent 动作 | 盯价成交率、Scheduled Action 留存、代购完成率与撤销率 | 只报任务发起量 |
| Generative retrieval | 召回覆盖、多目标(click/save)、线上 A/B 的 sitewide 指标 | 把论文 lift 写成全站收入 |
| 整页生成 | engagement + serving latency + 布局多样性约束 | 只报延迟忽略内容质量 |
| 经典精排主体 | 原有 LTR / 业务 KPI | 未上线就宣称「已被 LLM 替换」 |

## 五、PM 视角的启示

1. **先画落点,再选模型。** 问清 LLM 负责的是意图理解、解释、Agent、召回候选,还是(少见公开的)实时精排。Amazon 路径证明:保留强检索/推荐/履约中台,用生成式能力啃「说不清要什么 / 属性太多比不动」的摩擦,往往比「用大模型重做推荐」更可验证。
2. **两条工业线不要抄错作业。** 做电商就深挖决策节点与动作闭环(证据看 Alexa 案例);做 feed/首页才优先研究 generative retrieval 与整页生成(PinRec / GenPage)。把 Rufus 式聊天窗直接当成召回替换方案,或把 PinRec 的 click lift 当成电商 GMV 证明,都是错位。
3. **KPI 与责任边界一起设计。** 导购层成功看增量成交与是否误导;Agent 层必须有确认、可撤销与偏好可编辑;生成式召回成功看多目标与延迟,且论文指标不可外推收入。封闭账号上下文是护城河,也放大错误推荐与错误代购的伤害。

## 相关阅读

- [Amazon Alexa for Shopping:电商推荐里的大模型落地](amazon-alexa-shopping.md)
- [AI 搜索 vs 传统搜索:交互范式与商业模式的双重颠覆](ai-search-vs-traditional-search.md)
- [Perplexity 案例拆解:AI 搜索的答案引擎范式](perplexity.md)
- [Google AI Overviews 与 AI Mode:传统搜索的答案层防御](google-ai-mode.md)
- [什么是 RAG:检索增强生成入门](../../01-ai-basics/llm/what-is-rag.md)
- [Agent 入门:规划、记忆、工具调用](../../01-ai-basics/llm/agent-basics.md)
- [AI 搜索的北极星指标怎么定](../../04-interview/case-analysis/ai-search-north-star-metric.md)
- [模型选型框架](../../02-pm-skills/cost-and-tech/model-selection-framework.md)
- [LLM 成本入门](../../02-pm-skills/cost-and-tech/llm-cost-101.md)

## 参考资料

- [Meet Alexa for Shopping, your personalized, agentic AI assistant on Amazon](https://www.aboutamazon.com/news/retail/alexa-for-shopping-ai-assistant) — About Amazon;访问日期 2026-09-14
- [How Amazon is using generative and agentic AI to transform the shopping experience](https://www.aboutamazon.com/news/retail/amazon-agentic-ai-gen-ai-shopping) — About Amazon;含更名与转化倾向等公司口径;访问日期 2026-09-14
- [Amazon.com Announces Fourth Quarter Results](https://ir.aboutamazon.com/news-release/news-release-details/2026/Amazon-com-Announces-Fourth-Quarter-Results/) — Amazon IR;Rufus 3 亿+ 顾客、近 120 亿美元增量年化销售;访问日期 2026-09-14
- [How AWS is helping retailers build their own AI-powered shopping experiences](https://www.aboutamazon.com/news/aws/aws-agentic-shopping-assistant-retailers) — About Amazon/AWS；介绍面向零售商的 Agentic Shopping Assistant 与测试/生产边界；访问日期 2026-09-14
- [The technology behind Amazon's genAI-powered shopping assistant Rufus](https://www.amazon.science/blog/the-technology-behind-amazons-genai-powered-shopping-assistant-rufus) — Amazon Science;购物域 LLM + RAG;访问日期 2026-09-14
- [How Rufus scales conversational shopping experiences… with Amazon Bedrock](https://aws.amazon.com/blogs/machine-learning/how-rufus-scales-conversational-shopping-experiences-to-millions-of-amazon-customers-with-amazon-bedrock/) — AWS ML Blog;多模型路由;访问日期 2026-09-14
- [PinRec: Outcome-Conditioned, Multi-Token Generative Retrieval…](https://arxiv.org/abs/2504.10507) — arXiv;约 +2% sitewide clicks、+4% search repins;论文实验口径，访问日期 2026-09-14
- [GenPage: Towards End-to-End Generative Homepage Construction at Netflix](https://netflixtechblog.com/genpage-towards-end-to-end-generative-homepage-construction-at-netflix-77146fba8a08) — Netflix TechBlog;serving 延迟约 -20%;工程博客口径，访问日期 2026-09-14
- [Semantic IDs for Generative Search and Recommendation](https://research.atspotify.com/2025/9/semantic-ids-for-generative-search-and-recommendation) — Spotify Research；研究文章，访问日期 2026-09-14
- [LIGER: Unifying Generative and Dense Retrieval…](https://arxiv.org/abs/2411.18814) — arXiv;混合召回与冷启动研究，访问日期 2026-09-14
