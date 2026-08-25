---
title: openevidence
type: note
permalink: aipmwiki/docs/03-case-studies/vertical/openevidence
---

# OpenEvidence 案例拆解:医疗 AI 的证据分级设计

**分类**:垂直行业
**体验时间**:2026 年 7 月

## 一、产品背景

OpenEvidence 是面向美国执业医生等临床人群的 AI 临床决策支持 / 医学检索产品:回答必须**溯源到同行评议文献**,在诊间(point of care)把「查证据」压缩成对话式交互。它和通用 ChatGPT/Claude 的分水岭不在「会不会答医学题」,而在**谁被允许用、答案必须挂什么证据、产品如何处理「证据质量并不均等」这一临床现实**。

2026 年 7 月 10 日,公司官方公告发布 **EvidenceGrade™**:在每条 AI 答案下方,对所引用、所依据的已发表证据做实时质量评级与可视化——直接回应医疗 AI 的老问题:模型善于揉合多源摘要,却容易抹平「随机双盲对照试验」与「小样本观察性研究」之间的天壤之别;在临床场景里,这种抹平可能影响生死决策的可信权重。

**竞争与位置**:对手既包括传统临床知识库 / CDS(如 UpToDate 及其 AI 化形态),也包括越来越强的通用大模型。2026 年 6 月 *Nature Medicine* 有研究指出,在部分医学基准上通用模型可超过 OpenEvidence 等专科工具——公司对此提出方法论与利益冲突等质疑,并另有医师评分向的对照研究传闻。对 PM 而言,关键不在「刷榜谁赢」,而在专科产品必须用**工作流信任设计**(引用、分级、指南对齐、执业身份门槛)证明自己不可被通用对话框替代。

**规模口径(公司口径,存疑)**:官方 About 文案称「美国临床医生中使用最广的临床决策支持与医学搜索引擎之一」,并写「majority of U.S. physicians use OpenEvidence daily」。媒体(如 NBC News)转述公司说法时,出现过「约 65% 美国医生使用」等表述;Fierce Healthcare 等亦报道过经执照核验的临床用户规模量级。**以上均为公司自报或媒体转述公司口径,非独立审计渗透率**,本文不将其写成确定事实;写作重点放在可核验的产品机制——EvidenceGrade。

另需注意:OpenEvidence 官网对欧盟与英国访问有区域限制声明(监管不确定性 / EU AI Act 等),说明高合规医疗 AI 的**地理可用性本身就是产品决策**。

## 二、核心功能拆解

### 主流程:从临床问题到「带分级的答案」

| 环节 | 用户感知 | AI / 产品起作用的地方 |
|------|----------|------------------------|
| 身份与准入 | 面向核验过的医疗专业人员 | 降低「普通消费者当诊疗工具」的滥用面 |
| 提问 | 自然语言临床问题 | 意图理解、是否构成可评级的证据主张 |
| 检索与生成 | 带来源引用的结构化回答 | 文献检索 + 摘要综合(医疗向 RAG/生成) |
| EvidenceGrade | 答案下方看到证据强度可视化 | 对引用文献做质量/确定性/相关性评级 |
| 金标准互补 | 有 Cochrane 系统评价等则优先露出 | 与正式证据综合产品化对接 |
| 指南对齐 | 与学会指南更新联动(如 AAO-HNSF 合作报道) | 减少「文献很多但指南已过时」的缝隙 |

### EvidenceGrade:设计要点(基于 2026-07-10 官方公告与同步报道)

官方定位:在**实时、诊间可用的速度**下,把循证医学里成熟的 **GRADE** 思路(Cochrane、WHO 及多数临床指南背后的证据评价方法)产品化——不是取代 Cochrane 系统评价,而是补上「绝大多数日常问题尚无正式分级综合」的空白。

据 TechTarget 等对官方说明的转述,机制可概括为:

1. **先判断问题是否「可评级」**:过滤简单定义查询、纯摘要类任务等——不是每个对话气泡都硬贴字母等级,避免伪精确。
2. **对答案中纳入的已发表文献**,从质量、确定性、相关性等维度评级,给出 **A–D** 等级观感:例如 A 侧更接近严谨系统评价一类证据;D 侧可能落在临床前 / 动物数据等「不能当临床决策主依据」的区间(细节以产品内说明与官方博客为准)。
3. **可视化「证据能承受多大权重」**:产品文案强调的是 *how much weight the underlying evidence can bear*,而不是再生成一段更流畅的话术——把不确定性显式交给医生。

公司医疗 AI 负责人 Samuel Finlayson 在公告中的表述可浓缩成产品原则:**并非所有证据同等确定;医生据此行动时,必须知道底层证据扛得住多大分量。** 专家人工评价团队覆盖不了每个日常问题,EvidenceGrade 被定位为方法的规模化延伸,并声明会随临床社区反馈迭代——这是把「起点版本」写进发布叙事,降低「算法即真理」的预期。

### 与「只做引用」类产品的差异

许多医疗/通用助手已经会做「角标引用」。EvidenceGrade 的增量是:**引用解决「从哪来」,分级解决「有多硬」**。对 PM 这是一层信息架构升级——同样三条参考文献,RCT 与病例系列若视觉权重相同,界面本身就在误导。OpenEvidence 选择把分级做成答案的常驻层,等于承认:在医疗垂直里,**信任 UI 是核心功能,不是合规附录**。

## 三、技术方案推测

> 以下为基于公开产品行为与公告的**推测**,OpenEvidence 未完整公开模型与管线细节。

- **检索增强生成(推测)**:核心仍是「临床问题 → 检索同行评议文献语料 → 带引用生成」。EvidenceGrade 更像在生成链路之外(或之后)增加一条**证据评价管线**:对命中文献抽特征(研究设计、样本量、偏倚风险代理指标、与问题的相关性等)再映射到 A–D;是否完全自动化、何处引入医师规则/校准集,官方未细说。
- **与 GRADE 的关系(官方表述 + 推测)**:公司称 *builds on the GRADE framework*,并与 Cochrane 合作露出系统评价。完整 GRADE 人工流程耗时且需方法学训练,实时产品**不可能**逐题跑完整专家共识;更合理的产品化是「GRADE 精神的自动化近似 + 对已有金标准综合的优先展示」,公告也强调正式分级综合只覆盖问题的一小部分。
- **可评级门控(推测)**:先做问题分类(证据主张 vs 定义/操作任务)再决定是否展示等级——这是降低「假精确」的产品策略,也减少无关场景的算力与争议。
- **评估争议的启示(推测)**:通用模型在部分基准领先,并不自动否定专科 CDS;若医生工作流需要的是可审计引用与证据强度,评测集就应包含「错误证据权重」类失败模式,而不仅是多选题正确率——这也是专科 PM 应对「通用模型够用论」时该准备的评测叙事。

## 四、商业模式

公开材料更强调临床采纳与工作流嵌入,完整价目表非本文核验重点。可观察的商业逻辑包括:

- **供给侧**:免费或低摩擦进入执业医生群体(媒体报道常见「医生工具」心智),用核验身份把网络效应锁在专业人群内。
- **信任溢价**:EvidenceGrade、Cochrane / 学会合作,本质是提高「敢在诊间用」的转换率——高合规垂直里,功能清单往往不如**责任可解释性**值钱。
- **竞争壁垒假设**:文献授权与更新、指南合作、执业身份图谱、证据评价方法的持续校准,都比「换一个更强基座模型」更难被通用助手一夜抄走;但通用模型 + 医院私有部署仍是长期压力。
- **区域策略**:对欧盟/英国不可用,说明监管套利与合规成本会直接切市场地图——全球化医疗 AI 不一定是默认选项。

收入侧具体是订阅、机构授权还是广告/药企触达等,公开报道口径不一,**此处不强行定性**;PM 启示应落在「先把证据责任设计成产品,再谈变现锚点」,而非倒过来。

## 五、PM 视角的启示

1. **高风险垂直的差异化,常常是「把不确定性产品化」,而不是「把答案说得更满」。** EvidenceGrade 主动展示证据能承受的权重,等于把幻觉与过度概括的风险部分转成用户可读信号——这比事后免责声明更接近真实临床决策习惯。
2. **金标准合作与自动分级要设计成互补,而不是互相替代。** Cochrane 系统评价只覆盖问题的一部分;自动 A–D 填补长尾。产品文案若吹成「已经等同指南」,会反噬信任;OpenEvidence 选择写明「complement」与「starting point」,是预期管理上的加分项。
3. **先过滤「什么不该分级」,再谈分级算法。** 对定义题、纯摘要任务硬贴等级会造成伪科学感;门控是证据 UI 的一部分。
4. **厂商渗透率话术要降级。** 「65% 医生」「majority daily use」适合标注为公司口径,用于理解其野心与销售叙事,不适合当作行业统计真相;案例与面试里更应抓住可复核机制(引用、GRADE 化、合作方)。
5. **专科工具必须准备「评测战」话术。** 当通用模型在公开基准领先时,要用工作流指标(证据权重是否误导、引用是否可点开核对、指南是否对齐)重新定义胜负,而不是只在同一套多选题上缠斗。

## 相关阅读

- [幻觉问题:成因与产品层面的缓解手段](../../01-ai-basics/llm/hallucination.md)
- [什么是大模型的幻觉?产品上怎么缓解?](../../04-interview/basics/hallucination-mitigation.md)
- [什么是 RAG:检索增强生成入门](../../01-ai-basics/llm/what-is-rag.md)
- [如何评估 RAG 知识库的准确率?](../../04-interview/basics/rag-kb-accuracy-evaluation.md)
- [智能客服:最成熟的 LLM 落地场景拆解](ai-customer-service.md)
- [模型选型决策框架:效果、成本、延迟、合规四要素](../../02-pm-skills/cost-and-tech/model-selection-framework.md)

## 参考资料

- [OpenEvidence Launches EvidenceGrade™](https://www.openevidence.com/announcements/openevidence-launches-evidencegrade-empowering-physicians-to-see-the-strength-of-cited-evidence-beneath-each-ai-answer) — 官方公告,2026-07-10,2026-07 访问
- [OpenEvidence adds real-time evidence quality grading to AI](https://www.techtarget.com/healthtechanalytics/news/366645822/OpenEvidence-adds-real-time-evidence-quality-grading-to-AI) — TechTarget 同步报道(A–D、可评级门控、Cochrane/GRADE),2026-07 访问
- [OpenEvidence takes aim at the quality of AI-generated evidence](https://www.fiercehealthcare.com/ai-and-machine-learning/openevidence-launches-medical-ai-copilot-feature-grades-medical-evidence) — Fierce Healthcare,2026-07 访问
- [OpenEvidence: Most physicians quietly use this medical AI tool](https://www.nbcnews.com/tech/tech-news/openevidence-ai-doctor-medical-physician-login-app-what-npi-uptodate-rcna341064) — NBC News(含公司转述的约 65% 医生等口径,**标存疑**),2026-07 访问
- [PR Newswire 同题通稿](https://www.prnewswire.com/news-releases/openevidence-launches-evidencegrade-empowering-physicians-to-see-the-strength-of-cited-evidence-beneath-each-ai-answer-302822750.html) — 2026-07-10,2026-07 访问