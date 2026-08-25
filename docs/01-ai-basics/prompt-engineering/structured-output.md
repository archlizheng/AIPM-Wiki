---
title: structured-output
type: note
permalink: aipmwiki/docs/01-ai-basics/prompt-engineering/structured-output
---

# 结构化输出:让模型稳定吐出 JSON

## 为什么产品化必须要结构化输出

聊天场景里,模型输出一段自然语言就够了——人眼睛扫一遍就懂。但只要 Prompt 的输出要被**下游代码消费**,情况就完全不同了:前端要拿某个字段渲染卡片、后端要把"是否需要转人工"这个布尔值写进工单系统、另一个 Agent 要把这次的结果当作下一步的输入。这时候,模型吐出的东西必须是一份"契约"——字段名固定、类型固定、结构可预测,而不是一段"大概率长这样"的自然语言。

这就是"结构化输出"要解决的问题:让模型的输出从"人读的文字"变成"机器能直接解析的数据"。这件事做不稳,AI 功能就永远卡在 Demo 阶段——工程同学不敢把它接入真正的业务链路,因为谁也不能保证下一次调用不会因为多了一个逗号、少了一个引号而让 JSON 解析直接崩掉。

## 从"求它输出 JSON"到官方 Structured Outputs:一段演进史

### 第一阶段:口头请求(不可靠)

最朴素的做法就是在 Prompt 末尾加一句"请只输出 JSON,不要有其他文字"。这个阶段的典型问题:

- 模型在 JSON 前后加了"好的,以下是结果:"这类客套话,导致 `JSON.parse` 直接报错
- 字段名走样(比如该出 `user_name` 却写成 `userName` 或 `姓名`)
- 该必填的字段被漏掉,该是数字的字段被写成字符串
- 每次输出的字段顺序、缩进都不一样,给字符串层面的比对和调试带来麻烦

这个阶段本质上是在"祈祷"模型听话,产品上必须配合大量的正则清洗和异常兜底代码,稳定性无法保证。

### 第二阶段:JSON 模式(保证语法有效,不保证结构)

OpenAI 较早推出的 JSON 模式(`response_format: { type: "json_object" }`)解决了"语法是否有效"的问题——模型一定会输出合法可解析的 JSON,但**不保证**里面有哪些字段、字段类型对不对。官方现在把这个模式定性为"legacy"(遗留方案):它只能保证语法正确,拿到手之后依然需要自己写校验逻辑,一旦模型漏了必填字段或塞进了非法枚举值,照样会在下游炸掉。

### 第三阶段:借道工具调用(Tool Use / Function Calling)

在原生 Structured Outputs 出现之前,业界的主流workaround 是"借用工具调用格式":把想要的输出结构定义成一个"工具"的入参 schema,让模型以"调用这个工具"的方式吐出参数,从而间接获得结构化数据。这个方法比裸 JSON 请求靠谱不少,但按 OpenAI 官方给出的评测数据,传统 function calling 的 schema 遵从率大约在 86% 左右——离"生产级别可靠"还有距离。

### 第四阶段(2026 年现状):原生 Structured Outputs,三家均已落地

到 2026 年 7 月,三家官方文档确认的现状是:结构化输出已经从"Prompt 技巧"升级为"模型原生能力",底层通过**约束解码(constrained decoding)**在 token 生成层面直接限制模型只能生成符合 schema 的内容,而不是靠模型"自觉"。

| 维度 | Anthropic(Claude) | OpenAI | Google(Gemini) |
|------|-------------------|--------|-----------------|
| 功能名称 | Structured Outputs(含 JSON Outputs + Strict Tool Use 两种模式) | Structured Outputs(Strict Mode) | Structured output(`responseSchema`) |
| 核心参数 | `output_config.format`(类型 `json_schema`);工具入参加 `strict: true` | `response_format`(Chat Completions)/ `text.format`(Responses API),需设 `strict: true` | 设置 `responseMimeType: "application/json"` + `responseSchema` |
| SDK 便捷用法 | Python 传 Pydantic 模型给 `output_format`;TypeScript 用 `zodOutputFormat` | Pydantic(Python)/ Zod(JS)自动生成 schema | Pydantic(Python)/ Zod(JS) |
| 官方给出的可靠性数据 | 严格工具调用场景下 schema 合规率约 99.8% | Structured Outputs 评测中约 100% schema 合规(对比传统 function calling 约 86%) | 未给出单一数字,但强调约束生成保证匹配 schema |
| Schema 能力边界 | 支持 `enum`/`const`/`$ref`/字符串格式;**不支持**递归 schema、数值范围约束(`minimum`/`maximum`)、字符串长度约束 | 依赖 JSON Schema 子集,建议保持结构扁平 | 支持 JSON Schema 子集,**支持**通过 `$ref` 实现递归结构;强调 `title`/`description` 引导生成质量 |
| 与工具调用的关系 | 可与 Strict Tool Use 同时使用:一次调用里既保证工具入参合法,又保证最终回复符合 JSON Schema | Structured Outputs 更适合"面向用户的最终输出",工具调用场景另有 function calling 机制配合 | 可与 Google Search / 代码执行 / Function Calling 等工具协同使用 |

有意思的是三家在细节上的分歧点:Anthropic 明确不支持递归 schema,Google 反而把递归结构(自引用)作为亮点能力提出;Google 特别强调 schema 里的属性顺序(property ordering)会被保留并影响生成质量,如果 Prompt 里的示例顺序和 schema 声明顺序不一致,反而可能把模型搞糊涂。这提醒我们:**同一个"结构化输出"的说法,三家的能力边界并不完全等价**,选型和联调时要按官方文档逐条核对,而不是想当然地认为"都支持 JSON Schema 就是一回事"。

## Schema 设计建议

不管用哪家的实现,设计 schema 时几条实用经验是共通的:

1. **能扁平就不要嵌套**。深层嵌套的 schema 不仅模型生成更容易出错,Google 官方也明确提示"过大或深度嵌套的 schema 可能被直接拒绝"。
2. **给每个字段写清楚的 `description`**,而不是只靠字段名猜意图。字段名 `status` 到底是"审核状态"还是"库存状态",光看名字模型也会懵;一句话的 description 能显著提升准确率。
3. **能用 `enum` 就不要用自由字符串**。比如"情感倾向"字段用 `enum: ["positive","negative","neutral"]`,比让模型自己造词稳定得多——但要注意 Anthropic 文档提示的一个边界情况:模型返回的枚举值大小写可能不完全一致,校验时建议做归一化处理(如统一转小写再比较)。
4. **明确标出哪些字段是 `required`**,可选字段允许为空,而不是让模型"猜"要不要填。
5. **善用 `additionalProperties: false`**,避免模型顺手多塞一些没有约定过的字段,污染下游系统的解析逻辑。
6. **示例与 schema 的顺序保持一致**(尤其在使用 Gemini 时),Prompt 里给的样例字段顺序应该和 schema 声明顺序对齐。
7. **优先复用代码里已有的类型定义**——用 Pydantic / Zod 直接生成 schema,而不是手写一份 JSON Schema 再维护两份定义,这是三家 SDK 都推荐的做法,也顺带解决了"代码和 Prompt 里的字段定义容易脱节"的老问题。

## 失败兜底:校验与重试

即便用了官方原生的 Structured Outputs,依然存在无法保证输出的"合法边界情况",三家文档都提到类似的问题:

- **模型拒绝回答**(比如触发安全策略):这种情况下返回的内容可能根本不符合 schema,产品上必须把"拒绝"当成一个独立的错误类型来处理,而不是简单地尝试解析后崩溃。
- **达到最大 token 限制被截断**:输出会是一段不完整的 JSON,同样需要显式检测并触发重试或降级。
- **枚举值大小写、格式细节的微小偏差**:即使 schema 合规率号称接近 100%,生产环境依然建议保留一层轻量校验(比如用 Pydantic/Zod 再校验一遍并做归一化),而不是完全裸信任模型输出。

因此,一个稳妥的产品化流程应该是:**结构化输出 API 打底(减少 90% 以上的低级错误)+ 应用层轻量校验(兜住剩下的边界情况)+ 校验失败自动重试一次(通常能解决瞬时性错误)+ 重试仍失败则走人工兜底或降级话术**,而不是假设"用了官方功能就万事大吉"。校验失败的 badcase 也应该沉淀回评测集,用于后续排查 schema 设计本身是否有问题,方法论可参考 [从零建设评测集](../../02-pm-skills/model-evaluation/build-your-eval-set.md)。

## 这对 PM 写 PRD 意味着什么

结构化输出不是研发的"内部实现细节",它直接决定了一份 AI 功能的 PRD 该怎么写:

- **PRD 里不能只写"模型输出摘要",要明确写清楚输出的字段契约**——字段名、类型、是否必填、取值范围(枚举列表)、给一个具体的示例值。这一份契约其实就是本篇讲的 JSON Schema 的产品语言版本,PM 写清楚了,研发才能对应实现,QA 才有验收依据。
- **必须明确定义"模型拒绝/生成失败"时的产品体验**,而不是让这种情况变成一个未定义的空白页——是显示"暂时无法生成,请重试",还是自动降级成一个更简单的兜底文案,这是产品设计决策,不能留给工程随手处理。
- **要求技术方案优先使用官方 Structured Outputs / 严格工具调用,而不是纯靠 Prompt 里加"请输出JSON"这种口头约束**——这是当前(2026年)行业公认更可靠的工程方案,PM 在技术评审时可以直接把这一点写进验收标准。
- **契约一旦定义,变更成本很高**:下游系统(前端、数据看板、其他 Agent)一旦开始消费某个字段,后续再改字段名或类型就是一次跨团队的联调成本,PRD 阶段就应该尽量把 schema 设计稳定下来,而不是等上线后频繁变更。

结构化输出解决的是"格式稳不稳"的问题,而“说什么、怎么说”仍然要靠 Prompt 本身把关,两者是配合关系而非替代关系,基本功部分详见 [Prompt 基本功](prompt-basics.md)。

## 相关阅读

- [Prompt 基本功:角色、任务、约束、示例、输出格式](prompt-basics.md)
- [系统提示词设计与 Prompt 工程化:把玄学变工程](system-prompt-and-prompt-ops.md)
- [从零建设评测集:用例从哪来、标准答案怎么定](../../02-pm-skills/model-evaluation/build-your-eval-set.md)
- [什么是大模型的幻觉?产品上怎么缓解?](../../04-interview/basics/hallucination-mitigation.md)
- [AI 术语速查表](../glossary.md)

## 参考资料

- [Structured outputs](https://platform.claude.com/docs/en/build-with-claude/structured-outputs) — Anthropic 官方文档,2026-07 访问
- [Structured model outputs](https://developers.openai.com/api/docs/guides/structured-outputs) — OpenAI 官方文档,2026-07 访问
- [Structured output](https://ai.google.dev/gemini-api/docs/structured-output) — Google AI for Developers 官方文档,2026-07 访问