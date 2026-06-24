# Skill Method Library

这个仓库用于系统研究优秀 AI Agent Skills / Plugins / Workflows：不是收集“提示词”，而是拆解它们如何完成任务。

目标：

1. 从 OpenAI Skills / Codex Plugins、Anthropic Claude Skills、Claude Code 社区、MCP/Agent workflow、Cursor rules 等来源筛选优秀 Skills。
2. 按“任务类型”归类：某一类任务有哪些优秀 Skill 可以完成。
3. 每个 Skill 单独输出一份方法论研究文档：它如何触发、如何拆步骤、用什么工具/脚本/MCP/API、如何验证、哪些模式可迁移。
4. 后续每个 Skill 用一个独立 sub agent 研究，避免上下文污染。

## 仓库结构

```text
catalog/                    # 总目录：按任务类型归类
sources/                    # 来源与候选池
skills/<category>/<skill>.md # 每个 Skill 一份研究文档
originals/                  # 已研究/已筛选 Skill 原文或原目录，便于直接导出使用
templates/                  # 研究文档模板、评分模板
backlog/                    # 后续批次计划
```

## 当前已完成

- 第一批来源筛选：OpenAI/Codex、Anthropic/Claude、开源共享平台。
- 第一版任务类型目录：`catalog/by-task-type.md`，已新增“入选理由/为什么优秀”与“原文路径”列
- 已建立原文归档目录：`originals/`，保存已研究 Skill 的原始文件/目录
- 已完成 3 个 Skill 深度研究：
  - `skills/meta-skill-development/openai-plugin-creator.md`
  - `skills/meta-skill-development/anthropic-skill-creator.md`
  - `skills/frontend-ui/openai-frontend-app-builder.md`

## 研究原则

- 优先官方、高星、维护活跃、结构清晰、有真实工作流和验证步骤的 Skill。
- 不运行目标 Skill；只阅读、理解、归纳其完成任务的方法。
- 每个 Skill 独立 sub agent 研究。
- 文档重点是“任务如何被完成”，而不是复述 README。
- 关注：脚本、工具、MCP、API、输入输出、验证 gate、失败恢复、可迁移模式。

## 新增硬规则：优秀理由 + 原文归档

每个候选 Skill 必须回答两个问题：

1. **为什么它值得学？**  
   需要写明入选理由，例如：官方维护、GitHub stars/forks 高、近期 trending、社区讨论多、结构完整、验证流程强、工具链成熟、任务价值高、可迁移性强。

2. **原文在哪里？**  
   对已研究或准备研究的 Skill，要在 `originals/` 下保存原始 `SKILL.md` 及关键 `scripts/`、`references/`、`agents/`、`assets/` 等文件，让大哥可以直接从本仓库查看、复制、导出。
