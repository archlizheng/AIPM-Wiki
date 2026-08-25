---
title: CONTRIBUTING
type: note
permalink: aipmwiki/contributing
---

# 贡献指南

感谢你愿意为 AIPM Wiki 做贡献!本仓库是内容型知识库,贡献门槛很低——修正一个错别字也是有价值的贡献。

## 你可以贡献什么

| 类型 | 方式 |
|------|------|
| 纠错(错别字、事实错误、失效链接) | 直接提 PR,或提 Issue 说明位置 |
| 面试题 / 面经 | 用 [templates/interview-question.md](templates/interview-question.md) 或 [templates/interview-experience.md](templates/interview-experience.md) 模板,提 PR |
| 案例拆解 | 用 [templates/case-study.md](templates/case-study.md) 模板,提 PR |
| 知识类文章 | 先提 Issue 说明选题,认领后再写,避免撞车 |
| 资源推荐 | 直接在 `docs/05-resources/` 对应文件追加,附一句话推荐理由 |

## 内容规范

1. **文件命名**:英文小写 + 连字符,如 `what-is-rag.md`;文章标题用中文写在文件首行 `# 标题`。
2. **存放位置**:按内容类型放入 `docs/` 对应编号目录;图片放 `assets/` 下的同名子目录,使用相对路径引用。
3. **使用模板**:面试题、面经、案例拆解必须使用 `templates/` 中的对应模板结构,保证全库格式统一。
4. **原创或注明出处**:引用他人内容必须附来源链接;禁止直接搬运付费课程内容。
5. **中立客观**:面经中不进行人身攻击,公司相关内容基于事实。
6. **更新索引**:新增文章后,在所在目录的 `README.md` 索引中追加条目。

## PR 流程

1. Fork 本仓库,创建分支(如 `add/rag-interview-question`)
2. 按上述规范添加或修改内容
3. 提交 PR,按 PR 模板填写说明
4. 维护者 review 后合并;可能会提出修改意见,请留意评论

## 内容创作说明

本仓库部分内容由 AI 辅助创作,所有内容均经维护者审校后发布。单篇文章不另行标注。发现事实错误请提 Issue 或 PR,我们会尽快修正。

## 行为准则

友善、尊重、对事不对人。我们欢迎所有背景的贡献者,尤其是正在转行路上的同学——你的疑问本身就是宝贵的贡献素材。