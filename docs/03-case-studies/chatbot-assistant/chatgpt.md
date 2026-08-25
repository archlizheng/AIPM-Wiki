---
title: chatgpt
type: note
permalink: aipmwiki/docs/03-case-studies/chatbot-assistant/chatgpt
---

# ChatGPT 案例拆解

**分类**:对话助手
**体验时间**:2026 年 7 月

## 一、产品背景

ChatGPT 由 OpenAI 于 2022 年 11 月上线,是这一轮生成式 AI 浪潮的起点产品,也是目前全球用户规模最大的 AI 应用,没有之一。

**用户规模**(核实截至 2026 年 7 月的最新数据):

- 周活跃用户(WAU):OpenAI 在 2026 年 2-3 月公布已突破 **9 亿**,较 2025 年 10 月披露的 8 亿又增长了约 1 亿。
- 月活跃用户(MAU):2026 年 6 月,ChatGPT App 全球月活突破 **10 亿**,是历史上最快达到这一里程碑的应用,快于 TikTok、Instagram、Google 地图。
- 增长曲线大致是:1 亿 MAU(2023)→ 4 亿 WAU → 7 亿 WAU(2025 年 7 月)→ 8 亿 WAU(2025 年 10 月)→ 9 亿 WAU(2026 年 2-3 月)。

这个体量意味着 ChatGPT 早已不是"极客玩具",而是和微信、抖音同一量级的国民级应用。它的产品策略也随之从"单点问答"演化为"承接用户几乎所有数字任务的入口"——对话、搜索、编程、购物、浏览器,全都往里装。

**竞争格局**:海外头部通用助手三分天下——OpenAI ChatGPT(用户规模最大,产品面最广)、Anthropic Claude(定位知识工作者与企业深度协作,以长文本质量和低幻觉见长)、Google Gemini(背靠 Google 全家桶的分发优势,主打长上下文与多模态推理)。国内则有豆包、Kimi、元宝等本土化竞品。ChatGPT 在中国大陆不可直接访问,但其产品逻辑仍是国内团队对标的标杆。

**目标用户**:从零基础的普通消费者(Free/Go 档位),到重度知识工作者、开发者(Plus/Pro),再到企业客户(Business/Enterprise)——一个产品矩阵覆盖全用户分层,这本身就是一种值得研究的产品策略。

## 二、核心功能拆解

ChatGPT 的核心交互仍是对话框,但对话框背后挂载的能力已经变成一整套"工作台"。下表按当前(2026 年 7 月)产品现状梳理主要功能模块:

| 模块 | 功能说明 | AI 起作用的环节 |
|------|---------|-----------------|
| 基础对话 | 由 GPT-5.5(主力模型,Plus 起可用)驱动,免费版为 GPT-5.3(美区免费版目前带广告) | 模型自动在"即时响应"和"深度推理"之间路由,复杂问题触发更长思考链 |
| ChatGPT Search(搜索) | 对话中直接联网检索并给出带引用的答案,类似"内嵌 RAG"的搜索体验 | 检索 + 摘要生成 + 来源标注 |
| Agent Mode(智能体模式) | 2026 年迭代到 v2:可跨会话保留任务记忆、定时执行任务、原生对接 GitHub/Jira 等连接器,能替用户完成"订机票""填表""发邮件"等多步操作 | 任务规划、工具调用、执行结果自检 |
| Memory(记忆) | 跨会话记住用户偏好与事实;2026 年更新后记忆容量翻倍,用户可在"三点菜单"里删除记忆或一键关闭 | 从历史对话中提炼可复用信息,检索式注入新对话 |
| Deep Research(深度研究) | 输入研究课题后,模型自主规划检索计划、可指定只在可信站点/已连接的企业应用中搜索,生成结构化研究报告,支持中途插话调整方向 | 多步网页浏览、信息综合、报告生成 |
| 多模态能力 | 图像理解与生成(GPT-Image 系列)、实时语音对话(Advanced Voice Mode,基于语音到语音模型,可感知语气/情绪)、文件与截图理解 | 跨模态编码/解码统一在同一模型能力线上 |
| Codex(编程智能体) | 独立的自主编程 Agent:读写文件、执行命令、跑测试、修 Bug,以"完整任务"为粒度交付而非行级建议;支持 Codex Remote,可在手机端远程操控电脑上的编程任务 | 代码生成、自动化测试、自我纠错闭环 |
| Projects / Tasks | 项目级上下文隔离 + 定时任务调度,类似"给 AI 建文件夹"和"给 AI 设闹钟" | 项目内记忆隔离、周期性任务触发 |
| Custom GPTs / GPT Store | 用户可配置专属系统提示词、知识库、工具的"迷你产品",在商店内被他人复用 | 系统提示词与检索范围的产品化封装 |
| ChatGPT Atlas(浏览器) | 基于 Chromium 的独立浏览器,ChatGPT 以侧边栏形式嵌入,可总结网页、跨标签页比价、记住"浏览记忆"(如"帮我总结上周看过的招聘信息"),付费用户可开启浏览器内 Agent 模式代替操作网页 | 页面内容理解、跨页面记忆检索、网页操作执行 |
| Shopping Research(购物研究) | 描述购物需求后,ChatGPT 主动追问细节、深度调研商品与评测、结合历史对话生成个性化选购指南 | 需求澄清、多源比价与评测综合 |

一个值得记录的产品动作是:曾经作为独立旗舰产品线的 **Sora**(文生视频)已于 2026 年 4 月 26 日停止 Web/App 服务,API 也计划于同年 9 月 24 日关闭。据媒体报道,Sora 全球用户峰值约 100 万,后回落到 50 万以下,而其算力成本高达日均百万美元级,与 OpenAI"聚焦企业级 AI、推理模型与编程工具"的核心收入策略不匹配,因此被主动砍掉——包括一笔已谈妥的迪士尼十亿美元级合作也因此中止。这是理解 OpenAI 产品组合策略的重要参照:不是所有热点都要All in,产品线也要有进有退。

主流程走一遍(以搜索+Agent 组合任务为例):用户提问 →(判断需要联网)自动触发 ChatGPT Search 检索 → 模型基于检索结果生成带引用的回答 → 若用户进一步要求"帮我订好"这类可执行任务,则切换到 Agent Mode → Agent 规划步骤、调用工具(浏览器操作/连接器 API)→ 执行并汇报结果,用户可随时打断介入。整个链路里,AI 承担了"检索—生成—规划—执行—自检"几乎全部环节,人类的角色收缩为"下达意图 + 关键节点确认"。

## 三、技术方案推测

> 以下内容基于公开产品表现与官方博客描述进行**推测**,OpenAI 未完整公开其内部架构细节,请勿作为确定性技术结论引用。

- **模型路由(推测)**:ChatGPT 背后并非单一模型,而是在 GPT-5.5/5.6 系列的"Instant(即时)"与"Thinking/Pro(深度推理)"变体之间做自动路由,依据问题复杂度和用户是否显式选择模式来分配算力,这也解释了为何同一对话框里简单问题秒回、复杂问题会显式展示思考过程。
- **搜索能力(推测)**:ChatGPT Search 大概率是"检索增强生成(RAG)"的一种产品化实现——联网抓取/调用搜索索引获取实时网页片段,拼接进 Prompt 后由模型生成带编号引用的回答,原理与本仓库《什么是 RAG》一文描述的流程一致,只是检索源换成了实时网络而非私有知识库。
- **Agent Mode / Atlas 浏览器(推测)**:官方博客披露 Atlas 浏览器采用了代号 "OWL" 的新架构,但具体技术细节未完全公开;可以合理推测其能力来自"网页 DOM/视觉理解 + 动作规划 + 沙箱执行"的组合,与业界"computer use"类智能体的技术路线相似,通过截图/DOM 树感知页面状态,再输出点击、输入等动作序列,循环执行直至任务完成。
- **Memory(推测)**:跨会话记忆很可能是"从历史对话中抽取结构化事实/偏好 → 存入用户专属存储 → 新对话开始时按相关性检索注入 System Prompt"的机制,本质是面向单个用户的轻量级 RAG,而非把全部历史对话都塞进上下文窗口。
- **Codex(推测)**:作为独立的编程 Agent 产品线,大概率复用了与 Agent Mode 相同的"规划—工具调用—自我验证"框架,只是工具集换成了文件系统、终端、测试框架,并针对代码场景做了专门的强化学习后训练。

## 四、商业模式

### 订阅档位(个人版,核实截至 2026 年 7 月)

| 档位 | 价格 | 核心权益 |
|------|------|----------|
| Free | 免费(美区含广告) | GPT-5.3、基础对话与工具,有限额度 |
| Go | 8 美元/月 | 消息量为 Free 的 10 倍、文件上传、图像生成、无限量 GPT-5.3 Instant;仍含广告,不含 GPT-5.5 |
| Plus | 20 美元/月 | GPT-5.5、每月 10 次 Deep Research、Sora(历史权益)、Codex、Agent Mode |
| Pro | 100 美元/月(2026 年 4 月新增中间档)或 200 美元/月 | 与 200 美元档相同模型矩阵(含 GPT-5.5 Pro),额度为 Plus 的 5 倍 |

### 团队与企业版

| 档位 | 价格 | 核心权益 |
|------|------|----------|
| Business | 30 美元/席位/月(月付)或 25 美元/席位/月(年付) | SOC 2 Type II、SAML SSO、管理后台、60+ 集成 |
| Enterprise | 定制报价(第三方估算约 40-60 美元/席位/月) | ISO 27001、SCIM、企业密钥管理、RBAC、10 个区域数据驻留、定制法务条款 |

### API 定价(按每百万 Token,核实截至 2026 年 7 月)

| 模型 | 输入 | 缓存输入 | 输出 |
|------|------|----------|------|
| GPT-5.5 | 5 美元 | 0.5 美元 | 30 美元 |
| GPT-5.5 Pro | 30 美元 | — | 180 美元 |
| GPT-5.4(上一代) | 2.5 美元 | 0.25 美元 | 15 美元 |
| GPT-5.6 系列(Sol/Terra/Luna,预览版,面向受邀合作伙伴) | 5/2.5/1 美元 | — | 30/15/6 美元 |

### 广告与购物货币化(2026 年新增变化)

2026 年 1 月 16 日,OpenAI 正式确认在美区 Free 与 Go 免费/低价档位内测广告;2026 年 5 月推出自助广告平台,取消了此前 5 万美元的最低消费门槛,让中小商家也能投放。广告主要形式是结合 **Shopping Research(购物研究)** 功能的商品信息流广告——零售与生鲜品牌在早期投放中占比最高,逻辑是"用户在对话里主动描述购物需求"本身就是强购买意图信号,比传统搜索广告的点击信号更直接。

### 收入结构

据媒体报道,OpenAI 年化收入(ARR)在 2026 年 3 月已突破约 250 亿美元(2025 年底约 214 亿美元),其中约 70% 来自 ChatGPT 订阅(约 2000 万付费席位),约 25% 来自 API 调用,其余来自 Sora(注:该产品已于 2026 年 4 月停止服务)及授权等业务。需要说明的是,尽管收入高速增长,据报道公司整体仍处于大幅亏损状态,营收与支出的比例被外部测算为"每赚 1 美元亏 1.22 美元"级别——这也是理解其广告化、分层定价背后动机的关键背景:免费用户规模巨大但转化率有限,广告与更细的付费分层是弥补现金流缺口的必然选择。

## 与 Claude、Gemini 的定位差异

三家头部通用助手已经形成清晰的差异化叙事:ChatGPT 走"广度优先"——用户规模最大、功能面最全,试图做成"一个入口装下所有任务"的通用工作台,并率先探索广告与购物等消费级货币化路径;Claude 走"深度优先"——更受重度知识工作者与团队青睐,强调长文本协作中的一致性、指令遵循能力和更低的幻觉率,商业化上更偏向企业客户;Gemini 走"生态优先"——依托 Google 在搜索、Android、Chrome 的分发优势和超长上下文能力,把 AI 能力嵌入既有生产力场景。三者并非零和竞争,而是分别在"规模、质量、生态"三个维度上占位。

## 五、PM 视角的启示

1. **产品矩阵要能覆盖全用户分层,但复杂度要靠清晰的"档位阶梯"消化**。Free/Go/Plus/Pro 四个消费级档位 + Business/Enterprise 两个企业级档位,本质是用价格和额度把不同支付意愿的用户自然分流,而不是让所有人挤在同一套体验里——这对任何做订阅制 AI 产品的 PM 都是可复用的分层框架。
2. **敢于对不匹配核心战略的产品线做减法**。Sora 曾是流量与话题度俱佳的旗舰产品,但在用户留存和 ROI 不达预期、且与企业级/编程/推理的核心收入策略脱节后被果断关停,即使代价是终止与迪士尼的十亿美元级合作。这提醒 PM:资源有限时,"这个产品是否服务于公司当下最重要的战略目标"应该定期被重新拷问,而不是被沉没成本绑架。
3. **信任与商业化之间的张力需要显式设计,而不是放任模糊**。ChatGPT 选择在低价/免费档位引入广告、同时把"深度推理"与"专业能力"留在付费档位,是一种"先保规模再分层变现"的策略;而购物场景里"用户对话即高意图信号"的广告逻辑,也提示 PM 在设计 AI 产品的商业化路径时,要思考"用户在什么环节暴露了最真实的意图",并围绕那个环节而不是页面曝光去设计变现点。

## 相关阅读

- [什么是 RAG:检索增强生成入门](../../01-ai-basics/llm/what-is-rag.md)
- [如何分析一个优秀的 AI 产品](../../04-interview/case-analysis/analyze-a-good-ai-product.md)
- [AI 产品的商业模式与定价](../../04-interview/case-analysis/ai-business-model-and-pricing.md)
- [Perplexity 案例拆解:AI 搜索的答案引擎范式](../search-and-rec/perplexity.md)

## 参考资料

- [ChatGPT Plans | Free, Go, Plus, Pro, Business, and Enterprise](https://chatgpt.com/pricing/) — OpenAI 官方定价页,2026-07 访问
- [Pricing | OpenAI API](https://developers.openai.com/api/docs/pricing) — OpenAI 官方 API 定价文档,2026-07 访问
- [ChatGPT reaches 900M weekly active users](https://techcrunch.com/2026/02/27/chatgpt-reaches-900m-weekly-active-users/) — TechCrunch,2026-07 访问
- [Introducing ChatGPT Atlas](https://openai.com/index/introducing-chatgpt-atlas/) — OpenAI 官方博客,2026-07 访问
- [How we built OWL, the new architecture behind our ChatGPT-based browser, Atlas](https://openai.com/index/building-chatgpt-atlas/) — OpenAI 官方博客,2026-07 访问
- [Introducing shopping research in ChatGPT](https://openai.com/index/chatgpt-shopping-research/) — OpenAI 官方博客,2026-07 访问
- [Introducing deep research](https://openai.com/index/introducing-deep-research/) — OpenAI 官方博客,2026-07 访问
- [Previewing GPT-5.6 Sol: a next-generation model](https://openai.com/index/previewing-gpt-5-6-sol/) — OpenAI 官方博客,2026-07 访问
- [What to know about the Sora discontinuation](https://help.openai.com/en/articles/20001152-what-to-know-about-the-sora-discontinuation) — OpenAI 官方帮助中心,2026-07 访问
- [Why OpenAI really shut down Sora](https://techcrunch.com/2026/03/29/why-openai-really-shut-down-sora/) — TechCrunch,2026-07 访问
- [OpenAI tops $25 billion in annualized revenue](https://finance.yahoo.com/news/openai-tops-25-billion-annualized-033836274.html) — Yahoo Finance 转引 The Information,2026-07 访问