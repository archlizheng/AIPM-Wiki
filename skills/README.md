---
title: README
type: note
permalink: aipmwiki/skills/readme
---

# AIPM Wiki 配套 Skills

本目录是面试 Skill 的注册与分发入口。目前不复制 Skill 的完整内容，官方仓库保持独立维护；克隆本项目后，按需安装即可。

## 面试 Skills

### interview-self-introduce

- 仓库：[archlizheng/interview-self-introduce](https://github.com/archlizheng/interview-self-introduce)
- 用途：根据 JD、简历和面试轮次生成 30/60/90/120 秒自我介绍，支持中文、英文、双语和反模板改写。

### interview-assessment

- 仓库：[archlizheng/interview-assessment](https://github.com/archlizheng/interview-assessment)
- 用途：基于 JD、简历和面试记录做岗位匹配、面试准备和面试后复盘，支持候选人和招聘方视角。

## 安装

推荐使用 Skills CLI：

```bash
npx skills add archlizheng/interview-self-introduce
npx skills add archlizheng/interview-assessment
```

如果环境没有 Skills CLI，也可以直接克隆到 Codex 的 Skill 目录：

```bash
git clone https://github.com/archlizheng/interview-self-introduce.git "${CODEX_HOME:-$HOME/.codex}/skills/interview-self-introduce"
git clone https://github.com/archlizheng/interview-assessment.git "${CODEX_HOME:-$HOME/.codex}/skills/interview-assessment"
```

安装完成后重启 Codex，使新 Skill 生效。