# Anthropic `claude-automation-recommender` 方法论研究

- 来源：Anthropic 官方 `claude-plugins-official`
- 原始链接：https://github.com/anthropics/claude-plugins-official/tree/main/plugins/claude-code-setup/skills/claude-automation-recommender
- 本仓库原文路径：`originals/anthropic/claude-plugins-official/plugins/claude-code-setup/skills/claude-automation-recommender/`
- 所属任务类型：项目自动化诊断 / Agent Workflow 设计 / Claude Code 初始化配置
- 研究方式：独立 sub agent 只读研究

## 0. 为什么认为这个 Skill 优秀

- **官方/权威来源**：来自 Anthropic 官方 `claude-plugins-official` 仓库，是 Claude Code 官方插件生态的一部分。
- **社区质量信号强**：调研时仓库约 **30,995 stars / 3,371 forks**，默认分支 `main`，持续更新。
- **元自动化能力**：它不是执行某个单点任务，而是分析一个项目应该用哪些自动化能力，是“推荐自动化的 Skill”。
- **结构清晰**：包含 `SKILL.md` 与 references：`hooks-patterns.md`、`mcp-servers.md`、`plugins-reference.md`、`skills-reference.md`、`subagent-templates.md`。
- **可迁移价值高**：核心方法是“先读取项目事实，再识别自动化机会，最后按自动化类型输出少量高价值建议”，可迁移到 Hermes 的项目自动化顾问、商业流程自动化评估、工程治理建议等场景。

## 1. 它要完成的任务

`claude-automation-recommender` 的核心任务是：分析一个代码库，基于语言、框架、依赖、目录结构、测试、CI/CD、外部服务、已有 Claude 配置等事实，推荐最适合该项目的 Claude Code 自动化方案。

它不直接创建或修改配置，而是生成一份**只读自动化推荐报告**，覆盖：

1. MCP Server 推荐。
2. Skill 推荐。
3. Hook 推荐。
4. Subagent 推荐。
5. Plugin 推荐。

它明确强调：**只读分析，不创建或修改任何文件**。用户可以根据推荐自行实施，也可以继续要求 Claude 帮忙实现。

## 2. 触发条件

该 Skill 适合在以下情况触发：

- 用户要求推荐 Claude Code 自动化。
- 用户想优化 Claude Code 使用方式。
- 用户提到改进 Claude Code workflow。
- 用户问某个项目该如何初次配置 Claude Code。
- 用户问应该使用哪些 Claude Code 功能。
- 用户明确询问 hooks、subagents、skills、plugins、MCP servers 中某一类。

如果用户只问某一类，例如“有哪些 hooks 适合我的项目”，它会聚焦该类并给出更多建议，约 3–5 个；如果是整体推荐，则每类只给 1–2 个，避免信息过载。

## 3. 输入 / 输出

### 输入

它的输入是项目代码库与用户意图，而不是结构化表单。关键输入包括：

#### 项目根目录文件

- `package.json`
- `pyproject.toml`
- `Cargo.toml`
- `go.mod`
- `pom.xml`

#### 依赖与技术栈

- React / Vue / Angular / Next.js
- Express / FastAPI / Django
- Prisma / Supabase / Convex
- Stripe / OpenAI / AWS SDK

#### Claude Code 配置

- `.claude/`
- `CLAUDE.md`
- `.mcp.json`

#### 项目结构

- `src/`
- `app/`
- `lib/`
- `tests/`
- `components/`
- `pages/`
- `api/`

#### 测试与 CI

- Jest / pytest / Playwright
- GitHub Actions / CircleCI

#### 工程治理信号

- `.env`
- lock files
- Dockerfile / docker-compose
- Linear issue references
- Sentry / Slack / Notion 等服务痕迹

### 输出

输出是一份 Markdown 推荐报告，官方建议结构：

```markdown
## Claude Code Automation Recommendations

### Codebase Profile
- Type
- Framework
- Key Libraries

### 🔌 MCP Servers
### 🎯 Skills
### ⚡ Hooks
### 🤖 Subagents
```

每条推荐应包含：

- 名称。
- 为什么推荐。
- 安装或创建位置。
- 适用调用方式。
- 官方 plugin 来源（如有）。
- 必要配置片段。

报告末尾还应提示用户：

- 可以要求更多某一类推荐。
- 可以继续要求实现某条建议。

## 4. 完成任务的步骤流程

### Phase 1：代码库分析

先收集项目事实，而不是直接套模板。官方示例命令包括：

```bash
ls -la package.json pyproject.toml Cargo.toml go.mod pom.xml 2>/dev/null
cat package.json 2>/dev/null | head -50
cat package.json 2>/dev/null | grep -E '"(react|vue|angular|next|express|fastapi|django|prisma|supabase|convex|stripe)"'
ls -la .claude/ CLAUDE.md 2>/dev/null
ls -la src/ app/ lib/ tests/ components/ pages/ api/ 2>/dev/null
```

要捕获的关键指标：

- 语言 / 框架。
- 前端栈。
- 后端栈。
- 数据库。
- 外部 API。
- 测试框架。
- CI/CD。
- issue tracking。
- 文档模式。

### Phase 2：按自动化类别生成推荐

#### A. MCP Server 推荐

检测信号与推荐关系：

| 项目信号 | 推荐 |
|---|---|
| React / Express / Prisma / Stripe 等流行库 | `context7` |
| 前端项目或 UI 测试需求 | `Playwright MCP` |
| Supabase | `Supabase MCP` |
| Convex | `Convex MCP` |
| PostgreSQL / MySQL | 数据库 MCP |
| GitHub 仓库 | `GitHub MCP` |
| Linear issue | `Linear MCP` |
| AWS SDK / IaC | `AWS MCP` |
| Sentry | `Sentry MCP` |
| Docker | `Docker MCP` |
| 需要长期上下文 | `Memory MCP` |

#### B. Skills 推荐

官方 plugin skills：

- `skill-development`：来自 `plugin-dev`。
- `commit`：来自 `commit-commands`。
- `frontend-design`：来自 `frontend-design`。
- `writing-rules`：来自 `hookify`。
- `feature-dev`：来自 `feature-dev`。

项目自定义 skills：

- `api-doc`：为 API routes 生成 OpenAPI 文档。
- `create-migration`：生成并验证数据库 migration。
- `gen-test`：根据项目示例生成测试。
- `new-component`：按模板生成组件、测试、Storybook。
- `pr-check`：按项目 checklist 审查 PR。
- `release-notes`：根据 git history 生成 release notes。
- `project-conventions`：Claude-only 的项目约定知识。
- `setup-dev`：开发环境初始化流程。

#### C. Hooks 推荐

检测信号与 hook 推荐：

| 项目信号 | 推荐 Hook |
|---|---|
| Prettier | PostToolUse 自动格式化 |
| ESLint | PostToolUse 自动 lint/fix |
| Ruff / Black | Python 格式化与 lint |
| TypeScript | PostToolUse `tsc --noEmit` |
| tests 目录 | 编辑后运行相关测试 |
| `.env` | PreToolUse 阻止敏感文件编辑 |
| lock files | 阻止直接编辑 lock 文件 |
| Go | `gofmt` |
| Rust | `rustfmt` |
| 多任务场景 | Notification hook，如 permission prompt / idle prompt 提醒 |

#### D. Subagents 推荐

常见 subagent：

- `code-reviewer`：大代码库并行审查。
- `security-reviewer`：认证、支付、PII、安全敏感代码审查。
- `api-documenter`：API 文档生成。
- `performance-analyzer`：N+1、复杂度、内存泄漏、热点路径分析。
- `ui-reviewer`：可访问性与 UX 审查。
- `test-writer`：测试生成。
- `dependency-updater`：依赖更新。
- `migration-helper`：框架或版本迁移。

模型选择：

- `haiku`：简单重复检查。
- `sonnet`：大多数审查与分析任务。
- `opus`：复杂迁移、架构分析。

#### E. Plugins 推荐

官方 plugin 推荐：

- `anthropic-agent-skills`：通用核心技能包。
- `plugin-dev`：开发 Claude Code 插件。
- `pr-review-toolkit`：PR 审查。
- `code-review`：自动代码审查。
- `code-simplifier`：重构简化。
- `feature-dev`：端到端功能开发。
- `commit-commands`：git commit / PR workflow。
- `hookify`：从规则生成 hooks。
- `frontend-design`：前端 UI。
- LSP 插件：`typescript-lsp`、`pyright-lsp`、`gopls-lsp`、`rust-analyzer-lsp`。

### Phase 3：输出报告

输出必须克制：

- 每类推荐 1–2 个。
- 不相关类别可以跳过。
- 用户指定某类别时，该类别给 3–5 个。
- 结尾说明可继续索要更多推荐或要求实现。

## 5. 调用的工具 / 脚本 / MCP / API

### Skill 自身工具权限

frontmatter 声明：

```yaml
tools: Read, Glob, Grep, Bash
```

即：

- `Read`：读取配置文件、代码、文档。
- `Glob`：查找项目结构。
- `Grep`：检索依赖、模式、关键字。
- `Bash`：运行只读 shell 命令分析项目。

### Claude Code 机制

涉及的机制：

- `.claude/skills/<name>/SKILL.md`：项目 Skill。
- `.claude/settings.json`：hooks 与权限配置。
- `.claude/agents/<name>.md`：subagent。
- `.mcp.json`：项目级 MCP 配置，可提交到仓库共享。
- `~/.claude.json`：全局 MCP 配置。
- `/plugin install <plugin-name>`：安装 plugin。
- `/skill-name`：用户手动调用 Skill。
- `disable-model-invocation: true`：只允许用户调用。
- `user-invocable: false`：只允许 Claude 自动调用。
- `context: fork`：隔离上下文运行。
- `allowed-tools`：限制 skill 工具权限。
- ``!`command` ``：动态注入命令输出。
- `claude --mcp-debug`：调试 MCP。
- `claude -p`：headless 模式，用于 CI / 自动化。

### 外部 API

该 Skill 本身不硬编码调用外部 API，但会推荐 MCP server 与外部服务连接，例如 GitHub、Linear、Supabase、Convex、AWS、Sentry、Slack、Notion 等。

## 6. 关键决策点

### 6.1 是否是前端项目？

React / Vue / Angular / Next.js → 推荐 `Playwright MCP`、`frontend-design`、`ui-reviewer`。

### 6.2 是否有数据库 / 后端平台？

Prisma / Supabase / Convex / PostgreSQL → 数据库 MCP、migration skill、performance analyzer。

### 6.3 是否有测试体系？

Jest / pytest / Vitest / Playwright → test hook、test-writer、gen-test skill。

### 6.4 是否有安全敏感域？

auth / login / session / stripe / payment / user / pii → security-reviewer、敏感文件保护 hook。

### 6.5 是否已有 Claude Code 配置？

- 有 `.claude/` / `CLAUDE.md`：推荐增量优化。
- 没有：推荐初始 setup。

### 6.6 用户问整体还是特定类别？

- 整体：每类 1–2 个。
- 指定类别：该类 3–5 个。

### 6.7 自动化应放在哪里？

| 任务属性 | 自动化载体 |
|---|---|
| 事件驱动、强制规则 | hook |
| 专家并行审查 | subagent |
| 可复用流程 / 知识 / 模板 | skill |
| 外部系统连接 | MCP |
| 多个能力打包共享 | plugin |

## 7. 验证方法 / 交付 Gate

该 Skill 的 gate 主要是推荐质量：

1. **只读**
   - 不创建、不修改文件。
   - 不安装 MCP。
   - 不写 `.claude` 配置。
   - 只输出建议。

2. **基于事实**
   - 推荐必须能回溯到检测信号，例如依赖、目录、配置文件。

3. **数量受控**
   - 每类最多 1–2 个核心建议。
   - 避免把 reference 全部倒给用户。

4. **分类准确**
   - 不把 skill 推荐成 hook。
   - 不把 MCP 当成 plugin。
   - 不把 subagent 当成普通命令。

5. **输出可执行**
   - hooks → `.claude/settings.json`
   - skills → `.claude/skills/<name>/SKILL.md`
   - agents → `.claude/agents/<name>.md`
   - MCP → `.mcp.json` 或 `~/.claude.json`
   - plugins → `/plugin install`

6. **包含后续路径**
   - 告诉用户可继续请求更多某类推荐。
   - 告诉用户可继续要求实现某条建议。

## 8. 可迁移到 Hermes Skills / 长线自动化的写法

### 8.1 自动化推荐器本身应该是一个 Skill

可设计为：

```text
skills/automation-recommender/
├── SKILL.md
└── references/
    ├── cron-patterns.md
    ├── memory-patterns.md
    ├── plugin-patterns.md
    ├── integration-patterns.md
    ├── project-skill-patterns.md
    └── verification-gates.md
```

### 8.2 Claude Code 类型映射到 Hermes

| Claude Code | Hermes 可迁移形态 |
|---|---|
| Hooks | cron、watcher、pre/post action、policy guard |
| Subagents | 子任务 agent、审查 agent、研究 agent |
| Skills | Hermes profile skills |
| Plugins | 可安装 skill/plugin bundle |
| MCP Servers | Hermes tool integration、外部 API、浏览器、数据库 |
| `.claude/settings.json` | Hermes profile 配置 |
| `CLAUDE.md` | 项目记忆 / project instructions |
| ``!`command` `` 动态注入 | skill 内运行 terminal / API 获取上下文 |

### 8.3 长线自动化方法论

可迁移步骤：

1. 扫描项目或业务流程。
2. 识别重复任务。
3. 判断任务触发方式：
   - 人主动调用。
   - 文件变化触发。
   - 定时触发。
   - 外部事件触发。
   - agent 并行分析。
4. 选择自动化载体。
5. 输出最小高价值建议。
6. 附带验证 gate。
7. 再由另一个实现型 Skill 落地。

这比直接写自动化更稳，因为它先做“自动化设计评审”。

## 9. 适合归类的任务类型

- 项目自动化诊断
- Agent 工作流设计
- 开发者体验优化
- Claude Code / AI IDE 初始化配置
- 工程治理建议
- 工具链推荐
- 自动化架构规划
- 项目 onboarding
- AI-assisted SDLC 配置顾问
- 元自动化：推荐哪些任务值得自动化，以及应该用什么机制自动化

## 10. 风险与注意事项

- 不应在推荐阶段直接修改项目，否则会把“诊断”变成“实施”。
- 推荐必须基于实际项目证据，不能凭技术栈刻板印象硬推。
- 不要一次推荐太多，否则用户无法执行。
- 不要混淆自动化载体：hook、skill、subagent、MCP、plugin 的适用边界不同。
- MCP 推荐需要考虑用户是否有对应外部服务账号和权限。
- Hook 推荐需要考虑误伤风险，强制型规则应更谨慎。
- Subagent 推荐适合复杂审查和并行分析，不适合小而确定的任务。
- 如果项目已有 Claude 配置，应做增量建议，不要覆盖现有体系。

## 11. 原文归档说明

已归档目录：

```text
originals/anthropic/claude-plugins-official/plugins/claude-code-setup/skills/claude-automation-recommender/
```

已保存：

```text
SKILL.md
references/hooks-patterns.md
references/mcp-servers.md
references/plugins-reference.md
references/skills-reference.md
references/subagent-templates.md
.source-commit
```

其中迁移价值最高的文件：

1. `SKILL.md`：核心流程、触发条件、输出格式、决策框架。
2. `references/skills-reference.md`：Skill frontmatter、调用控制、自定义 Skill 模板、动态上下文注入。
3. `references/mcp-servers.md`：外部集成推荐矩阵。
4. `references/hooks-patterns.md`：事件驱动自动化与安全 guard 模式。
5. `references/subagent-templates.md`：agent 类型、模型选择、工具权限设计。
6. `references/plugins-reference.md`：官方 plugin 生态参考。

## 12. 一句话方法论

先用只读方式识别项目事实，再把重复流程、质量检查、外部集成和专家审查分别映射到 skills、hooks、MCP、subagents、plugins，并只推荐每类最有价值的 1–2 个自动化。
