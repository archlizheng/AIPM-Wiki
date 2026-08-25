---
title: khanmigo
type: note
permalink: aipmwiki/docs/03-case-studies/vertical/khanmigo
---

# Khanmigo 案例拆解:教育 AI 的成效指标与采纳教训

**分类**:垂直行业
**体验时间**:2026 年 7 月

## 一、产品背景

教育 AI 最容易被讲成"每个学生配一个 AI 家教",也最容易在真实教室里撞上另一件事:**有权限 ≠ 会用,会聊 ≠ 学会**。Khanmigo 是 Khan Academy(可汗学院)推出的生成式 AI 辅导与教师助手,2023 年首次接入平台;作为非营利组织产品,它的目标表述更接近"提升可衡量的学习结果",而不是追求消费级 DAU 叙事。

到 2025–26 学年,Khan Academy 在官方博客中公开了一组对产品经理极有价值的数据:**拥有 Khanmigo 权限的学生里,大约只有 15% 会真正使用**;同时他们把成效指标锚定在 **next-item correctness**(下一题独立正确率)——学生接受辅导后,能否在同一技能上**不借助 Khanmigo** 做对下一题。配套的交互重设计(把 AI 嵌进练习流、主动出手、区分作答前后帮助方式)在试点区推行,并计划于 **2026 年夏季**向全部学区合作伙伴推开"Reimagined"体验。

与[智能客服案例](ai-customer-service.md)对照:客服把"结果"定义成可计费的会话关闭事件;Khanmigo 把"结果"定义成**学习迁移是否发生**。计费可以按学生席位/学区合同走,但产品是否成功,看的是练习成效与采纳,而不是"聊了多少轮"。

## 二、核心功能拆解

### 1. 学生侧:从"另开聊天窗口"到"嵌在练习里"

早期形态更接近"旁边有一个 AI 聊天框":学生卡住时需要自己想到去问。官方复盘认为,这正是采纳上不去的重要原因——很多学生会继续瞎猜、卡住或干脆停练,而不是主动发起完美提问。

重设计后的方向(学区试点与官方博客披露):

| 设计变化 | 意图 |
|----------|------|
| Khanmigo 在做作业/做题时更可见,引导整份作业 | 降低"想起开工具"的摩擦 |
| 不等学生问出完美问题,在需要时主动提供帮助 | 从被动应答改为主动辅导 |
| 作答前 / 作答后帮助方式分化(基于求助行为研究) | 作答前鼓励尝试与轻提示;答错后可更直接讲解,避免错误练成习惯 |
| 按"初学 vs 复习"调节支持强度 | 同一技能不同掌握阶段用不同策略 |
| 注入技能掌握度与先修(prerequisite)信息,必要时先复习先修 | 把辅导接到学生真实知识图谱,而非只看当前题面 |
| 整体交互更简单直观 | 教室场景下的可用性优先于功能堆叠 |

产品护栏上,Khanmigo 长期坚持**不直接把答案塞给学生**(鼓励思考与产出性挣扎);试点反馈后,规则被细化为"提交前偏启发、提交后可更支架化",而不是一刀切禁止讲解。

### 2. 教师与学区侧:仪表盘与路径可见性

Reimagined 体验同时重做教师/管理员仪表盘与学生学习路径:按周、教师、学校、班级、学生查看练习与进度;学生侧则有更清晰的作业队列、进度反馈与激励(如 Gems、完成动效等)。官方强调 AI 应**支持教师判断**,在教师无法同时盯住每个孩子时补位,而不是取代教师。

### 3. 规模感(官方口径)

- 学年内工作日平均约 **26.9 万** 次 Khanmigo 交互;
- 自 2023 年上线累计超 **1.08 亿** 次交互;
- 即便如此,**约 15%** 有权限学生真正使用——这是本文的核心采纳教训。

## 三、技术方案推测

> 以下内容部分来自 Khan Academy 官方博客对实验与架构线索的披露,部分为推测。

- **官方已披露**:存在专门的 **math agent**,在后台实时校验计算与数学表达式,以保证回复中的数学正确性;团队通过换更快模型、缩短输出、收紧超时、预判是否需要校验等手段降延迟,并监控是否"过早泄题"。
- **官方已披露**:把学生在 Khan Academy 上的**结构化学习历史**(近期对错、技能水平、先修缺口等)注入辅导上下文后,next-item correctness 明显上升;隐私层面官方声明遵守 FERPA、COPPA 及州隐私法等学生数据相关要求。
- **官方已披露**:成效实验采用内部 A/B 平台,以"chance to win > 0.95 且护栏指标不恶化"作为上线门槛;核心指标包括响应延迟、next-item correctness、认知参与质量(passive / active / constructive),并监控泄题、数学错误率、每线程交互次数等护栏。
- **推测**:整体是"通用大模型对话 + 学科校验 Agent + 学习记录检索/摘要注入 + 安全与内容审核"的编排,而不是单一端到端模型;教育场景的差异化更多来自**学习科学指标与课堂工作流**,而非另起一个基座模型。
- **推测**:教师仪表盘与学区报表更多是传统数据产品 + 事件流,AI 辅导质量与采纳数据回流到这些面板,形成"教学决策闭环"。

## 四、商业模式(成效指标体系)

### 1. 结果定义:next-item correctness,而不是"解决率"

Khan Academy 将 **next-item correctness** 定义为:学生在接受辅导后,于**同一次练习会话、同一技能**上,在**不借助 Khanmigo** 的情况下做对**下一题**。官方明确把它当作**独立学习迁移**的度量,而不是"有 AI 帮忙时答对了没有"。

2025 年 10 月至 2026 年 4 月约六个月、覆盖逾 1500 万辅导线程的系列实验中,官方汇总:

| 改动 | 对 next-item correctness 的影响 |
|------|--------------------------------|
| 注入近期解题历史摘要(对错与尝试次数等) | **+3.4%**(约 60.8 万线程) |
| 暴露未掌握先修并先做短复习 | **+2.7%**(约 136 万线程) |
| **合计** | **约 +6.1%** |

另有发现:把难解析的原始日志硬塞给模型未必有效;改成纯文本并扩展近 24 小时相关线程后,认知参与有提升,但成效迁移仍需继续实验。降延迟的多项改动在护栏指标可接受的前提下落地——教育 AI 同样要算**规模化成本与体验延迟**。

这与客服的 Resolution Rate 形成清晰对照:

| | 智能客服 | Khanmigo |
|--|----------|----------|
| 主结果指标 | 会话是否解决/是否转人工 | 下一题能否独立做对 |
| 优化危险 | 刷解决率、弱隐式反馈 | 泄题提对率、被动接收答案 |
| 护栏 | 合规拒答、不计费边界 | 不泄题、数学正确、认知参与 |

### 2. 采纳是第一瓶颈

15% 使用率说明:在教育场景,**分发成功(学区买了、账号开通了)远不等于产品成功**。官方因此把产品从"可选聊天插件"改成"练习流程内的主动辅导",并用学区试点共创(约 5,100 名学生、多个美国学区)验证课堂可用性。对 PM 的直接含义是:教育 AI 的漏斗要单列 **access → engage → learn transfer**,不能只报交互总量。

### 3. 收费形态(公开信息层级)

Khan Academy 核心练习内容对个人学习者长期免费;Khanmigo / 学区能力走订阅或学区合作合同。学区合作博客曾披露含学生侧 Khanmigo 的 District Partnership 等档位及按生计价信息,但价目会随学年续约调整,且大型学区多为定制报价。**本文不把第三方整理的价目表当作唯一真相**;商业模式上更重要的是:**非营利 + 学区 B2B 采购 + 用学习成效说话**,与 Harvey 式高 ARPU 律所合同、Fin 式按次结果费都不同。

## 五、PM 视角的启示

1. **先定义"学到了"再优化模型。** next-item correctness 强迫产品优化"迁移",而不是"这一题在 AI 帮助下更像答对"。垂直场景都该问:有没有一个**反作弊式**的主指标,能挡住刷分捷径?
2. **采纳问题常常是交互位置问题,不是模型不够聪明。** 15% 使用率推动的是"嵌进练习、主动出手、降低发起成本",这是经典产品改版,不是换更大模型能单独解决的。
3. **成效实验要有护栏指标。** Khanmigo 同时盯延迟、泄题、数学错误与认知参与;只抬一个主指标时,教育产品很容易滑向"直接给答案"或"更快但更空的回复"。

## 相关阅读

- [智能客服:最成熟的 LLM 落地场景拆解](ai-customer-service.md) —— 对照另一种"结果"定义与按结果付费
- [Harvey 案例拆解:法律垂直的高合规企业 AI](harvey-legal-ai.md) —— 高合规垂直的另一条商业路径
- [如何构建你的评估集](../../02-pm-skills/model-evaluation/build-your-eval-set.md)
- [Badcase 分析方法论](../../02-pm-skills/model-evaluation/badcase-analysis.md)
- [AI 是黑盒,用户不信任怎么办?](../../04-interview/basics/ai-blackbox-trust.md)
- [数据合规红线:用户数据能不能用来训练](../../02-pm-skills/data-annotation/data-compliance.md)

## 参考资料

- [Learning in the Open: What AI Is (and Isn't) Changing](https://blog.khanacademy.org/learning-in-the-open-what-ai-is-and-isnt-changing/) — Khan Academy 官方博客(约 15% 采纳、交互量、重设计要点、2026 夏学区 rollout),2026-07 访问
- [How Khan Academy Is Building a Better AI Tutor: Our Most Recent Learnings](https://blog.khanacademy.org/how-khan-academy-is-building-a-better-ai-tutor-our-most-recent-learnings/) — 官方博客(next-item correctness 定义与 +6.1%、math agent、A/B 方法),2026-07 访问
- [How Pilot Districts Shaped the Reimagined Khan Academy](https://blog.khanacademy.org/built-in-the-open-how-pilot-districts-shaped-the-reimagined-khan-academy/) — 官方博客(练习内嵌 AI、仪表盘、学区试点共创),2026-07 访问
- [Becoming a Khan Academy Districts Partner](https://blog.khanacademy.org/becoming-a-khan-academy-districts-partner/) — 学区合作与 Khanmigo 档位说明,2026-07 访问
- [Khan Academy Districts 2026–27 Renewal Guide](https://blog.khanacademy.org/khan-academy-districts-2026-27-renewal-guide/) — 2026–27 续约与 Reimagined 时间线,2026-07 访问