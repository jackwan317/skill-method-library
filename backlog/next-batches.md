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
- 输出必须覆盖：任务、触发、输入输出、步骤、工具/脚本/MCP/API、决策点、验证、可迁移写法、分类。
- 主 agent 负责把文档落盘、更新 catalog、提交 GitHub。
