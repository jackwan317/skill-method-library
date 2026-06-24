# Skills 按任务类型归类目录

> 本目录是长线索引。每次完成单个 Skill 深度研究后，把它追加到对应任务类型。

## 1. Skill / Plugin / Agent 开发

| Skill | 来源 | 用途 | 状态 |
|---|---|---|---|
| OpenAI `plugin-creator` | https://github.com/openai/plugins/blob/main/.agents/skills/plugin-creator/SKILL.md | 生成 Codex plugin 骨架与 marketplace 条目 | 已研究 |
| Anthropic `skill-creator` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/skill-creator/skills/skill-creator | 创建、评估、优化、打包 Skills | 已研究 |
| Anthropic `skill-development` | https://github.com/anthropics/claude-plugins-official/blob/main/plugins/plugin-dev/skills/skill-development/SKILL.md | 插件内 Skill 开发 | 待研究 |
| NeoLabHQ `create-skill` | https://github.com/NeoLabHQ/context-engineering-kit | 创建 Skill | 待研究 |
| Daymade `skill-reviewer` | https://github.com/daymade/claude-code-skills | Skill 审查 | 待研究 |

## 2. 前端 / UI / 设计到代码

| Skill | 来源 | 用途 | 状态 |
|---|---|---|---|
| OpenAI `frontend-app-builder` | https://github.com/openai/plugins/blob/main/plugins/build-web-apps/skills/frontend-app-builder/SKILL.md | Image Gen 设计先行，忠实实现前端应用 | 已研究 |
| Anthropic `frontend-design` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/frontend-design/skills/frontend-design | 前端视觉设计 | 待研究 |
| Figma official skills | https://officialskills.sh/ | Figma design-to-code / design review | 待筛选 |
| Awesome Claude Code Design Review Workflow | https://github.com/hesreallyhim/awesome-claude-code | Playwright MCP + subagent 设计评审 | 待研究 |

## 3. MCP / 外部工具集成

| Skill | 来源 | 用途 | 状态 |
|---|---|---|---|
| Anthropic `build-mcp-server` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/mcp-server-dev/skills/build-mcp-server | MCP Server 开发 | 待研究 |
| Microsoft `generate-mcp-app-ui` | https://github.com/microsoft/power-platform-skills | MCP app UI | 待研究 |
| Cloudflare Agents/Wrangler skills | https://officialskills.sh/ | Workers/Agents 部署 | 待筛选 |

## 4. 软件工程流程 / TDD / 调试

| Skill | 来源 | 用途 | 状态 |
|---|---|---|---|
| Superpowers `subagent-driven-development` | https://github.com/obra/superpowers | fresh subagent + spec review + quality review | 待研究 |
| Superpowers `systematic-debugging` | https://github.com/obra/superpowers | 4 阶段根因调试 | 待研究 |
| Superpowers `test-driven-development` | https://github.com/obra/superpowers | RED/GREEN/REFACTOR | 待研究 |
| Matt Pocock `diagnosing-bugs` | https://github.com/mattpocock/skills | bug 诊断 | 待研究 |
| GitHub Spec Kit commands | https://github.com/github/spec-kit/tree/main/templates/commands | spec/clarify/plan/tasks/implement | 待研究 |

## 5. 代码评审 / CI 修复 / GitHub 工作流

| Skill | 来源 | 用途 | 状态 |
|---|---|---|---|
| OpenAI `gh-fix-ci` | https://github.com/openai/plugins/blob/main/plugins/github/skills/gh-fix-ci/SKILL.md | 修复 GitHub Actions CI | 待研究 |
| Codex Plugin CC `review` | https://github.com/openai/codex-plugin-cc/tree/main/plugins/codex/commands | Codex review / adversarial review | 待研究 |
| Trail of Bits `differential-review` | https://officialskills.sh/trailofbits/skills/differential-review | 安全差异评审 | 待研究 |
| Sentry `sentry-code-review` | https://officialskills.sh/ | observability-aware review | 待筛选 |

## 6. 安全 / 逆向 / 审计

| Skill | 来源 | 用途 | 状态 |
|---|---|---|---|
| OpenAI `security-scan` | https://github.com/openai/plugins/blob/main/plugins/codex-security/skills/security-scan/SKILL.md | 多阶段 repo 安全扫描 | 待研究 |
| OpenAI / Trail of Bits `security-threat-model` | https://officialskills.sh/openai/skills/security-threat-model | 威胁建模 | 待研究 |
| Trail of Bits `ghidra-headless` | https://github.com/trailofbits/skills-curated | Ghidra headless 逆向分析 | 待研究 |
| Trail of Bits `static-analysis` | https://officialskills.sh/trailofbits/skills/static-analysis | CodeQL/Semgrep/SARIF | 待研究 |

## 7. 文档 / 知识处理 / 文件转换

| Skill | 来源 | 用途 | 状态 |
|---|---|---|---|
| Daymade `doc-to-markdown` | https://github.com/daymade/claude-code-skills/tree/main/daymade-docs/doc-to-markdown | DOCX/PDF/PPTX 转 Markdown | 待研究 |
| Antigravity `pdf-conversion-router` | https://github.com/sickn33/antigravity-awesome-skills | PDF 路由转换 | 待研究 |
| Anthropic document skills | https://github.com/anthropics/claude-plugins-official | PDF/DOCX/XLSX/PPTX | 待筛选 |

## 8. 生产问题 / Observability / 运维

| Skill | 来源 | 用途 | 状态 |
|---|---|---|---|
| Sentry `sentry-workflow` | https://officialskills.sh/getsentry/skills/sentry-workflow | Sentry issue 定位与修复 | 待研究 |
| Sentry `sentry-fix-issues` | https://officialskills.sh/getsentry/skills/sentry-fix-issues | 生产错误修复 | 待研究 |
| Cloudflare `wrangler` | https://officialskills.sh/ | Cloudflare 部署与调试 | 待筛选 |

## 9. 商业 / 产品 / 战略

| Skill | 来源 | 用途 | 状态 |
|---|---|---|---|
| Strategy `decision-memo` | https://github.com/aapersh/strategy-skills-for-claude | answer-first 决策备忘录 | 待研究 |
| Strategy `market-mapping` | https://github.com/aapersh/strategy-skills-for-claude | 市场地图 | 待研究 |
| Strategy `business-case-builder` | https://github.com/aapersh/strategy-skills-for-claude | 商业案例 | 待研究 |
| Strategy `transformation-roadmap` | https://github.com/aapersh/strategy-skills-for-claude | 转型路线图 | 待研究 |
