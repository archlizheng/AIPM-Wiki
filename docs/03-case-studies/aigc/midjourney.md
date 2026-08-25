---
title: midjourney
type: note
permalink: aipmwiki/docs/03-case-studies/aigc/midjourney
---

# Midjourney 案例拆解:文生图的用户分层与 GPU 定价

**分类**:AIGC
**体验时间**:2026 年 7 月

## 一、产品背景

Midjourney 是文生图赛道里把「付费订阅 + GPU 时长计量」跑得最彻底的产品之一。它不提供可长期依赖的免费生成档(官网与文档列出的正式套餐均为订阅制;仅部分移动端 App 有限时试用),用户从第一次正式出图起就要为算力付费——这和 ChatGPT、GitHub Copilot 等「先免费养习惯再转化」的路径形成鲜明对比。

产品形态上,Midjourney 经历了从 Discord Bot 到独立 Web(midjourney.com)的迁移:早期几乎完全依赖 Discord 频道完成 `/imagine` 交互与社区互评;如今 Web 端的 Create / Explore 已成为主力入口,Discord 仍保留,但核心计费与隐私控制都挂在订阅账户上。目标用户大致可拆成三层:

| 用户层 | 典型诉求 | 对产品的关键约束 |
|--------|----------|----------------|
| 爱好者 / 轻量试玩 | 偶尔出几张图、跟风玩风格 | 价格门槛要低,但不需要无限量 |
| 创作者 / 独立设计师 | 日常出图、迭代构图与风格 | 要「够用的快通道」+「慢但无限」的兜底 |
| 商业团队 / 年营收较高的公司 | 客户保密、高并发、商用合规 | 要隐私(Stealth)、更高并发,且条款强制更高档位 |

竞争格局上,同赛道还有 OpenAI 的图像生成(打包进 ChatGPT 订阅)、Adobe Firefly(嵌入 Creative Cloud)、国内的即梦 / 通义万相 / Midjourney 类竞品等。Midjourney 的差异化不在「谁更能出一张好看的图」,而在于它把 GPU 当作可售卖的「流量包」,并用 Fast / Relax / Stealth 把不同支付意愿的用户拆开——这正是本文的分析主线。

## 二、核心功能拆解

一次典型的文生图主流程:

| 环节 | 用户动作 | AI 起作用的环节 |
|------|----------|-----------------|
| 输入 | 在 Web Imagine 栏或 Discord 输入 prompt(可加参数、参考图、风格引用) | 文本/图像条件理解 |
| 生成 | 选择 Fast / Relax /(部分版本)Turbo 速度档 | 扩散模型在 GPU 上采样出图(通常一次 4 张网格) |
| 迭代 | Upscale、Variation、局部重绘、风格迁移等 | 在已有 latent/图像条件上再生成 |
| 分发与隐私 | 默认可能进入 Explore 公开展示;Pro/Mega 可开 Stealth | 产品层策略,非模型能力本身 |
| 商用 | 按订阅条款使用输出物 | 法律与套餐约束,非技术环节 |

对 PM 最有研究价值的不是某个按钮叫什么,而是三组「产品化的算力开关」:

**1. Fast vs Relax(速度换容量)**

官方文档把订阅描述为「购买 GPU 时间」,类比手机流量套餐:

- **Fast**:消耗每月定额的 Fast GPU 时长,生成更快;未用完的 Fast 时间**不结转**到下月。
- **Relax**:不消耗 Fast 时长、图像侧可视为无限(Standard 及以上),但排队等待,官方说明等待可从接近 0 到约 30 分钟不等,取决于队列与个人使用强度。
- **Turbo**(实验性):走更高优先级 GPU 池,可达更快,但**双倍消耗** Fast 时间;部分新模型版本可能暂不支持。

官方给出的大致消耗参考(便于理解「一小时 Fast 能换多少次出图」,非保证值):一组 SD 四图约 0.8 分钟 GPU、HD 约 1.3 分钟;一组 SD 四视频约 8 分钟、HD 约 26 分钟。可见视频任务对 Fast 配额的吞噬远快于静态图——但本文不展开视频产品化路径,只把它当作「同一套 GPU 计价体系下的高消耗品类」。

**2. Stealth(隐私换档位)**

默认社区是开放的:生成物可能出现在网站 Explore。**Stealth Mode 仅 Pro / Mega 提供**,用于在网站侧隐藏作品。官方同时提醒:若在共享 Discord 频道生成,同频道其他人仍可见——隐私是「套餐权益 + 使用场景」的组合,不是一键全局隐身。

**3. 并发与队列**

档位越高,同时跑的 Fast 任务数越多(图像:Basic/Standard 约 3 路 Fast,Pro/Mega 约 12 路 Fast)。这对「设计师开着一堆变体并行试错」和「工作室流水线出图」是实质体验差异,而不只是营销表上的一行字。

## 三、技术方案推测

> 以下内容部分基于官方已披露的产品机制,部分为基于公开表现的合理推测,已标注区分。

- **官方已披露(计费抽象)**:用户购买的是 GPU 时间配额与模式(Fast / Relax / Turbo),而非「按张数」的简单计数;不同任务(分辨率、upscale、视频等)消耗不同分钟数。
- **推测(生成骨干)**:文生图主流仍是扩散模型(diffusion)路线——在噪声上逐步去噪得到图像;Midjourney 未完整公开各版本网络结构与训练数据细节,不宜写成确定架构结论。
- **推测(Fast / Relax 的工程含义)**:Fast 对应预留/高优先级推理资源;Relax 对应闲时或共享队列上的尽力而为调度。这能解释为何 Relax 可以「无限张」却仍可能排队很久——无限的是请求权,不是即时算力。
- **推测(社区默认公开)**:早期 Discord 公频出图强化了风格传播与「看别人 prompt」的学习效应,降低获客成本;Stealth 则是把「社区增长」和「商业保密」拆成可单独售卖的权益,避免所有用户默认承担同一套隐私成本。

## 四、商业模式

### 套餐结构(核实截至 2026 年 7 月,来源:Midjourney 官方 Plans 文档)

| 档位 | 月付 | 年付(约合月价) | Fast GPU / 月 | Relax | Stealth |
|------|------|----------------|---------------|-------|---------|
| Basic | $10 | $96/年(约 $8/月) | 3.3 小时(约 200 分钟) | 无 | 无 |
| Standard | $30 | $288/年(约 $24/月) | 15 小时 | 无限图像 | 无 |
| Pro | $60 | $576/年(约 $48/月) | 30 小时 | 无限图像 + SD 视频 Relax | 有 |
| Mega | $120 | $1,152/年(约 $96/月) | 60 小时 | 同 Pro | 有 |

补充规则(同样来自官方文档,对分层逻辑很关键):

- **没有面向网页/Discord 的长期免费出图档**;年付相对月付约 **8 折**(官方表述为年付约 20% 折扣)。
- Fast 用尽后可加购额外 Fast 时间,官方标价约 **$4 / 小时**(各档相同)。
- **年营收超过 $1,000,000 USD 的公司**,须购买 **Pro 或 Mega** 才能按条款进行相应商用——这是用法律条款做的「企业档强制升级」,而不是单纯靠功能差异劝升。

### 定价逻辑拆解

Midjourney 的分层可以读成三张「可独立计价的轴」:

1. **容量轴(Fast 小时数)**:$10 → $30 → $60 → $120,Fast 配额大致按 3.3 / 15 / 30 / 60 小时递进。轻量用户用 Basic「买一小袋流量」;重度用户用更高档位降低「单位创作的焦虑」。
2. **弹性轴(Relax)**:Standard 起才给图像 Relax。Basic 是硬顶——Fast 用完就只能加购或等下月。这逼出一道清晰的升级钩子:「我想多玩但不想每张都烧 Fast」→ 从 $10 跳到 $30。
3. **隐私与合规轴(Stealth + $1M 条款)**:Stealth 绑在 $60+;高营收公司条款也绑在 $60+。商业客户为保密与合规付费,爱好者为社区公开「付」的是曝光而非现金——两边用同一生成引擎,付费理由不同。

与「按张收费」或「订阅内无限畅玩」相比,GPU 时长制的好处是:平台可以把真实的推理成本波动(模型变大、分辨率变高、视频更贵)吸收进「每任务消耗更多分钟」,而不必每月改标价;坏处是用户心智成本高——「我到底还能出多少张」不如「每月 500 张」好懂,需要靠文档里的分钟对照表教育市场。

## 五、PM 视角的启示

1. **把算力商品化时,优先设计「可感知的分层轴」,而不是只堆功能 checklist。** Midjourney 的 Fast/Relax/Stealth 分别对应速度、容量弹性、隐私——用户升级时能明确说出「我为哪一件事多付钱」。做 AI 产品套餐时,先问这三轴里哪一轴对你的付费用户最痛,再决定绑在哪一档。
2. **可以没有免费档,但必须有「足够低的入门价」和「清晰的下一跳」。** $10 Basic 承担了获客与试水;真正的用量解放发生在 $30 的 Relax。若入门价直接 $60,爱好者层会流失;若 $10 就给无限 Relax,则高 ARPU 层缺乏升级理由。
3. **用条款做企业升级,比只靠「企业功能清单」更硬。** 「年营收 > $1M 须 Pro/Mega」把定价与客户支付能力直接挂钩,减少大客户长期趴在低价档上的空间。PM 若服务 To-B 创意工具,值得研究这类「收入门槛条款」与功能门槛(SSO、发票、审计)如何配合使用。

## 相关阅读

- [DeepSeek 开始峰谷定价,你怎么看 AI 产品的商业模式?](../../04-interview/case-analysis/ai-business-model-and-pricing.md)
- [AI 产品成本测算入门:token、并发与人力](../../02-pm-skills/cost-and-tech/llm-cost-101.md)
- [某功能接入大模型后成本涨了 10 倍,如何降本?](../../04-interview/case-analysis/reduce-llm-cost-10x.md)
- [如何分析一个优秀的 AI 产品](../../04-interview/case-analysis/analyze-a-good-ai-product.md)
- [AI 视频生成案例拆解:Sora 与 Veo 的两条路线](ai-video-generation.md)

## 参考资料

- [Comparing Midjourney Plans](https://docs.midjourney.com/hc/en-us/articles/27870484040333-Comparing-Midjourney-Plans) — Midjourney 官方文档(Basic/Standard/Pro/Mega 价格与权益、$1M 营收条款),2026-07 访问
- [GPU Speed (Fast, Relax, Turbo)](https://docs.midjourney.com/hc/en-us/articles/32016412137741-GPU-Speed-Fast-Relax-Turbo) — Midjourney 官方文档(GPU 时间计量与任务耗时参考),2026-07 访问
- [Stealth Mode](https://docs.midjourney.com/hc/en-us/articles/32019750070669-Stealth-Mode) — Midjourney 官方文档(Stealth 仅 Pro/Mega),2026-07 访问
- [Keeping Your Creations Private](https://docs.midjourney.com/hc/en-us/articles/28014645615373-Keeping-Your-Creations-Private) — Midjourney 官方文档(默认公开与隐私边界),2026-07 访问
- [Plan Information](https://docs.midjourney.com/hc/en-us/sections/28005319720845-Plan-Information) — Midjourney 官方文档栏目(订阅与试用说明),2026-07 访问