# Skills 按任务类型归类目录

> 本目录是长线索引。每次完成单个 Skill 深度研究后，必须追加到对应任务类型，并填写 **入选理由** 与 **原文路径**。  
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

| Skill | 来源 | 用途 | 入选理由 | 原文路径 | 状态 |
|---|---|---|---|---|---|
| OpenAI `plugin-creator` | https://github.com/openai/plugins/blob/main/.agents/skills/plugin-creator/SKILL.md | 生成 Codex plugin 骨架与 marketplace 条目 | OpenAI 官方；有可执行脚本 `create_basic_plugin.py`；覆盖 manifest、marketplace、个人/团队作用域、覆盖保护，是“脚手架生成器 Skill”的优秀样本。 | `originals/openai/plugins/.agents/skills/plugin-creator/` | 已研究 |
| Anthropic `skill-creator` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/skill-creator/skills/skill-creator | 创建、评估、优化、打包 Skills | Anthropic 官方；不只是写 Skill，而是包含 eval、grader、comparator、analyzer、description 优化、打包脚本，代表 Skill 工程化闭环。 | `originals/anthropic/claude-plugins-official/plugins/skill-creator/skills/skill-creator/` | 已研究 |
| Anthropic `skill-development` | https://github.com/anthropics/claude-plugins-official/blob/main/plugins/plugin-dev/skills/skill-development/SKILL.md | 插件内 Skill 开发 | Anthropic 官方；适合补充学习 Skill 文件结构、frontmatter、渐进披露和插件内 Skill 组织方式。 | 待归档 | 待研究 |
| NeoLabHQ `create-skill` | https://github.com/NeoLabHQ/context-engineering-kit | 创建 Skill | 社区 context-engineering 工具集，包含大量 `SKILL.md`；适合与 Anthropic 官方 `skill-creator` 对比其方法差异。 | 待归档 | 待研究 |
| Daymade `skill-reviewer` | https://github.com/daymade/claude-code-skills | Skill 审查 | 社区实用型 Skill 库；任务聚焦“审查 Skill 质量”，适合反向学习优秀 Skill 的评价标准。 | 待归档 | 待研究 |

## 2. 前端 / UI / 设计到代码

| Skill | 来源 | 用途 | 入选理由 | 原文路径 | 状态 |
|---|---|---|---|---|---|
| OpenAI `frontend-app-builder` | https://github.com/openai/plugins/blob/main/plugins/build-web-apps/skills/frontend-app-builder/SKILL.md | Image Gen 设计先行，忠实实现前端应用 | OpenAI 官方；流程从 Image Gen brief、概念图批准、设计系统提取、编码到浏览器视觉验证；Hard Rules 和 final response contract 很完整。 | `originals/openai/plugins/plugins/build-web-apps/skills/frontend-app-builder/` | 已研究 |
| Anthropic `frontend-design` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/frontend-design/skills/frontend-design | 前端视觉设计 | Anthropic 官方；适合与 OpenAI 前端 Skill 对比“设计指导型 Skill”和“设计到代码型 Skill”的差异。 | 待归档 | 待研究 |
| Figma official skills | https://officialskills.sh/ | Figma design-to-code / design review | OfficialSkills 收录的官方生态 Skill；设计工具链价值高，适合补足从设计文件到实现的流程。 | 待归档 | 待筛选 |
| Awesome Claude Code Design Review Workflow | https://github.com/hesreallyhim/awesome-claude-code | Playwright MCP + subagent 设计评审 | 高热 Claude Code 生态索引中的 workflow；结合 Playwright MCP 和 subagent，是 UI/UX review 自动化样本。 | 待归档 | 待研究 |

## 3. MCP / 外部工具集成

| Skill | 来源 | 用途 | 入选理由 | 原文路径 | 状态 |
|---|---|---|---|---|---|
| Anthropic `build-mcp-server` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/mcp-server-dev/skills/build-mcp-server | MCP Server 开发 | Anthropic 官方；包含 MCP 协议、tool design、认证、部署等 references，是学习“Skill 如何封装外部协议”的高价值样本。 | 待归档 | 待研究 |
| Microsoft `generate-mcp-app-ui` | https://github.com/microsoft/power-platform-skills | MCP app UI | Microsoft 官方 Power Platform Skill；适合学习企业平台内 MCP app UI 生成方法。 | 待归档 | 待研究 |
| Cloudflare Agents/Wrangler skills | https://officialskills.sh/ | Workers/Agents 部署 | 官方生态 Skill；Cloudflare Workers/Agents 是高频部署场景，适合学习工程化部署流程。 | 待归档 | 待筛选 |

## 4. 软件工程流程 / TDD / 调试

| Skill | 来源 | 用途 | 入选理由 | 原文路径 | 状态 |
|---|---|---|---|---|---|
| Superpowers `subagent-driven-development` | https://github.com/obra/superpowers | fresh subagent + spec review + quality review | 社区高热方法论项目；流程明确要求 fresh subagent、spec review、quality review，可直接迁移到我们的多 agent 工作流。 | 待归档 | 待研究 |
| Superpowers `systematic-debugging` | https://github.com/obra/superpowers | 4 阶段根因调试 | 社区高热；把 debug 从“猜修”变成 root-cause workflow，适合高频工程任务。 | 待归档 | 待研究 |
| Superpowers `test-driven-development` | https://github.com/obra/superpowers | RED/GREEN/REFACTOR | 社区高热；TDD 流程强约束，适合学习如何在 Skill 里约束 agent 不跳步骤。 | 待归档 | 待研究 |
| Matt Pocock `diagnosing-bugs` | https://github.com/mattpocock/skills | bug 诊断 | 知名工程教育作者维护；工程实践导向强，适合对比专业开发者写法。 | 待归档 | 待研究 |
| GitHub Spec Kit commands | https://github.com/github/spec-kit/tree/main/templates/commands | spec/clarify/plan/tasks/implement | GitHub 官方；spec-driven development 工作流完整，适合学习从需求澄清到任务拆解的命令式流程。 | 待归档 | 待研究 |

## 5. 代码评审 / CI 修复 / GitHub 工作流

| Skill | 来源 | 用途 | 入选理由 | 原文路径 | 状态 |
|---|---|---|---|---|---|
| OpenAI `gh-fix-ci` | https://github.com/openai/plugins/blob/main/plugins/github/skills/gh-fix-ci/SKILL.md | 修复 GitHub Actions CI | OpenAI 官方 GitHub Skill；任务高频且验证明确，适合学习 CI 诊断闭环。 | 待归档 | 待研究 |
| Codex Plugin CC `review` | https://github.com/openai/codex-plugin-cc/tree/main/plugins/codex/commands | Codex review / adversarial review | OpenAI 官方 Codex-Claude 协作插件；适合学习如何把 review 委派给另一模型/agent。 | 待归档 | 待研究 |
| Trail of Bits `differential-review` | https://officialskills.sh/trailofbits/skills/differential-review | 安全差异评审 | Trail of Bits 权威安全团队；专业安全 review 方法，质量信号强。 | 待归档 | 待研究 |
| Sentry `sentry-code-review` | https://officialskills.sh/ | observability-aware review | Sentry 官方生态；把生产错误上下文带入 review，适合学习“可观测性驱动代码审查”。 | 待归档 | 待筛选 |

## 6. 安全 / 逆向 / 审计

| Skill | 来源 | 用途 | 入选理由 | 原文路径 | 状态 |
|---|---|---|---|---|---|
| OpenAI `security-scan` | https://github.com/openai/plugins/blob/main/plugins/codex-security/skills/security-scan/SKILL.md | 多阶段 repo 安全扫描 | OpenAI 官方安全 Skill；分阶段 threat model、finding discovery、validation、attack path analysis，流程完整。 | 待归档 | 待研究 |
| OpenAI / Trail of Bits `security-threat-model` | https://officialskills.sh/openai/skills/security-threat-model | 威胁建模 | 官方/权威安全来源；适合学习如何从资产、边界、攻击者能力推导风险。 | 待归档 | 待研究 |
| Trail of Bits `ghidra-headless` | https://github.com/trailofbits/skills-curated | Ghidra headless 逆向分析 | Trail of Bits 维护；含专业逆向工具链和脚本，是“Skill 封装专业工具”的样本。 | 待归档 | 待研究 |
| Trail of Bits `static-analysis` | https://officialskills.sh/trailofbits/skills/static-analysis | CodeQL/Semgrep/SARIF | 权威安全团队；适合学习静态分析工具、规则、结果验证如何被编排进 Skill。 | 待归档 | 待研究 |

## 7. 文档 / 知识处理 / 文件转换

| Skill | 来源 | 用途 | 入选理由 | 原文路径 | 状态 |
|---|---|---|---|---|---|
| Daymade `doc-to-markdown` | https://github.com/daymade/claude-code-skills/tree/main/daymade-docs/doc-to-markdown | DOCX/PDF/PPTX 转 Markdown | 社区实用型 Skill；文件转换任务可验证，适合学习工具链、后处理和格式保真。 | 待归档 | 待研究 |
| Antigravity `pdf-conversion-router` | https://github.com/sickn33/antigravity-awesome-skills | PDF 路由转换 | 大型 Skill 集合中的实用路由器；先分类 PDF 类型再选 OCR/layout/table 路径，流程有迁移价值。 | 待归档 | 待研究 |
| Anthropic document skills | https://github.com/anthropics/claude-plugins-official | PDF/DOCX/XLSX/PPTX | Anthropic 官方文档处理类 Skill；适合学习官方如何组织文件处理任务。 | 待归档 | 待筛选 |

## 8. 生产问题 / Observability / 运维

| Skill | 来源 | 用途 | 入选理由 | 原文路径 | 状态 |
|---|---|---|---|---|---|
| Sentry `sentry-workflow` | https://officialskills.sh/getsentry/skills/sentry-workflow | Sentry issue 定位与修复 | Sentry 官方；使用 issue、stack trace、breadcrumb、trace context 定位生产问题，高业务价值。 | 待归档 | 待研究 |
| Sentry `sentry-fix-issues` | https://officialskills.sh/getsentry/skills/sentry-fix-issues | 生产错误修复 | Sentry 官方；可学习从 observability 到代码修复的闭环。 | 待归档 | 待研究 |
| Cloudflare `wrangler` | https://officialskills.sh/ | Cloudflare 部署与调试 | 官方生态 Skill；部署/边缘计算任务高频，适合学习运维型 Skill 的验证方式。 | 待归档 | 待筛选 |

## 9. 商业 / 产品 / 战略

| Skill | 来源 | 用途 | 入选理由 | 原文路径 | 状态 |
|---|---|---|---|---|---|
| Strategy `decision-memo` | https://github.com/aapersh/strategy-skills-for-claude | answer-first 决策备忘录 | 虽非官方大厂，但结构清晰、商业价值高；适合学习咨询式输出框架。 | 待归档 | 待研究 |
| Strategy `market-mapping` | https://github.com/aapersh/strategy-skills-for-claude | 市场地图 | 商业分析任务与大哥目标强相关；适合转化为机会筛选 SOP。 | 待归档 | 待研究 |
| Strategy `business-case-builder` | https://github.com/aapersh/strategy-skills-for-claude | 商业案例 | 可迁移到产品/副业项目评估；结构化 ROI、风险、假设验证。 | 待归档 | 待研究 |
| Strategy `transformation-roadmap` | https://github.com/aapersh/strategy-skills-for-claude | 转型路线图 | 把战略拆成里程碑、owner、依赖、治理，适合学习复杂计划拆解。 | 待归档 | 待研究 |
