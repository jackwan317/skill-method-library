# 后续研究批次

## Batch 2：安全 / CI / 工程流程

1. OpenAI Plugins `security-scan`
2. OpenAI Plugins `threat-model`
3. OpenAI Plugins `gh-fix-ci`
4. Superpowers `systematic-debugging`
5. Superpowers `subagent-driven-development`

## Batch 3：MCP / 外部工具集成

1. Anthropic `build-mcp-server`
2. Cloudflare Agents/Wrangler skills
3. Microsoft `generate-mcp-app-ui`
4. GuDaStudio `collaborating-with-codex`

## Batch 4：文档 / 知识处理

1. Daymade `doc-to-markdown`
2. Antigravity `pdf-conversion-router`
3. Anthropic document skills
4. Skill Seekers document-to-skill workflow

## Batch 5：商业 / 产品 / 战略

1. Strategy `decision-memo`
2. Strategy `market-mapping`
3. Strategy `business-case-builder`
4. Strategy `transformation-roadmap`

## 每个 Skill 的执行规则

- 一个 Skill = 一个独立 sub agent。
- sub agent 只读研究，不运行目标 Skill，不修改目标仓库。
- 新流程不用下载/归档原文，只输出方法论报告。
- 输出必须覆盖：任务、触发、输入输出、步骤、工具/脚本/MCP/API、决策点、验证、可迁移写法、分类。
- 每跑完一个 sub agent，主会话立刻把报告落盘到 `skills/<category>/<name>.md`，更新 `catalog/by-task-type.md` 的报告路径和备注状态，提交并推送 GitHub。
- 状态必须及时更新为 `研究中` / `已有报告` / `跳过` / `需复查`，避免后续 sub agent 重复作业。


## Current deep-dive progress

- ✅ Anthropic `hook-development` — studied, archived, documented at `skills/agent-automation/anthropic-hook-development.md`.
- ✅ Anthropic `command-development` — studied, archived, documented at `skills/agent-automation/anthropic-command-development.md`.
- ✅ Anthropic `claude-automation-recommender` — studied, archived, documented at `skills/agent-automation/anthropic-claude-automation-recommender.md`.
- ⏭️ Next: Anthropic `knowledge-synthesis`.
