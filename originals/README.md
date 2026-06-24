# Original Skill Archives

这里保存已筛选/已研究 Skill 的原文或原目录，方便直接查看、复制、导出和安装。

注意：这些文件来自上游公开仓库，版权和许可证归原作者/原仓库所有。每个目录内尽量保留原始结构；研究文档只做方法论总结，不替代原文。

## 已归档

| Skill | 上游链接 | 本仓库原文路径 | 入选理由摘要 |
|---|---|---|---|
| OpenAI `plugin-creator` | https://github.com/openai/plugins/tree/main/.agents/skills/plugin-creator | `originals/openai/plugins/.agents/skills/plugin-creator/` | OpenAI 官方插件脚手架 Skill；包含可执行脚本与插件 manifest/marketplace 规范，是“元 Skill/脚手架生成器”的优秀样本。 |
| Anthropic `skill-creator` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/skill-creator/skills/skill-creator | `originals/anthropic/claude-plugins-official/plugins/skill-creator/skills/skill-creator/` | Anthropic 官方元 Skill；包含 eval、grader、comparator、description 优化、打包脚本，是 Skill 工程化闭环的高质量范式。 |
| OpenAI `frontend-app-builder` | https://github.com/openai/plugins/tree/main/plugins/build-web-apps/skills/frontend-app-builder | `originals/openai/plugins/plugins/build-web-apps/skills/frontend-app-builder/` | OpenAI 官方前端构建 Skill；流程覆盖 Image Gen 设计、设计系统提取、实现、浏览器视觉验证和 final response contract，质量 gate 明确。 |

## 后续归档规则

每新增一个研究文档，必须同步归档：

1. 原始 `SKILL.md`。
2. 关键 `references/`、`scripts/`、`templates/`、`agents/`、`assets/`。
3. 原始链接、许可证/上游说明。
4. 在 `catalog/by-task-type.md` 里写明“入选理由”和“原文路径”。
