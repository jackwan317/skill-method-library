# Skills 按任务类型归类目录

> 本目录是候选池 + 进度表。每次发现候选或完成单个 Skill 深度研究后，必须更新对应行的 **入选理由**、**报告路径** 与 **备注状态**。  
> 大哥的核心目标不是“收集很多 Skill”，而是学习优秀 Skill 如何做事；所以每个候选都必须解释：为什么它值得研究。

## 判断一个 Skill 是否优秀的依据

优先级从高到低：

1. **官方/权威来源**：OpenAI、Anthropic、Microsoft、GitHub、Trail of Bits、Sentry、Cloudflare 等官方或专业组织维护。
2. **社区热度**：GitHub stars/forks 高、近期增长快、进入 trending、被 awesome list 多次收录。
3. **结构完整**：不仅有提示词，还有 `scripts/`、`references/`、`agents/`、`templates/`、验证步骤、失败恢复。
4. **流程清晰**：能看出它如何拆任务、如何调用工具、如何做验证，而不是泛泛建议。
5. **可迁移性强**：它的方法可以被我们改写成 Hermes Skill、SOP 或业务自动化流程。
6. **任务价值高**：覆盖高频/高价值任务，例如插件开发、MCP、前端、调试、安全、CI、文档、商业分析。

---

## 1. Skill / Plugin / Agent 开发

| Skill | 来源 | 用途 | 入选理由 | 报告路径 | 备注状态 |
|---|---|---|---|---|---|
| OpenAI `plugin-creator` | https://github.com/openai/plugins/blob/main/.agents/skills/plugin-creator/SKILL.md | 生成 Codex plugin 骨架与 marketplace 条目 | OpenAI 官方；有可执行脚本 `create_basic_plugin.py`；覆盖 manifest、marketplace、个人/团队作用域、覆盖保护，是“脚手架生成器 Skill”的优秀样本。 | `originals/openai/plugins/.agents/skills/plugin-creator/` | 已研究 |
| Anthropic `skill-creator` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/skill-creator/skills/skill-creator | 创建、评估、优化、打包 Skills | Anthropic 官方；不只是写 Skill，而是包含 eval、grader、comparator、analyzer、description 优化、打包脚本，代表 Skill 工程化闭环。 | `originals/anthropic/claude-plugins-official/plugins/skill-creator/skills/skill-creator/` | 已研究 |
| Anthropic `skill-development` | https://github.com/anthropics/claude-plugins-official/blob/main/plugins/plugin-dev/skills/skill-development/SKILL.md | 插件内 Skill 开发 | Anthropic 官方；适合补充学习 Skill 文件结构、frontmatter、渐进披露和插件内 Skill 组织方式。 | 待生成 | 待研究 |
| NeoLabHQ `create-skill` | https://github.com/NeoLabHQ/context-engineering-kit | 创建 Skill | 社区 context-engineering 工具集，包含大量 `SKILL.md`；适合与 Anthropic 官方 `skill-creator` 对比其方法差异。 | 待生成 | 待研究 |
| Daymade `skill-reviewer` | https://github.com/daymade/claude-code-skills | Skill 审查 | 社区实用型 Skill 库；任务聚焦“审查 Skill 质量”，适合反向学习优秀 Skill 的评价标准。 | 待生成 | 待研究 |

## 2. 前端 / UI / 设计到代码

| Skill | 来源 | 用途 | 入选理由 | 报告路径 | 备注状态 |
|---|---|---|---|---|---|
| OpenAI `frontend-app-builder` | https://github.com/openai/plugins/blob/main/plugins/build-web-apps/skills/frontend-app-builder/SKILL.md | Image Gen 设计先行，忠实实现前端应用 | OpenAI 官方；流程从 Image Gen brief、概念图批准、设计系统提取、编码到浏览器视觉验证；Hard Rules 和 final response contract 很完整。 | `originals/openai/plugins/plugins/build-web-apps/skills/frontend-app-builder/` | 已研究 |
| Anthropic `frontend-design` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/frontend-design/skills/frontend-design | 前端视觉设计 | Anthropic 官方；适合与 OpenAI 前端 Skill 对比“设计指导型 Skill”和“设计到代码型 Skill”的差异。 | 待生成 | 待研究 |
| Figma official skills | https://officialskills.sh/ | Figma design-to-code / design review | OfficialSkills 收录的官方生态 Skill；设计工具链价值高，适合补足从设计文件到实现的流程。 | 待生成 | 待筛选 |
| Awesome Claude Code Design Review Workflow | https://github.com/hesreallyhim/awesome-claude-code | Playwright MCP + subagent 设计评审 | 高热 Claude Code 生态索引中的 workflow；结合 Playwright MCP 和 subagent，是 UI/UX review 自动化样本。 | 待生成 | 待研究 |

## 3. MCP / 外部工具集成

| Skill | 来源 | 用途 | 入选理由 | 报告路径 | 备注状态 |
|---|---|---|---|---|---|
| Anthropic `build-mcp-server` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/mcp-server-dev/skills/build-mcp-server | MCP Server 开发 | Anthropic 官方；包含 MCP 协议、tool design、认证、部署等 references，是学习“Skill 如何封装外部协议”的高价值样本。 | 待生成 | 待研究 |
| Microsoft `generate-mcp-app-ui` | https://github.com/microsoft/power-platform-skills | MCP app UI | Microsoft 官方 Power Platform Skill；适合学习企业平台内 MCP app UI 生成方法。 | 待生成 | 待研究 |
| Cloudflare Agents/Wrangler skills | https://officialskills.sh/ | Workers/Agents 部署 | 官方生态 Skill；Cloudflare Workers/Agents 是高频部署场景，适合学习工程化部署流程。 | 待生成 | 待筛选 |

## 4. 软件工程流程 / TDD / 调试

| Skill | 来源 | 用途 | 入选理由 | 报告路径 | 备注状态 |
|---|---|---|---|---|---|
| Superpowers `subagent-driven-development` | https://github.com/obra/superpowers | fresh subagent + spec review + quality review | 社区高热方法论项目；流程明确要求 fresh subagent、spec review、quality review，可直接迁移到我们的多 agent 工作流。 | 待生成 | 待研究 |
| Superpowers `systematic-debugging` | https://github.com/obra/superpowers | 4 阶段根因调试 | 社区高热；把 debug 从“猜修”变成 root-cause workflow，适合高频工程任务。 | 待生成 | 待研究 |
| Superpowers `test-driven-development` | https://github.com/obra/superpowers | RED/GREEN/REFACTOR | 社区高热；TDD 流程强约束，适合学习如何在 Skill 里约束 agent 不跳步骤。 | 待生成 | 待研究 |
| Matt Pocock `diagnosing-bugs` | https://github.com/mattpocock/skills | bug 诊断 | 知名工程教育作者维护；工程实践导向强，适合对比专业开发者写法。 | 待生成 | 待研究 |
| GitHub Spec Kit commands | https://github.com/github/spec-kit/tree/main/templates/commands | spec/clarify/plan/tasks/implement | GitHub 官方；spec-driven development 工作流完整，适合学习从需求澄清到任务拆解的命令式流程。 | 待生成 | 待研究 |

## 5. 代码评审 / CI 修复 / GitHub 工作流

| Skill | 来源 | 用途 | 入选理由 | 报告路径 | 备注状态 |
|---|---|---|---|---|---|
| OpenAI `gh-fix-ci` | https://github.com/openai/plugins/blob/main/plugins/github/skills/gh-fix-ci/SKILL.md | 修复 GitHub Actions CI | OpenAI 官方 GitHub Skill；任务高频且验证明确，适合学习 CI 诊断闭环。 | 待生成 | 待研究 |
| Codex Plugin CC `review` | https://github.com/openai/codex-plugin-cc/tree/main/plugins/codex/commands | Codex review / adversarial review | OpenAI 官方 Codex-Claude 协作插件；适合学习如何把 review 委派给另一模型/agent。 | 待生成 | 待研究 |
| Trail of Bits `differential-review` | https://officialskills.sh/trailofbits/skills/differential-review | 安全差异评审 | Trail of Bits 权威安全团队；专业安全 review 方法，质量信号强。 | 待生成 | 待研究 |
| Sentry `sentry-code-review` | https://officialskills.sh/ | observability-aware review | Sentry 官方生态；把生产错误上下文带入 review，适合学习“可观测性驱动代码审查”。 | 待生成 | 待筛选 |

## 6. 安全 / 逆向 / 审计

| Skill | 来源 | 用途 | 入选理由 | 报告路径 | 备注状态 |
|---|---|---|---|---|---|
| OpenAI `security-scan` | https://github.com/openai/plugins/blob/main/plugins/codex-security/skills/security-scan/SKILL.md | 多阶段 repo 安全扫描 | OpenAI 官方安全 Skill；分阶段 threat model、finding discovery、validation、attack path analysis，流程完整。 | 待生成 | 待研究 |
| OpenAI / Trail of Bits `security-threat-model` | https://officialskills.sh/openai/skills/security-threat-model | 威胁建模 | 官方/权威安全来源；适合学习如何从资产、边界、攻击者能力推导风险。 | 待生成 | 待研究 |
| Trail of Bits `ghidra-headless` | https://github.com/trailofbits/skills-curated | Ghidra headless 逆向分析 | Trail of Bits 维护；含专业逆向工具链和脚本，是“Skill 封装专业工具”的样本。 | 待生成 | 待研究 |
| Trail of Bits `static-analysis` | https://officialskills.sh/trailofbits/skills/static-analysis | CodeQL/Semgrep/SARIF | 权威安全团队；适合学习静态分析工具、规则、结果验证如何被编排进 Skill。 | 待生成 | 待研究 |

## 7. 文档 / 知识处理 / 文件转换

| Skill | 来源 | 用途 | 入选理由 | 报告路径 | 备注状态 |
|---|---|---|---|---|---|
| Daymade `doc-to-markdown` | https://github.com/daymade/claude-code-skills/tree/main/daymade-docs/doc-to-markdown | DOCX/PDF/PPTX 转 Markdown | 社区实用型 Skill；文件转换任务可验证，适合学习工具链、后处理和格式保真。 | 待生成 | 待研究 |
| Antigravity `pdf-conversion-router` | https://github.com/sickn33/antigravity-awesome-skills | PDF 路由转换 | 大型 Skill 集合中的实用路由器；先分类 PDF 类型再选 OCR/layout/table 路径，流程有迁移价值。 | 待生成 | 待研究 |
| Anthropic document skills | https://github.com/anthropics/claude-plugins-official | PDF/DOCX/XLSX/PPTX | Anthropic 官方文档处理类 Skill；适合学习官方如何组织文件处理任务。 | 待生成 | 待筛选 |

## 8. 生产问题 / Observability / 运维

| Skill | 来源 | 用途 | 入选理由 | 报告路径 | 备注状态 |
|---|---|---|---|---|---|
| Sentry `sentry-workflow` | https://officialskills.sh/getsentry/skills/sentry-workflow | Sentry issue 定位与修复 | Sentry 官方；使用 issue、stack trace、breadcrumb、trace context 定位生产问题，高业务价值。 | 待生成 | 待研究 |
| Sentry `sentry-fix-issues` | https://officialskills.sh/getsentry/skills/sentry-fix-issues | 生产错误修复 | Sentry 官方；可学习从 observability 到代码修复的闭环。 | 待生成 | 待研究 |
| Cloudflare `wrangler` | https://officialskills.sh/ | Cloudflare 部署与调试 | 官方生态 Skill；部署/边缘计算任务高频，适合学习运维型 Skill 的验证方式。 | 待生成 | 待筛选 |

## 9. 商业 / 产品 / 战略

| Skill | 来源 | 用途 | 入选理由 | 报告路径 | 备注状态 |
|---|---|---|---|---|---|
| Strategy `decision-memo` | https://github.com/aapersh/strategy-skills-for-claude | answer-first 决策备忘录 | 虽非官方大厂，但结构清晰、商业价值高；适合学习咨询式输出框架。 | 待生成 | 待研究 |
| Strategy `market-mapping` | https://github.com/aapersh/strategy-skills-for-claude | 市场地图 | 商业分析任务与大哥目标强相关；适合转化为机会筛选 SOP。 | 待生成 | 待研究 |
| Strategy `business-case-builder` | https://github.com/aapersh/strategy-skills-for-claude | 商业案例 | 可迁移到产品/副业项目评估；结构化 ROI、风险、假设验证。 | 待生成 | 待研究 |
| Strategy `transformation-roadmap` | https://github.com/aapersh/strategy-skills-for-claude | 转型路线图 | 把战略拆成里程碑、owner、依赖、治理，适合学习复杂计划拆解。 | 待生成 | 待研究 |


---

## 10. 第二批新增优质候选（待生成/待深度研究）

> 这些候选来自第二轮跨平台扫描。完整表格见 `sources/expanded-candidates-2026-06-24.md`。这里先把最值得后续研究的候选登记到总目录，下一步逐个用 sub agent 深挖并输出报告。新流程不再下载原文。

| Skill / Workflow | 来源 | 用途 | 入选理由 | 报告路径 | 备注状态 |
|---|---|---|---|---|---|
| OpenAI Agents SDK workflow | https://github.com/openai/openai-agents-python | Agent 编排 / handoff / tracing | OpenAI 官方；约 27k stars/4.2k forks；适合学习生产级 agent 的工具、handoff、tracing、guardrails。 | 待生成 | 新增候选 |
| Sentry Error Monitoring workflow | https://docs.sentry.io/ / https://github.com/getsentry/sentry | 错误监控 / issue triage / 性能追踪 | Sentry 官方；主仓约 44k stars/4.7k forks；适合学习生产错误上下文到代码修复的闭环。 | 待生成 | 新增候选 |
| Cloudflare Agents Skill | https://github.com/cloudflare/agents | Edge-native AI Agents | Cloudflare 官方；约 5.1k stars/604 forks；适合学习部署在边缘的 agent 应用、状态和实时交互。 | 待生成 | 新增候选 |
| Cloudflare MCP Server Skill | https://github.com/cloudflare/mcp-server-cloudflare | MCP / Cloudflare 资源管理 | Cloudflare 官方；约 3.9k stars/427 forks；适合学习如何把云平台能力接入 Agent 工具系统。 | 待生成 | 新增候选 |
| Anthropic `hook-development` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/plugin-dev/skills/hook-development | Claude Code Hooks / 事件自动化 | Anthropic 官方；约 30k stars/3.3k forks；覆盖 PreToolUse/PostToolUse/Stop/SubagentStop，含 validate/lint/test 脚本，适合学习事件驱动 agent 安全守门和自动化。 | `skills/agent-automation/anthropic-hook-development.md` | 已有报告 |
| Anthropic `command-development` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/plugin-dev/skills/command-development | Claude Code Slash Commands / Workflow 产品化 | Anthropic 官方；约 30k stars/3.3k forks；覆盖 frontmatter、参数、文件引用、Bash 注入、AskUserQuestion、测试与分发，适合学习把 agent workflow 产品化为命令。 | `skills/agent-automation/anthropic-command-development.md` | 已有报告 |
| Anthropic `claude-automation-recommender` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/claude-code-setup/skills/claude-automation-recommender | 项目自动化诊断 / Agent 工作流设计 | Anthropic 官方；约 30k stars/3.3k forks；只读扫描项目事实，再推荐 MCP/Skills/Hooks/Subagents/Plugins，是元自动化顾问样本。 | `skills/agent-automation/anthropic-claude-automation-recommender.md` | 已有报告 |
| Anthropic `knowledge-synthesis` | https://github.com/anthropics/knowledge-work-plugins/tree/main/enterprise-search/skills/knowledge-synthesis | 企业搜索 / 多源知识综合 | Anthropic 官方；knowledge-work-plugins 约 21k stars/2.5k forks；以 `SKILL.md` 约束多源结果去重、主题聚类、权威/新鲜度/一致性评估、来源归因和按结果规模格式化，是 RAG/企业搜索后处理的高价值样本。 | `skills/knowledge-processing/anthropic-knowledge-synthesis.md` | 已有报告 |
| BMAD-METHOD | https://github.com/bmad-code-org/BMAD-METHOD | Agile AI-driven development | 社区高热；约 49k stars/5.7k forks；覆盖业务/产品规划到开发执行，适合提炼产品-需求-开发全链路 SOP。 | 待生成 | 新增候选 |
| wshobson/agents | https://github.com/wshobson/agents | Multi-agent marketplace / 跨平台 agents | 社区高热；约 37k stars/4k forks；支持 Claude Code、Codex CLI、Cursor、OpenCode、Copilot、Gemini CLI。 | 待生成 | 新增候选 |
| claude-task-master | https://github.com/eyaltoledano/claude-task-master | AI task management / 任务拆解 | 社区高热；约 27k stars/2.6k forks；聚焦把需求拆成可执行任务，和 Skill library 目标高度契合。 | 待生成 | 新增候选 |
| SuperClaude Framework | https://github.com/SuperClaude-Org/SuperClaude_Framework | Claude Code commands / persona / 方法论 | 社区高热；约 23k stars/1.9k forks；结合 commands、cognitive personas、development methodologies。 | 待生成 | 新增候选 |
| Context7 docs-for-LLMs workflow | https://github.com/upstash/context7 | 最新文档注入 / AI code editor docs | 社区高热；约 57k stars/2.7k forks；解决 LLM 使用过期文档的核心痛点。 | 待生成 | 新增候选 |
| awesome-mcp-servers | https://github.com/punkpeye/awesome-mcp-servers | MCP / DevOps / 工具集成来源索引 | 超高星；约 89k stars/12k forks；适合作为后续 DevOps、Docs、GitHub、数据库、云服务类 Skill 上游候选池。 | 待生成 | 新增候选 |
| Matt Pocock `diagnosing-bugs` | https://github.com/mattpocock/skills/tree/main/skills/engineering/diagnosing-bugs | Debug / tight feedback loop | 高星工程 Skill 库；约 143k stars/12k forks；强调先构建测试/curl/Playwright/trace/fuzz/bisect 等反馈环。 | 待生成 | 新增候选 |
| Addy Osmani `browser-testing-with-devtools` | https://github.com/addyosmani/agent-skills/tree/main/skills/browser-testing-with-devtools | 浏览器真实运行验证 / Chrome DevTools MCP | 社区高星；约 66k stars/7k forks；用 DOM、console、network、performance、visual output 验证前端。 | 待生成 | 新增候选 |
| Anthropic `launch-your-agent` | https://github.com/anthropics/launch-your-agent/tree/main/.claude/skills/launch-your-agent | 从 idea 到 Claude Managed Agent 上线 | Anthropic 官方；从 interview、scope v0、API/mock connectors、grading、iteration 到 schedule，链路端到端。 | 待生成 | 新增候选 |
| Anthropic `launch-review` | https://github.com/anthropics/claude-for-legal/tree/main/product-legal/skills/launch-review | 产品上线法律审查 / 风险 memo | Anthropic 官方；claude-for-legal 约 8.4k stars/1.5k forks；适合学习高风险专业领域 guardrails 和输出路由。 | 待生成 | 新增候选 |
