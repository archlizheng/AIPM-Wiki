---
title: ai-video-generation
type: note
permalink: aipmwiki/docs/03-case-studies/aigc/ai-video-generation
---

# AI 视频生成案例拆解:Sora 与 Veo 的两条路线

**分类**:AIGC
**体验时间**:2026 年 7 月

## 一、产品背景

视频生成是目前生成式 AI 里单位成本最高、也最能体现"能力强不等于产品化容易"的赛道。这篇拆解选了两个技术水平接近、但产品化路径完全相反的样本:OpenAI 的 Sora,以及 Google DeepMind 的 Veo(经由 Flow / Gemini App 分发)。

Sora 走的是"独立爆款消费应用"路线:2024 年 2 月发布研究预览,2025 年 9 月底推出 Sora 2 模型,并同步上线了一款仿短视频社区形态的独立 App(带有把真人形象插入生成视频的 Cameo 功能)。但这条路线只走了半年多——2026 年 1 月起免费用户被切断生成权限,3 月 OpenAI 官方宣布放弃消费级 Sora App,4 月 26 日 App 与网页版正式关停,原定与迪士尼的角色授权合作也随之取消,只有面向开发者的 API 保留使用,且已定档 2026 年 9 月 24 日停止服务。截至本文体验时间(2026 年 7 月),Sora 已经不是一个可以直接打开使用的消费产品,而是一个"进入倒计时"的 API。

Veo 走的是相反的路线:不单独打造一款视频生成 App,而是把 Veo 3.1 模型能力分发进 Google 已有的产品矩阵——面向创作者的 Flow(AI 电影制作工具)、Gemini App、Google AI Studio、面向企业的 Vertex AI,以及嵌入 Google Vids 等生产力工具。同一个模型,同时服务 C 端订阅用户和 B 端按量付费的开发者。

竞争格局上,同一时期还有 Runway Gen-3/4、快手可灵、字节跳动即梦/Seedance、Pika 等玩家,但 Sora 的关停是目前唯一一个由头部大厂官方承认、大规模、公开复盘的"视频生成消费应用失败案例",这也是本文选它做主案例的原因:比起看一个"成功产品长什么样",看清一次公开的失败更能建立 PM 的成本 sense。

## 二、核心功能拆解

| 维度 | Sora(2026 年 7 月现状) | Veo 3.1(2026 年 7 月现状) |
|------|------------------------|---------------------------|
| 产品形态 | 消费级 App/网页已关停,仅剩开发者 API | Gemini App / Flow / Vertex AI / AI Studio / Google Vids 多入口在用 |
| 时长 | Sora 2:4s / 8s / 12s;Sora 2 Pro:10s / 15s / 25s | 单次生成 8s,可通过"场景延伸"功能拼接更长片段 |
| 分辨率 | Sora 2 标准 720p;Sora 2 Pro 支持 720p / 1024p / 1080p | 720p / 1080p / 4K(按模型档位区分) |
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

**成本结构与失败案例(Sora 消费级 App)**:多家科技媒体(TechCrunch、Variety 等)报道并援引 Sam Altman 的表态,Sora App 上线半年多的时间里日均算力成本约 100 万美元,而整个生命周期内的应用内购收入总计约 210 万美元;活跃用户从峰值近百万降至关停前不足 50 万。Sam Altman 将关停原因归结为"把算力和产品资源集中到下一代自动化研究和企业级产品上",而多篇报道也指出深层原因是版权/deepfake 争议、与迪士尼的授权合作破裂,以及 IPO 前对盈利能力的优先级调整。这是目前最具体、最公开的"视频生成消费级产品烧钱不可持续"案例。

**API 定价策略**:两家都采用"按秒计费 + 分辨率分层"的精细计价,而不是打包订阅:

| | Sora 2 API | Veo 3.1 API |
|---|---|---|
| 标准档 | $0.10/秒(720p) | $0.40/秒(720p/1080p) |
| 其他档位 | 高阶 Sora 2 Pro:$0.30/秒(720p)、$0.50/秒(1024p)、$0.70/秒(1080p) | 低价 Fast 档:$0.10/秒(720p)、$0.12/秒(1080p)、$0.30/秒(4K) |
| 折扣选项 | Batch 模式约 5 折,24 小时内交付 | Lite 档低至 $0.05/秒(720p),不支持 4K |

**消费端订阅**:Sora 关停前依附于 ChatGPT Plus($20/月)与 Pro($200/月)订阅;Veo 则打包进 Google AI Pro(约 19.99 美元/月,含 1,000 点 Flow 积分)与 Google AI Ultra(约 249.99 美元/月,积分更高)的"积分消耗制"里——积分按分辨率/模型档位消耗,用户很难感知到背后真实的算力成本,平台通过积分设计把成本波动隐藏在订阅定价之后。

两条路线的商业模式对比,本质是"独立计量、独立盈亏的爆款应用" vs "成本摊入更大订阅盘子、由多产品分摊风险"的差异——后者在 Sora 关停事件后看起来是更稳健的选择。

## 五、PM 视角的启示

1. **高成本能力开放给 C 端高频免费/低价场景之前,先把单位经济模型算清楚。** Sora 的教训很直接:视频生成的边际成本目前还撑不起"类抖音"式的高频免费消费场景,PM 在决定要不要做成独立消费应用之前,必须先核算 LTV/CAC 与算力成本的匹配度,而不是先追求用户规模和刷屏效果。
2. **分发策略上,把高成本能力嵌入已有生态位比孤注一掷做独立爆款 App 更能对冲风险。** Google 没有为 Veo 单独做一个消费级爆款应用,而是把它分发进 Gemini App、Flow、Vertex AI、Google Vids 等多个已有入口——即使某个场景遇冷,损失也是局部的,不会像 Sora 那样因单一产品失败而整体退场。
3. **定价机制要和真实成本结构强绑定,并做到可拆分的精细颗粒度。** 按秒 + 分辨率分层、批处理折扣,这类"精细计价"本身就是应对高成本 AI 能力的产品设计手段;而消费端的"积分/订阅打包"看似简化了用户认知,实际上是把成本风险转移给了平台自己,一旦重度用户占比上升,打包定价就可能重演 Sora 式的亏损。

## 相关阅读

- [可灵与即梦:视频生成两条商业化路径](kling-vs-jimeng.md)
- [DeepSeek 开始峰谷定价,你怎么看 AI 产品的商业模式?](../../04-interview/case-analysis/ai-business-model-and-pricing.md)
- [某功能接入大模型后成本涨了 10 倍,如何降本?](../../04-interview/case-analysis/reduce-llm-cost-10x.md)
- [AI 产品成本测算入门:token、并发与人力](../../02-pm-skills/cost-and-tech/llm-cost-101.md)

## 参考资料

- [Sora 2 is here](https://openai.com/index/sora-2/) — OpenAI 官方,2026-07 访问
- [What to know about the Sora discontinuation](https://help.openai.com/en/articles/20001152-what-to-know-about-the-sora-discontinuation) — OpenAI 官方帮助中心,2026-07 访问
- [Sora 2 Model](https://developers.openai.com/api/docs/models/sora-2) — OpenAI 开发者文档(定价、分辨率、时长规格),2026-07 访问
- [Why OpenAI Shut Down Sora: Sam Altman Felt 'Terrible' Telling News to Disney CEO Josh D'Amaro](https://variety.com/2026/digital/news/why-openai-shut-down-sora-sam-altman-felt-terrible-disney-ceo-josh-damaro-1236705497/) — Variety 报道,2026-07 访问
- [Why OpenAI really shut down Sora](https://techcrunch.com/2026/03/29/why-openai-really-shut-down-sora/) — TechCrunch 报道,2026-07 访问
- [Veo](https://deepmind.google/models/veo/) — Google DeepMind 官方产品页,2026-07 访问
- [Gemini API pricing](https://ai.google.dev/gemini-api/docs/pricing) — Google 官方开发者文档(Veo 3/3.1 定价),2026-07 访问
- [Google AI Pro & Ultra 订阅](https://gemini.google/subscriptions/) — Google 官方订阅页(Flow 积分说明),2026-07 访问