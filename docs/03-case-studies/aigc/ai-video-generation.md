---
title: ai-video-generation
type: note
permalink: aipmwiki/docs/03-case-studies/aigc/ai-video-generation
---

# AI 视频生成案例拆解:Sora 与 Veo 的两条路线

**分类**:AIGC
**资料核验时间**:2026 年 9 月 14 日

## 一、产品背景

视频生成是目前生成式 AI 里单位成本最高、也最能体现"能力强不等于产品化容易"的赛道。这篇拆解选了两个技术水平接近、但产品化路径完全相反的样本:OpenAI 的 Sora,以及 Google DeepMind 的 Veo(经由 Flow / Gemini App 分发)。

Sora 走的是"独立爆款消费应用"路线。OpenAI 官方帮助中心现已明确，Sora 网页和 App 于 2026 年 4 月 26 日停止服务，Sora API 将于 2026 年 9 月 24 日停止服务。本文不再把媒体对算力、收入或用户规模的估算当作 OpenAI 经营事实，重点放在官方已确认的退场时间与产品容器选择。

Veo 走的是相反的路线:不单独打造一款视频生成 App,而是把 Veo 3.1 模型能力分发进 Google 已有的产品矩阵——面向创作者的 Flow(AI 电影制作工具)、Gemini App、Google AI Studio、面向企业的 Vertex AI,以及嵌入 Google Vids 等生产力工具。同一个模型,同时服务 C 端订阅用户和 B 端按量付费的开发者。

竞争格局上,同一时期还有 Runway Gen-3/4、快手可灵、字节跳动即梦/Seedance、Pika 等玩家。Sora 的官方退场公告为观察独立消费入口、API 生命周期和数据迁移提供了明确材料，这也是本文选它做主案例的原因:比起只看成功产品,公开退场同样能帮助 PM 建立成本和交付边界意识。

## 二、核心功能拆解

| 维度 | Sora(2026 年 9 月现状) | Veo 3.1(2026 年 9 月现状) |
|------|------------------------|---------------------------|
| 产品形态 | 消费级 App/网页已关停，API 也有明确停止日期 | Google DeepMind 产品页提供 Gemini、Flow 和 API 入口；具体账号资格按地区和计划复核 |
| 模型状态 | Sora API 将于 2026-09-24 停止服务 | Gemini API 当前列出 `veo-3.1-generate-preview`、Fast 与 Lite 预览模型 |
| 计费口径 | 本轮不保留将要退场 API 的旧价格作采购依据 | 官方价格页按秒列出 Standard、Fast、Lite，且 4K、音频和成功生成计费规则不同 |
| 输出能力 | 旧版时长和分辨率只作为历史资料，不代表 9 月可用性 | 官方产品页说明支持文本/图像生视频、原生音频和延展等能力，具体规格随模型入口复核 |
| 音频 | 支持对话、音效、环境音的原生同步生成 | 支持文本转"视频+音频"(T2VA),对话/音效/环境音同步生成 |
| 特色功能 | Cameo(把真实人物身份插入任意生成场景)、Storyboard 分镜编辑器 | 角色一致性保持、镜头控制、图生视频、画面内对象插入/移除 |
| AI 的作用环节 | 文本/图像理解 → 视频扩散生成 → 音频同步 → 身份保真插入(Cameo) | 文本/图像理解 → 视频扩散生成 → 音频同步 → 镜头与运动控制 |

从功能拆解看,两者的核心生成能力(时长、分辨率、原生音频)已经非常接近,真正的差异不在"AI 能做什么",而在"这个能力被装进了什么样的产品容器里"——Sora 曾经的容器是一个类抖音的独立社交 App,Veo 的容器是嵌入已有生产力/创作工具矩阵的一个"功能点"。

## 三、技术方案推测

> 以下内容部分基于官方已披露信息,部分为基于公开资料的合理推测,已标注区分。

- **官方已披露**:OpenAI 在 2024 年 Sora 技术报告中说明,模型将视频和图像统一表示为"视觉图块(patch)"序列,并使用扩散 Transformer(diffusion transformer)架构在这些图块上做生成,这是 Sora 技术路线中少数被官方明确公开的信息。
- **推测**:Veo 的具体架构 Google 并未完整公开,推测同样是时空维度联合建模的扩散 Transformer 类架构;由于 Google 自有 TPU 算力栈,推测其单位推理成本结构与依赖 GPU 云的 OpenAI 有所不同,这也可能是两家在"是否敢做独立高频消费应用"上选择不同的部分原因。
- **推测**:Cameo 功能大概率依赖类似"身份/人脸 embedding + 参考视频作为条件输入"的技术方案,官方未披露具体实现细节。
- **为什么视频生成天然比文本贵(基于公开原理的推理,非厂商官方数据)**:文本生成每次只需预测下一个 token;视频是在时间和空间两个维度上的像素级扩散去噪过程,一段 12 秒、720p 的视频所需的计算量比一次典型的文本对话高出几个数量级,且必须保证帧与帧之间的时序一致性,这决定了视频生成的推理成本目前很难压到文本生成的量级。

## 四、商业模式

**成本结构与失败案例(Sora 消费级 App)**:官方材料能够核验退场日期、数据导出、退款和 API 停止安排，不能据此计算产品的完整算力成本、收入或用户规模。关于经营原因和版权争议，本文只保留媒体报道的证据边界，不把它们改写成 OpenAI 已确认的单一因果。

**API 定价策略**:Google 当前价格页明确按秒和分辨率分层，Veo 3.1 模型名仍带 `preview`；Sora API 有停止服务日期，因此不适合作为新的采购基线:

| | Sora 2 API | Veo 3.1 API |
|---|---|---|
| 标准档 | 当前 API 将于 2026-09-24 停止服务，不保留旧价作为采购依据 | $0.40/秒(720p/1080p)，$0.60/秒(4K) |
| Fast 档 | 不适用 | $0.10/秒(720p)、$0.12/秒(1080p)、$0.30/秒(4K) |
| Lite 档 | 不适用 | $0.05/秒(720p)、$0.08/秒(1080p)，不支持 4K |

**消费端订阅**:Sora 的网页和 App 已关停。Veo 仍有 Gemini 和 Flow 等入口，但具体订阅、积分和地区资格应以当前订阅页及账号显示为准，本文不把 7 月旧套餐当成 9 月报价。

两条路线的商业模式对比,本质是"独立计量、独立盈亏的爆款应用" vs "成本摊入更大订阅盘子、由多产品分摊风险"的差异——后者在 Sora 关停事件后看起来是更稳健的选择。

## 五、PM 视角的启示

1. **高成本能力开放给 C 端高频免费/低价场景之前,先把单位经济模型算清楚。** Sora 的官方退场安排说明，独立消费入口、API 生命周期和用户数据导出都应在立项时进入风险表；PM 在决定要不要做成独立消费应用之前,必须先核算 LTV/CAC 与算力成本的匹配度,而不是先追求用户规模和刷屏效果。
2. **分发策略上,把高成本能力嵌入已有生态位比孤注一掷做独立爆款 App 更能对冲风险。** Google 没有为 Veo 单独做一个消费级爆款应用,而是把它分发进 Gemini App、Flow、Vertex AI、Google Vids 等多个已有入口——即使某个场景遇冷,损失也是局部的,不会像 Sora 那样因单一产品失败而整体退场。
3. **定价机制要和真实成本结构强绑定,并做到可拆分的精细颗粒度。** Veo 3.1 当前按秒和分辨率分层，Standard、Fast、Lite 形成不同速度与成本档位；消费端订阅、积分和地区资格仍要按当前页面复核，不能从一次 API 价格表推导长期套餐策略。

## 相关阅读

- [可灵与即梦:视频生成两条商业化路径](kling-vs-jimeng.md)
- [DeepSeek 开始峰谷定价,你怎么看 AI 产品的商业模式?](../../04-interview/case-analysis/ai-business-model-and-pricing.md)
- [某功能接入大模型后成本涨了 10 倍,如何降本?](../../04-interview/case-analysis/reduce-llm-cost-10x.md)
- [AI 产品成本测算入门:token、并发与人力](../../02-pm-skills/cost-and-tech/llm-cost-101.md)

## 参考资料

- [Sora 2 is here](https://openai.com/index/sora-2/) — OpenAI 官方，历史发布资料，2026-09-14 访问
- [What to know about the Sora discontinuation](https://help.openai.com/en/articles/20001152-what-to-know-about-the-sora-discontinuation) — OpenAI 官方帮助中心，网页/App 已于 2026-04-26 停止，API 将于 2026-09-24 停止，2026-09-14 访问
- [Sora 2 Model](https://developers.openai.com/api/docs/models/sora-2) — OpenAI 开发者文档，模型入口与历史规格，2026-09-14 访问
- [Why OpenAI Shut Down Sora: Sam Altman Felt 'Terrible' Telling News to Disney CEO Josh D'Amaro](https://variety.com/2026/digital/news/why-openai-shut-down-sora-sam-altman-felt-terrible-disney-ceo-josh-damaro-1236705497/) — Variety 报道,2026-07 访问
- [Why OpenAI really shut down Sora](https://techcrunch.com/2026/03/29/why-openai-really-shut-down-sora/) — TechCrunch 报道,2026-07 访问
- [Veo](https://deepmind.google/models/veo/) — Google DeepMind 官方产品页，Veo 3.1 能力与入口，2026-09-14 访问
- [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing) — Google 官方开发者文档，Veo 3.1 Standard/Fast/Lite 预览模型与按秒价格，2026-09-14 访问
- [Google AI Pro & Ultra 订阅](https://gemini.google/subscriptions/) — Google 官方订阅页(Flow 积分说明),2026-07 访问
