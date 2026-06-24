# Context Brief

仓库：`/home/ubuntu/skill-method-library`  
远端：<https://github.com/jackwan317/skill-method-library>

## 长线目标

研究优秀 AI Agent Skills / Plugins / Workflows 是如何完成任务的。重点不是跑 Skill，而是理解它们的做事流程：触发、输入输出、步骤、工具/脚本/MCP/API、关键决策、验证 gate、可迁移方法。

## 固定规则

1. 长线任务分两部分：A）按信息源发现优质 Skills 并加入候选池；B）按候选池逐项用 sub agent 输出报告。
2. 一个 Skill / Workflow = 一个独立 sub agent 只读研究。
3. 新流程不用下载或归档原文，只输出方法论报告。历史 `originals/` 保留但不再作为必做项。
4. Sub agent 跑完一个，主会话立即写报告到 `skills/<category>/<name>.md`，更新 catalog 状态，commit/push/verify；不用等整批完成。
5. 每完成一个节点，都必须更新 `catalog/by-task-type.md` 的 `报告路径` 与 `备注状态`，避免重复作业。
6. 每个候选必须写“为什么优秀”：官方/权威、stars/forks、社区热度、结构完整、验证强、可迁移价值。
7. 不追求速度，不并行跑很多 sub agent；优先研究清楚。

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

按候选池选择下一个未完成项目；当前队列下一项是 Anthropic `knowledge-synthesis`。执行时先检查 `catalog/by-task-type.md` 状态，避免重复研究。


## 已完成研究

- Anthropic `hook-development` → `skills/agent-automation/anthropic-hook-development.md`
- Anthropic `command-development` → `skills/agent-automation/anthropic-command-development.md`
- Anthropic `claude-automation-recommender` → `skills/agent-automation/anthropic-claude-automation-recommender.md`
