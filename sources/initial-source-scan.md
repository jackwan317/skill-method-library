# 第一批优秀 Skills 来源扫描

本批次由 3 个独立 sub agent 分别扫描：OpenAI/Codex、Anthropic/Claude、开源共享平台。

## P0/P1 来源

| 来源 | URL | 证据 | 适合任务类型 | 优先级 |
|---|---|---|---|---|
| OpenAI Codex | https://github.com/openai/codex | OpenAI 官方；高星；包含 repo-level `AGENTS.md` | Agent 指令、代码工程规范 | P0 |
| OpenAI Plugins | https://github.com/openai/plugins | OpenAI 官方；包含大量 `.agents/skills` 与插件 Skill | 插件、前端、安全、CI、GitHub、iOS、企业工具 | P0 |
| OpenAI Codex Plugin for Claude Code | https://github.com/openai/codex-plugin-cc | OpenAI 官方；Codex 与 Claude Code 协作 | 代码审查、对抗 review、委派 | P0 |
| Anthropic Claude Plugins Official | https://github.com/anthropics/claude-plugins-official | Anthropic 官方；包含 skill-creator、MCP、前端、报告等 | Skill 创建、MCP、Claude 工作流 | P0 |
| OfficialSkills | https://officialskills.sh/ | 聚合 Anthropic/OpenAI/Cloudflare/Sentry/Figma/Trail of Bits 等官方技能 | 官方 skills 分发入口 | P0 |
| obra/superpowers | https://github.com/obra/superpowers | 高热社区项目；支持 Claude/Codex/Cursor/Gemini | 软件工程方法论、TDD、调试、subagent | P1 |
| Trail of Bits skills-curated | https://github.com/trailofbits/skills-curated | 安全公司维护；安全与逆向质量高 | 安全审计、fuzzing、逆向、PR review | P1 |
| VoltAgent awesome-agent-skills | https://github.com/VoltAgent/awesome-agent-skills | 大型 curated list，收录官方 Skills | 来源索引、跨生态检索 | P1 |
| awesome-claude-code | https://github.com/hesreallyhim/awesome-claude-code | Claude Code 生态索引 | workflows、commands、hooks、MCP | P1 |
| mattpocock/skills | https://github.com/mattpocock/skills | 工程实践型 Skills | TDD、bug 诊断、架构、PRD/Issues | P1 |
| github/spec-kit | https://github.com/github/spec-kit | GitHub 官方；spec-driven development | specify/clarify/plan/tasks/implement | P1 |
| NeoLabHQ/context-engineering-kit | https://github.com/NeoLabHQ/context-engineering-kit | 多 Skill、context engineering | 多 Agent、TDD、评审、Git、MCP | P1 |
| daymade/claude-code-skills | https://github.com/daymade/claude-code-skills | 社区实用型 Skill 库 | 文档、研究、GitHub、音视频、PPT/PDF | P1 |
| Microsoft power-platform-skills | https://github.com/microsoft/power-platform-skills | Microsoft 官方 | 企业平台、Power Apps、Dataverse | P2 |
| antigravity-awesome-skills | https://github.com/sickn33/antigravity-awesome-skills | 大型 Skill 集合，需二次筛选 | DevOps、QA、安全、数据、增长 | P2 |
| awesome-cursorrules | https://github.com/PatrickJS/awesome-cursorrules | 高星 Cursor rules | 框架规范、反幻觉规则 | P2 |
| GuDaStudio/skills | https://github.com/GuDaStudio/skills | 中文生态，多模型协作 | Claude/Codex/Gemini 协作 | P2 |
| strategy-skills-for-claude | https://github.com/aapersh/strategy-skills-for-claude | 结构清晰的商业战略 Skills | 市场、战略、决策 memo、路线图 | P2 |

## 第一批建议优先研究候选

1. OpenAI Plugins / `plugin-creator`
2. Anthropic 官方 / `skill-creator`
3. OpenAI Plugins / `frontend-app-builder`
4. OpenAI Plugins / `security-scan`
5. OpenAI Plugins / `gh-fix-ci`
6. Anthropic 官方 / `build-mcp-server`
7. Anthropic 官方 / `frontend-design`
8. Trail of Bits / `openai-security-threat-model`
9. Trail of Bits / `ghidra-headless`
10. Superpowers / `subagent-driven-development`
11. Superpowers / `systematic-debugging`
12. GitHub Spec Kit / `templates/commands`
13. Matt Pocock / `diagnosing-bugs`
14. Daymade / `doc-to-markdown`
15. OfficialSkills / Sentry `sentry-workflow`
16. OfficialSkills / Playwright skills
17. Strategy Skills / `decision-memo`
18. GuDaStudio / `collaborating-with-codex`
