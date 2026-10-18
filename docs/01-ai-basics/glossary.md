---
title: glossary
type: note
permalink: aipmwiki/docs/01-ai-basics/glossary
---

# AI 术语速查表

> 面试和日常工作高频术语。每条力求一句话说清「是什么」+ 一句话说清「PM 为什么要关心」。
> 欢迎 PR 补充,按字母/拼音归类到对应小节。

## 模型基础

| 术语 | 是什么 | PM 为什么关心 |
|------|--------|---------------|
| LLM(大语言模型) | 在海量文本上预训练的生成式模型,通过预测下一个 token 来生成文本 | 当前绝大多数 AI 产品的底座,理解它的能力边界是 AI PM 的基本功 |
| Transformer | 以自注意力为核心的神经网络架构,是当今主流大模型的地基 | 理解上下文窗口、长文本成本为何随长度陡增的架构原因;面试常作技术边界探测 |
| 自注意力 / 多头注意力 | 处理某词时动态决定「多看哪些其他词」;多头=多组关注方式并行再综合 | 能用产品语言讲清即可,不必背公式;详见[Transformer 架构一文](llm/transformer-architecture.md) |
| Token | 模型分词器使用的文本片段单位；同一文本在不同模型中 token 数可能不同 | API 按 token 计费;上下文窗口按 token 计算,直接决定产品成本和功能上限 |
| 上下文窗口(Context Window) | 模型单次能"记住"的最大 token 数 | 决定能不能塞下长文档、多轮对话历史;超窗需要截断或摘要策略 |
| 幻觉(Hallucination) | 模型一本正经地编造不实内容 | AI 产品最大的信任风险,需要产品层面设计缓解手段(引用来源、RAG、免责声明) |
| 温度(Temperature) | 控制生成随机性的参数,越高越发散 | 客服类场景调低求稳,创意类场景调高求新,是 PM 可以直接决策的参数 |
| 多模态(Multimodal) | 模型能同时理解/生成文本、图像、音频等多种模态 | 决定产品形态的可能性边界 |

## 训练与优化

| 术语 | 是什么 | PM 为什么关心 |
|------|--------|---------------|
| 预训练(Pre-training) | 在海量通用语料上训练基础模型 | 成本极高,绝大多数公司不做,PM 需要知道"我们不训模型,我们用模型" |
| 微调(Fine-tuning / SFT) | 用少量领域数据继续训练,让模型适配特定任务 | 当 Prompt 优化到头时的下一个选项;需要评估数据成本和收益 |
| RLHF | 基于人类反馈的强化学习,让模型输出更符合人类偏好 | 理解"模型为什么这么听话",以及标注团队在做什么 |
| 蒸馏(Distillation) | 用大模型的输出训练小模型 | 降本手段:需用同一业务评测与全部成本验证，不能预设固定效果或降本比例 |

## 应用架构

| 术语 | 是什么 | PM 为什么关心 |
|------|--------|---------------|
| RAG(检索增强生成) | 先从知识库检索相关内容,再连同问题一起交给模型生成答案 | 企业知识问答类产品的标准架构;缓解幻觉、让模型"知道"私有知识的首选方案 |
| Agent(智能体) | 能自主规划、调用工具、多步执行任务的 AI 系统 | 当前产品创新的主战场;可靠性和成本是落地的核心挑战 |
| Agent Skills | 以 `SKILL.md` 为核心的可复用能力包,按需加载领域流程与规范 | 把组织 know-how 外化成可版本管理资产;与 MCP 互补(手册 vs 连接);详见[Agent Skills 一文](llm/agent-skills.md) |
| MCP(Model Context Protocol) | Agent 连接外部工具与数据的开放协议(Host/Client/Server;Tools/Resources/Prompts) | 「接到公司系统」的标准方式;和 Skills 搭配:MCP 触达世界,Skills 规定怎么做;详见[MCP 一文](llm/mcp.md) |
| Function Calling / 工具调用 | 模型输出结构化指令去调用外部 API | 让 AI 产品从"聊天"进化到"办事"的关键机制 |
| Embedding(向量化) | 把文本转成高维向量,语义相近的文本向量距离近 | 搜索、推荐、RAG 的底层;理解它才能理解"语义检索"和"关键词检索"的区别 |
| 向量数据库 | 专门存储和检索 Embedding 的数据库 | RAG 架构的标配组件,选型影响成本和检索质量 |

## 评估与运营

| 术语 | 是什么 | PM 为什么关心 |
|------|--------|---------------|
| 评测集(Eval Set) | 一组包含输入、预期行为、完成证据与评分规则的用例；开放任务不一定有唯一答案 | AI PM 的核心资产;没有评测集,一切"优化"都是玄学 |
| Badcase | 效果不佳的具体案例 | AI PM 日常:收集 badcase → 归因 → 推动优化,是最重要的迭代循环 |
| 首 token 延迟(TTFT) | 从发出请求到收到第一个字的时间 | 对话产品的核心体验指标,流式输出就是为了优化它的感知 |
| A/B 测试 | 线上分流量对比不同方案的效果 | AI 产品的效果评估最终要回到业务指标,而不是模型指标 |

---

*本表持续更新中,发现缺失术语欢迎直接 PR 补充。*

## 任务执行、交互与采用

| 术语 | 是什么 | 主文与产品意义 |
|---|---|---|
| Harness（执行配套系统） | 将模型接到工具、上下文、任务控制和检查的配套机制；不同厂商覆盖范围不同 | [平台选型](../02-pm-skills/cost-and-tech/agent-platform-selection-and-governance.md)：先列责任，不把它当一种新模型 |
| Runtime（运行时） | 让任务实际运行、维持会话与资源的环境和服务 | [平台选型](../02-pm-skills/cost-and-tech/agent-platform-selection-and-governance.md)：自建与托管都需明确身份、状态和退出成本 |
| 幂等（Idempotency） | 对同一业务意图重复执行，不产生重复有效副作用 | [生产化](../02-pm-skills/ai-product-operations/agent-product-design.md)：重试沿用业务键，不能每次超时新建 |
| Trace（执行轨迹） | 关联任务中的模型、工具、权限、状态与结果事件 | [Agent评测](../02-pm-skills/ai-product-operations/agent-evaluation-and-observability.md)：与最终业务结果联合判断 |
| 行为断言 | 检查任务中必须满足或禁止出现的行为 | [Agent评测](../02-pm-skills/ai-product-operations/agent-evaluation-and-observability.md)：不强制所有合法任务采用唯一工具顺序 |
| Memory lifecycle（记忆生命周期） | 对记忆资格、读写、纠正、过期与删除的持续管理 | [长期记忆](../02-pm-skills/ai-product-operations/agent-memory-design.md)：权威制度和业务状态不能随意写成个人偏好 |
| TTL（存活期限） | 数据或资源在指定规则下保留的时长 | [长期记忆](../02-pm-skills/ai-product-operations/agent-memory-design.md)：字段过期不等于副本、索引和日志已全部删除 |
| OCR（光学字符识别） | 将图像中的文字识别为文本 | [文档智能](../02-pm-skills/prd-and-design/document-intelligence-design.md)：识别文字、还原版面、抽取字段是不同验收任务 |
| Barge-in（语音打断） | 用户插话时停止或调整系统播报 | [实时语音](../02-pm-skills/prd-and-design/realtime-voice-agent-design.md)：停止声音不等于撤销已发动作 |
| Computer Use（电脑界面操作） | 通过屏幕和输入动作使用应用 | [电脑操作设计](../02-pm-skills/prd-and-design/computer-use-product-design.md)：动作发生后仍要验证业务状态 |
| Cohort（队列） | 按共同起点或条件选出的一组用户或任务 | [采用](../02-pm-skills/ai-product-operations/poc-to-production-adoption.md)：次周复用应同时解释是否再次有需求 |
| Credit／Step／Resolution | 厂商定义的额度、步骤或解决单位，彼此不天然等价 | [定价](../02-pm-skills/cost-and-tech/ai-pricing-and-monetization.md)：费用单位、用户任务与业务结果分别记录 |
| Beta／Preview／滚动发布 | 分别表示测试或预览状态、按批开放；具体承诺由对应产品说明 | [模型盘点](llm/model-landscape.md)：公告日不等于全部账号可用日 |

这些定义用于本库学习导航，协议版本与厂商功能以所链接主文的直接来源为准。
