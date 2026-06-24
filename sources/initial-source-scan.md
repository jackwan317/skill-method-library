# 第一批优秀 Skills 来源扫描

本批次由 3 个独立 sub agent 分别扫描：OpenAI/Codex、Anthropic/Claude、开源共享平台。

> 新增规则：每个来源/候选都必须写“为什么认为它优秀”。只说“看起来有用”不够，必须说明质量信号：官方、星标/fork、趋势、社区讨论、维护活跃、结构完整、工具链成熟、可迁移价值等。

## P0/P1 来源

| 来源 | URL | 证据 | 适合任务类型 | 为什么认为优秀 / 入选理由 | 优先级 |
|---|---|---|---|---|---|
| OpenAI Codex | https://github.com/openai/codex | OpenAI 官方；高星；包含 repo-level `AGENTS.md` | Agent 指令、代码工程规范 | 官方一手实践；`AGENTS.md` 体现大型工程中如何约束 agent 做测试、lint、模块边界和代码风格。 | P0 |
| OpenAI Plugins | https://github.com/openai/plugins | OpenAI 官方；包含大量 `.agents/skills` 与插件 Skill | 插件、前端、安全、CI、GitHub、iOS、企业工具 | 官方 Skill/Plugin 大集合；很多 Skill 同时有 `SKILL.md`、scripts、references、plugin manifest，适合学习完整任务流程。 | P0 |
| OpenAI Codex Plugin for Claude Code | https://github.com/openai/codex-plugin-cc | OpenAI 官方；Codex 与 Claude Code 协作 | 代码审查、对抗 review、委派 | 官方跨 agent 协作样本；能学习 Claude 如何把 review/debug/rescue 委派给 Codex。 | P0 |
| Anthropic Claude Plugins Official | https://github.com/anthropics/claude-plugins-official | Anthropic 官方；包含 skill-creator、MCP、前端、报告等 | Skill 创建、MCP、Claude 工作流 | Claude Skills 官方范式；尤其 `skill-creator` 含 eval、grader、打包和 description 优化，是元 Skill 标杆。 | P0 |
| OfficialSkills | https://officialskills.sh/ | 聚合 Anthropic/OpenAI/Cloudflare/Sentry/Figma/Trail of Bits 等官方技能 | 官方 skills 分发入口 | 作为官方 Skill 索引入口，便于优先找到可信 Skill，而不是从随机仓库里盲选。 | P0 |
| obra/superpowers | https://github.com/obra/superpowers | 高热社区项目；支持 Claude/Codex/Cursor/Gemini | 软件工程方法论、TDD、调试、subagent | 社区热度高且方法论强；多个 Skill 已被本地 Hermes 吸收，说明可迁移性强。 | P1 |
| Trail of Bits skills-curated | https://github.com/trailofbits/skills-curated | 安全公司维护；安全与逆向质量高 | 安全审计、fuzzing、逆向、PR review | Trail of Bits 是安全领域权威；其 Skill 经常封装专业安全工具链，质量信号强。 | P1 |
| VoltAgent awesome-agent-skills | https://github.com/VoltAgent/awesome-agent-skills | 大型 curated list，收录官方 Skills | 来源索引、跨生态检索 | 收录范围大，适合作为发现官方/高质量 Skill 的二级入口。 | P1 |
| awesome-claude-code | https://github.com/hesreallyhim/awesome-claude-code | Claude Code 生态索引 | workflows、commands、hooks、MCP | 社区热度高，覆盖 workflows/commands/hooks/MCP，可发现非官方但被广泛讨论的实践。 | P1 |
| mattpocock/skills | https://github.com/mattpocock/skills | 工程实践型 Skills | TDD、bug 诊断、架构、PRD/Issues | 由知名工程教育作者维护；偏真实工程实践，适合学习专业开发者如何写 AI 工作流。 | P1 |
| github/spec-kit | https://github.com/github/spec-kit | GitHub 官方；spec-driven development | specify/clarify/plan/tasks/implement | GitHub 官方 spec-driven 工作流；从需求到实施链路完整，适合学习任务拆解。 | P1 |
| NeoLabHQ/context-engineering-kit | https://github.com/NeoLabHQ/context-engineering-kit | 多 Skill、context engineering | 多 Agent、TDD、评审、Git、MCP | 主题聚焦 context engineering，Skill 数量多，适合作为方法论和多 agent 工作流参考。 | P1 |
| daymade/claude-code-skills | https://github.com/daymade/claude-code-skills | 社区实用型 Skill 库 | 文档、研究、GitHub、音视频、PPT/PDF | 覆盖实际办公/研究/文件处理任务，实用性强，适合学习工具链型 Skill。 | P1 |
| Microsoft power-platform-skills | https://github.com/microsoft/power-platform-skills | Microsoft 官方 | 企业平台、Power Apps、Dataverse | 官方企业平台 Skill；适合学习垂直平台任务如何写成 Skill。 | P2 |
| antigravity-awesome-skills | https://github.com/sickn33/antigravity-awesome-skills | 大型 Skill 集合，需二次筛选 | DevOps、QA、安全、数据、增长 | 数量大、覆盖广；适合发现候选，但质量参差，必须二次筛选有明确 workflow 的 Skill。 | P2 |
| awesome-cursorrules | https://github.com/PatrickJS/awesome-cursorrules | 高星 Cursor rules | 框架规范、反幻觉规则 | 虽不是标准 Skill，但能学习“规则约束型提示”如何减少 hallucination 和框架错误。 | P2 |
| GuDaStudio/skills | https://github.com/GuDaStudio/skills | 中文生态，多模型协作 | Claude/Codex/Gemini 协作 | 中文生态友好；多模型协作和委派模式与我们的 Hermes 长线任务高度相关。 | P2 |
| strategy-skills-for-claude | https://github.com/aapersh/strategy-skills-for-claude | 结构清晰的商业战略 Skills | 市场、战略、决策 memo、路线图 | 与大哥商业目标相关；虽然星标不一定最高，但结构清晰、商业任务价值高。 | P2 |

## 第一批建议优先研究候选

| 候选 Skill | 原始链接 | 为什么优先研究 | 状态 |
|---|---|---|---|
| OpenAI Plugins / `plugin-creator` | https://github.com/openai/plugins/blob/main/.agents/skills/plugin-creator/SKILL.md | OpenAI 官方；有脚本和 marketplace 规范；能学习脚手架型 Skill 如何做边界判断、路径选择和验证。 | 已研究+已归档 |
| Anthropic 官方 / `skill-creator` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/skill-creator/skills/skill-creator | Anthropic 官方元 Skill；包含 eval、grader、comparator、description 优化、打包，是 Skill 工程化闭环样本。 | 已研究+已归档 |
| OpenAI Plugins / `frontend-app-builder` | https://github.com/openai/plugins/blob/main/plugins/build-web-apps/skills/frontend-app-builder/SKILL.md | OpenAI 官方；设计先行、实现、浏览器视觉验证、final response contract 完整，适合学习高质量 UI 交付流程。 | 已研究+已归档 |
| OpenAI Plugins / `security-scan` | https://github.com/openai/plugins/blob/main/plugins/codex-security/skills/security-scan/SKILL.md | OpenAI 官方安全流程；多阶段安全审计，适合学习复杂审计任务拆解。 | 待研究 |
| OpenAI Plugins / `gh-fix-ci` | https://github.com/openai/plugins/blob/main/plugins/github/skills/gh-fix-ci/SKILL.md | OpenAI 官方；CI 修复任务验证明确，适合学习诊断-修复-验证闭环。 | 待研究 |
| Anthropic 官方 / `build-mcp-server` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/mcp-server-dev/skills/build-mcp-server | Anthropic 官方；MCP 是高价值外部工具集成方向，Skill 中 references 完整。 | 待研究 |
| Anthropic 官方 / `frontend-design` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/frontend-design/skills/frontend-design | Anthropic 官方；适合与 OpenAI `frontend-app-builder` 对比设计流程。 | 待研究 |
| Trail of Bits / `openai-security-threat-model` | https://github.com/trailofbits/skills-curated | Trail of Bits 权威安全组织；学习安全建模结构。 | 待研究 |
| Trail of Bits / `ghidra-headless` | https://github.com/trailofbits/skills-curated | 封装专业逆向工具链；适合学习“工具型 Skill”如何设计。 | 待研究 |
| Superpowers / `subagent-driven-development` | https://github.com/obra/superpowers | 高热社区方法论；与本任务“一个 Skill 一个 sub agent”直接相关。 | 待研究 |
| Superpowers / `systematic-debugging` | https://github.com/obra/superpowers | 高价值工程流程；约束 agent 不猜修。 | 待研究 |
| GitHub Spec Kit / `templates/commands` | https://github.com/github/spec-kit/tree/main/templates/commands | GitHub 官方 spec-driven workflow；从需求到执行链路完整。 | 待研究 |
| Matt Pocock / `diagnosing-bugs` | https://github.com/mattpocock/skills | 知名工程作者；真实工程 bug 诊断方法。 | 待研究 |
| Daymade / `doc-to-markdown` | https://github.com/daymade/claude-code-skills/tree/main/daymade-docs/doc-to-markdown | 文件转换任务可验证；适合学习工具链和格式保真。 | 待研究 |
| OfficialSkills / Sentry `sentry-workflow` | https://officialskills.sh/getsentry/skills/sentry-workflow | Sentry 官方；生产问题定位价值高。 | 待研究 |
| OfficialSkills / Playwright skills | https://officialskills.sh/ | 浏览器自动化高频；适合学习 UI 测试/调试 Skill。 | 待筛选 |
| Strategy Skills / `decision-memo` | https://github.com/aapersh/strategy-skills-for-claude | 商业决策输出结构清晰，和大哥目标相关。 | 待研究 |
| GuDaStudio / `collaborating-with-codex` | https://github.com/GuDaStudio/collaborating-with-codex | 中文生态多模型协作；适合学习 Claude/Codex 委派。 | 待研究 |
