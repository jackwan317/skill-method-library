# 第二批优质 Skills / Skill-like Workflows 候选池

> 本轮目标：从官方平台、高星 GitHub 仓库、社区高热项目里增加 10 个以上优质候选。  
> 筛选标准：官方/权威、高 stars/forks、社区热度、结构完整、任务流程可学习、可迁移到 Hermes Skill/SOP。

## 本轮新增结论

本轮新增 **43 个** 候选，其中建议优先研究 **P0 24 个**。  
注意：GitHub stars/forks 是本次调研时页面/API 显示值，后续会变化；部分官方文档型 Skill 用“官方/高频使用”作为质量证据，而不是 stars。

---

## A. 官方 / OfficialSkills / 平台生态候选

| Skill / Workflow | 原始链接 | 上游 | 热度/证据 | 任务类型 | 为什么优秀 | 优先级 |
|---|---|---|---|---|---|---|
| OpenAI API / SDK Skill | https://platform.openai.com/docs / https://github.com/openai/openai-cookbook | OpenAI 官方 | Cookbook 约 74k stars / 12k forks；openai-python 约 31k stars / 4.8k forks | LLM API、Responses API、tool calling、结构化输出 | 高频刚需；可沉淀模型选择、流式输出、JSON schema、tool calling、错误处理等最佳实践。 | P0 |
| OpenAI Agents SDK Skill | https://github.com/openai/openai-agents-python | OpenAI 官方 | 约 27k stars / 4.2k forks | Agent 编排、tools、handoff、tracing、guardrails | 官方 Agent 框架；适合学习生产级 agent workflow、handoff、tracing 和 guardrails。 | P0 |
| OpenAI Node SDK Skill | https://github.com/openai/openai-node | OpenAI 官方 | 约 11k stars / 1.5k forks | JS/TS OpenAI 集成 | JS/TS AI 应用高频；可覆盖 streaming、Realtime、tool calls、文件上传等流程。 | P1 |
| Sentry Error Monitoring Skill | https://docs.sentry.io/ / https://github.com/getsentry/sentry | Sentry 官方 | getsentry/sentry 约 44k stars / 4.7k forks | 错误监控、issue triage、性能追踪 | 生产问题定位高价值；适合学习如何让 Agent 用 stacktrace、breadcrumbs、trace context 修复问题。 | P0 |
| Sentry JavaScript / Source Maps Skill | https://github.com/getsentry/sentry-javascript | Sentry 官方 | 约 8.7k stars / 1.8k forks | 前端错误监控、source maps | 前端 source maps、release、environment 配置常出错；非常适合 Skill 化。 | P1 |
| Sentry Python Backend Skill | https://github.com/getsentry/sentry-python | Sentry 官方 | 约 2.2k stars / 629 forks | Python 后端监控 | 覆盖 FastAPI/Django/Flask/Celery、PII scrub、采样、日志关联；适合后端 observability。 | P1 |
| Cloudflare Workers Skill | https://github.com/cloudflare/workers-sdk / https://developers.cloudflare.com/workers/ | Cloudflare 官方 | Workers SDK 约 4.2k stars / 1.3k forks | Edge API、KV/R2/D1/Durable Objects | 高频部署平台；适合学习 wrangler 配置、部署、调试、边缘服务开发流程。 | P0 |
| Cloudflare Agents Skill | https://github.com/cloudflare/agents | Cloudflare 官方 | 约 5.1k stars / 604 forks | Edge-native AI Agents | 与 Agent 主题高度相关；适合学习状态、实时交互、部署到边缘的 agent 应用模式。 | P0 |
| Cloudflare MCP Server Skill | https://github.com/cloudflare/mcp-server-cloudflare | Cloudflare 官方 | 约 3.9k stars / 427 forks | MCP、Cloudflare 资源管理 | 官方 MCP server；适合学习如何把云平台能力连接到 Agent 工具系统。 | P0 |
| Figma Plugin Development Skill | https://github.com/figma/plugin-samples / https://www.figma.com/plugin-docs/ | Figma 官方 | plugin-samples 约 1.8k stars / 350 forks | Figma 插件、设计自动化 | 设计工具自动化高频；可学习节点操作、UI 插件、导出设计资产。 | P1 |
| Figma REST API Skill | https://github.com/figma/rest-api-spec / https://www.figma.com/developers/api | Figma 官方 | 官方 REST API 规范 | 读取设计文件、组件、变量、评论、导出图片 | 适合把 Figma 文件转为设计系统、前端代码、审查报告。 | P1 |
| Figma Code Connect Skill | https://github.com/figma/code-connect | Figma 官方 | 约 1.5k stars / 120 forks | 设计系统、组件映射 | 让设计组件和代码组件关联，适合设计系统一致性维护。 | P1 |

## B. Anthropic 官方 / Claude 官方生态候选

| Skill / Workflow | 原始链接 | 上游 | 热度/证据 | 任务类型 | 为什么优秀 | 优先级 |
|---|---|---|---|---|---|---|
| `hook-development` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/plugin-dev/skills/hook-development | Anthropic 官方 | claude-plugins-official 约 30k stars / 3.3k forks | Claude Code Hooks、事件自动化、安全拦截 | 结构完整；覆盖 PreToolUse/PostToolUse/Stop/SubagentStop 等事件，适合学习事件驱动 agent 自动化。 | P0 |
| `command-development` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/plugin-dev/skills/command-development | Anthropic 官方 | 同上 | Slash commands、可复用命令工作流 | references 深，覆盖 frontmatter、动态参数、文件引用、bash 执行、交互式命令和测试策略。 | P0 |
| `plugin-structure` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/plugin-dev/skills/plugin-structure | Anthropic 官方 | 同上 | Plugin 结构设计、打包发布 | 含 minimal/standard/advanced plugin 示例、manifest reference、component patterns。 | P0 |
| `claude-md-improver` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/claude-md-management/skills/claude-md-improver | Anthropic 官方 | 同上 | 项目记忆、CLAUDE.md 审计 | discovery → quality assessment → targeted update 流程清晰，适合学习项目上下文文件如何系统改进。 | P0 |
| `math-olympiad` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/math-olympiad/skills/math-olympiad | Anthropic 官方 | 同上 | 高难推理、证明、对抗验证 | 设计 fresh-context adversarial verifier、失败模式、置信度校准和 no confident solution 机制。 | P0 |
| `playground` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/playground/skills/playground | Anthropic 官方 | 同上 | 可视化探索、数据/代码/设计 playground | 含 code-map、design-playground、document-critique、data-explorer、diff-review、concept-map 模板。 | P1 |
| `claude-automation-recommender` | https://github.com/anthropics/claude-plugins-official/tree/main/plugins/claude-code-setup/skills/claude-automation-recommender | Anthropic 官方 | 同上 | Claude Code 配置建议、hooks/MCP/subagents 推荐 | references 覆盖 skills、MCP servers、plugins、hooks patterns、subagent templates，是自动化推荐元工作流。 | P0 |
| `launch-your-agent` | https://github.com/anthropics/launch-your-agent/tree/main/.claude/skills/launch-your-agent | Anthropic 官方 | launch-your-agent 约 467 stars / 95 forks | 从 idea 到 Claude Managed Agent 上线 | 从 interview、scope v0、API/mock connectors、grading、iteration 到 schedule，链路端到端。 | P0 |
| `wrap-up` | https://github.com/anthropics/launch-your-agent/tree/main/.claude/skills/wrap-up | Anthropic 官方 | 同上 | 项目收尾、handoff、复盘 | 适合学习长任务结束时如何生成可靠状态、产物索引、后续 action items。 | P1 |
| `scvi-tools` | https://github.com/anthropics/knowledge-work-plugins/tree/main/bio-research/skills/scvi-tools | Anthropic 官方 | knowledge-work-plugins 约 21k stars / 2.5k forks | 生物信息学、single-cell deep learning | 垂直科学工具链 Skill；references 完整，含模型选择、数据准备、troubleshooting。 | P1 |
| `knowledge-synthesis` | https://github.com/anthropics/knowledge-work-plugins/tree/main/enterprise-search/skills/knowledge-synthesis | Anthropic 官方 | 同上 | 企业搜索、多源知识综合、溯源回答 | 明确处理去重、freshness/authority、source attribution，是 RAG 最后一公里样本。 | P0 |
| `discover-brand` | https://github.com/anthropics/knowledge-work-plugins/tree/main/partner-built/brand-voice/skills/discover-brand | Anthropic 官方/partner-built | 同上 | 品牌资料发现、guideline 生成 | 覆盖 Notion/Confluence/Drive/Box/SharePoint/Figma/Gong/Slack 等多源搜索与归纳。 | P1 |
| `launch-review` | https://github.com/anthropics/claude-for-legal/tree/main/product-legal/skills/launch-review | Anthropic 官方 | claude-for-legal 约 8.4k stars / 1.5k forks | 产品上线法律审查、风险 memo | 高风险专业领域 Skill；包含 framework loading、PRD/ticket 拉取、category review、privilege/destination check。 | P1 |
| `tabular-review` | https://github.com/anthropics/claude-for-legal/tree/main/corporate-legal/skills/tabular-review | Anthropic 官方 | 同上 | 法务尽调、结构化表格审查 | 带 Excel/Sheets 输出 references，适合学习文档审查如何稳定产出结构化表格。 | P1 |

## C. 高星社区 / Agent 工程 / DevOps 候选

| Skill / Workflow | 原始链接 | 上游 | 热度/证据 | 任务类型 | 为什么优秀 | 优先级 |
|---|---|---|---|---|---|---|
| BMAD-METHOD | https://github.com/bmad-code-org/BMAD-METHOD | GitHub 社区 | 约 49k stars / 5.7k forks | Agile AI-driven development、产品/PRD/开发流程 | 从业务/产品规划到开发执行的 AI 敏捷方法，适合提炼成产品-需求-开发全链路 Skill/SOP。 | P0 |
| wshobson/agents | https://github.com/wshobson/agents | GitHub 社区 | 约 37k stars / 4k forks | Multi-agent marketplace、跨平台 agents | 支持 Claude Code、Codex CLI、Cursor、OpenCode、Copilot、Gemini CLI，适合研究跨 Agent 平台组织方式。 | P0 |
| claude-task-master | https://github.com/eyaltoledano/claude-task-master | GitHub 社区 | 约 27k stars / 2.6k forks | AI task management、任务拆解 | 聚焦把需求拆成可执行任务，和“任务流程化”目标高度契合。 | P0 |
| claude-code-templates | https://github.com/davila7/claude-code-templates | GitHub 社区 | 约 28k stars / 2.9k forks | Claude Code 配置、agents、commands、hooks、监控 | 高星 Claude Code 工具库，适合学习 rules、commands、hooks 如何工程化。 | P0 |
| SuperClaude Framework | https://github.com/SuperClaude-Org/SuperClaude_Framework | GitHub 社区 | 约 23k stars / 1.9k forks | Claude Code commands、persona、开发方法论 | 结合 specialized commands、cognitive personas、development methodologies，可拆解“人格+命令+方法论”的 Skill 设计。 | P0 |
| NirDiamant/GenAI_Agents | https://github.com/NirDiamant/GenAI_Agents | GitHub 社区 | 约 22k stars / 3.8k forks | GenAI Agent 教程、multi-agent 技术实现 | 50+ agent 技术教程和实现，适合补充 Agent 技术模式库。 | P1 |
| contains-studio/agents | https://github.com/contains-studio/agents | GitHub 社区 | 约 12k stars / 2.5k forks | Claude/AI agents collection | fork 比例高，说明复用意愿强；适合横向比较 agent description、tool constraints、任务边界写法。 | P1 |
| context-engineering-intro | https://github.com/coleam00/context-engineering-intro | GitHub 社区 | 约 13k stars / 2.7k forks | Context engineering | 可迁移为“上下文准备/文档投喂/任务约束”类 Skill。 | P1 |
| ai-dev-tasks | https://github.com/snarktank/ai-dev-tasks | GitHub 社区 | 约 7.7k stars / 1.7k forks | AI dev task management | 简洁的 AI dev agent 任务管理系统，适合研究任务卡、状态、验收标准推进。 | P1 |
| agent-rules | https://github.com/steipete/agent-rules | GitHub 社区 | 约 5.6k stars / 510 forks | Agent rules、Claude Code/Cursor 规则约束 | 专注 Rules and Knowledge，适合提炼反幻觉、代码风格、工程边界、上下文规则。 | P1 |
| ottomator-agents | https://github.com/coleam00/ottomator-agents | GitHub 社区 | 约 5.6k stars / 1.9k forks | Open-source AI agents、业务自动化 | fork 数高，适合找业务自动化、知识处理、工具调用型 agent 模板。 | P2 |
| Context7 | https://github.com/upstash/context7 | Upstash / GitHub 社区 | 约 57k stars / 2.7k forks | Docs for LLMs、AI code editor 文档上下文 | 解决 LLM 使用过期文档的核心痛点，适合研究动态文档注入型 Skill。 | P0 |
| awesome-mcp-servers | https://github.com/punkpeye/awesome-mcp-servers | GitHub 社区 curated list | 约 89k stars / 12k forks | MCP / DevOps / 工具集成来源索引 | 超高星 MCP server 索引，适合作为后续 DevOps、Docs、GitHub、数据库、云服务类 Skill 上游候选池。 | P0 |
| PRPs-agentic-eng | https://github.com/Wirasm/PRPs-agentic-eng | GitHub 社区 | 约 2.2k stars / 609 forks | PRP / agentic engineering workflow | 主题贴近 Prompt/PRD/Plan/执行，可与 BMAD、Spec Kit 对比。 | P2 |
| `diagnosing-bugs` | https://github.com/mattpocock/skills/tree/main/skills/engineering/diagnosing-bugs | mattpocock/skills | 约 143k stars / 12k forks | Debug、反馈环构建 | 核心方法是先构建 tight feedback loop，列出测试、curl、CLI fixture、Playwright、trace replay、fuzz、bisect 等反馈环。 | P0 |
| `browser-testing-with-devtools` | https://github.com/addyosmani/agent-skills/tree/main/skills/browser-testing-with-devtools | addyosmani/agent-skills | 约 66k stars / 7k forks | 浏览器真实运行验证、Chrome DevTools MCP | 强调不用静态猜测，而是用 DevTools MCP 看 DOM、console、network、performance、visual output。 | P0 |

---

## 建议下一批优先深度研究的 12 个

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

这些的共同点：**不是单纯提示词，而是包含明确流程、工具调用、验证方式或高社区复用价值。**
