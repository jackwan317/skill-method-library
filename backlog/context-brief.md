# Context Brief

仓库：`/home/ubuntu/skill-method-library`  
远端：<https://github.com/jackwan317/skill-method-library>

## 长线目标

研究优秀 AI Agent Skills / Plugins / Workflows 是如何完成任务的。重点不是跑 Skill，而是理解它们的做事流程：触发、输入输出、步骤、工具/脚本/MCP/API、关键决策、验证 gate、可迁移方法。

## 固定规则

1. 一个 Skill / Workflow = 一个独立 sub agent 只读研究。
2. 主会话负责写入仓库、归档原文、更新 catalog、commit/push/verify。
3. 每个候选必须写“为什么优秀”：官方/权威、stars/forks、社区热度、结构完整、验证强、可迁移价值。
4. 每个深度研究必须保存原文到 `originals/`，包括 `SKILL.md` 和关键 `references/`、`scripts/`、`agents/`、`templates/`、`assets/`。
5. 每个研究文档放到 `skills/<category>/<name>.md`。
6. 不追求速度，不并行跑很多 sub agent；优先研究清楚。

## 当前优先顺序

1. Anthropic `hook-development`
2. Anthropic `command-development`
3. Anthropic `claude-automation-recommender`
4. Anthropic `knowledge-synthesis`
5. OpenAI Agents SDK workflow
6. Cloudflare Agents Skill
7. Cloudflare MCP Server Skill
8. Sentry Error Monitoring / `sentry-workflow`
9. Context7 docs-for-LLMs workflow
10. BMAD-METHOD
11. wshobson/agents
12. Addy Osmani `browser-testing-with-devtools`

## 当前要执行

先研究 Anthropic `hook-development`：
<https://github.com/anthropics/claude-plugins-official/tree/main/plugins/plugin-dev/skills/hook-development>


## 已完成研究

- Anthropic `hook-development` → `skills/agent-automation/anthropic-hook-development.md`
- Anthropic `command-development` → `skills/agent-automation/anthropic-command-development.md`
