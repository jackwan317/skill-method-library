# Anthropic `command-development` 方法论研究

- 来源：Anthropic 官方 `claude-plugins-official`
- 原始链接：https://github.com/anthropics/claude-plugins-official/tree/main/plugins/plugin-dev/skills/command-development
- 本仓库原文路径：`originals/anthropic/claude-plugins-official/plugins/plugin-dev/skills/command-development/`
- 所属任务类型：Claude Code Slash Command / Agent Workflow 产品化 / 开发者自动化命令
- 研究方式：独立 sub agent 只读研究

## 0. 为什么认为这个 Skill 优秀

- **官方/权威来源**：来自 Anthropic 官方 `claude-plugins-official` 仓库，属于 Claude Code 插件开发官方 Skill。
- **社区质量信号强**：调研时仓库约 **30,993 stars / 3,371 forks**，默认分支 `main`，仍在更新。
- **结构完整**：包含 `SKILL.md`、`README.md`、`examples/`、`references/`，覆盖从基础命令到交互式命令、插件命令、高级 workflow、测试、文档、marketplace 分发。
- **接口意识强**：它把 slash command 当成有参数、有权限、有模型选择、有上下文注入、有错误处理的小型产品，而不是一段随意 prompt。
- **可迁移价值高**：它沉淀的是“把常用工作流产品化为可复用命令”的方法，可迁移到 Hermes Skills、内部 runbook、CI 辅助命令、代码审查机器人、运维 SOP。

## 1. 它要完成的任务

`command-development` 的核心任务是：指导用户为 Claude Code 创建高质量 slash command。

它帮助开发者完成：

1. 创建 Markdown 格式命令文件。
2. 选择命令位置：项目、个人、插件。
3. 编写真正给 Claude 执行的命令正文。
4. 配置 YAML frontmatter。
5. 设计动态参数接口。
6. 使用文件引用和 Bash 输出注入上下文。
7. 用 `${CLAUDE_PLUGIN_ROOT}` 引用插件内部资源。
8. 设计交互式、多步骤、可恢复、可分发命令。
9. 建立测试、验证、文档和发布标准。

一句话：它把 slash command 从“一个 Markdown prompt”提升为“有接口、有权限、有上下文、有测试、有分发质量标准的小型自动化产品”。

## 2. 触发条件

该 Skill 应在用户提出以下需求时触发：

- create a slash command
- add a command
- write a custom command
- define command arguments
- use command frontmatter
- organize commands
- create command with file references
- interactive command
- use AskUserQuestion in command
- 需要了解 slash command 结构
- 需要 YAML frontmatter 字段说明
- 需要动态参数、Bash 执行、交互模式、命令最佳实践
- 需要 Claude Code command development 指导

这个触发条件设计很精准：它围绕“命令开发”而非泛泛开发帮助。

## 3. 输入 / 输出

### 输入

常见输入包括：

1. **命令目标**  
   例如审查 PR、运行测试、生成文档、部署、分析代码质量。

2. **命令作用域**

```text
.claude/commands/          # 项目命令
~/.claude/commands/        # 个人命令
plugin-name/commands/      # 插件命令
```

3. **参数需求**

- `$ARGUMENTS`：全部自由文本参数。
- `$1`, `$2`, `$3`：位置参数。
- `argument-hint`：给用户展示参数格式。

4. **工具权限需求**

- 是否只读：`Read`, `Grep`, `Glob`
- 是否修改：`Write`, `Edit`
- 是否运行 shell：`Bash(git:*)`, `Bash(npm:*)`
- 是否交互：`AskUserQuestion`
- 是否启动 agent：`Task`

5. **上下文来源**

- 文件引用：`@file`
- Bash 输出：``!`command` ``
- 插件资源：`${CLAUDE_PLUGIN_ROOT}/...`

6. **交付质量要求**

- 是否需要测试。
- 是否要跨项目复用。
- 是否要发 marketplace。
- 是否需要多阶段恢复。

### 输出

输出通常是一个或多个 `.md` 命令文件，例如：

```markdown
---
description: Review code for security issues
argument-hint: [file-path]
allowed-tools: Read, Grep, Bash(git:*)
model: sonnet
---

Review @$1 for:

- SQL injection
- XSS
- Authentication issues
- Insecure data handling

Provide line numbers, severity, and remediation advice.
```

复杂输出还包括：

- 命令目录结构。
- 命名规范。
- 参数约定。
- 校验逻辑。
- 错误处理。
- 测试方案。
- README 或命令内文档。
- 插件资源调用方式。
- 分发注意事项。

## 4. 完成任务的步骤流程

### Step 1：定义命令职责

官方强调：**One command, one task.**

例如 `/review-pr` 只做 PR review，不应同时部署、生成文档、修复 bug。复杂流程可拆多个命令，或设计 workflow 命令。

### Step 2：选择命令位置

```text
.claude/commands/          # 项目命令，团队共享
~/.claude/commands/        # 个人命令，跨项目使用
plugin-name/commands/      # 插件命令，随 plugin 分发
```

插件命令会自动发现，无需手动注册，并在 `/help` 中显示为 plugin command。

### Step 3：确定命名与组织

小规模命令可扁平化：

```text
.claude/commands/
├── build.md
├── test.md
└── deploy.md
```

大规模命令建议 namespace：

```text
.claude/commands/
├── ci/
│   ├── build.md
│   └── test.md
├── git/
│   ├── commit.md
│   └── pr.md
└── docs/
    └── generate.md
```

命名规范：

- 动词优先。
- 使用 hyphen：`review-pr`、`fix-issue`。
- 避免 `test`、`run`、`do-stuff` 这类泛名。
- 插件命令可带插件前缀避免冲突。

### Step 4：写 Markdown 命令正文

最重要原则：**命令正文是写给 Claude 的执行指令，不是写给用户看的说明。**

正确：

```markdown
Review this code for security vulnerabilities including SQL injection, XSS, and authentication issues. Provide line numbers and severity ratings.
```

错误：

```markdown
This command will review your code for security issues.
```

前者会让 agent 直接执行，后者只是描述功能。

### Step 5：添加 frontmatter

基础模板：

```markdown
---
description: Brief description
argument-hint: [arg1] [arg2]
allowed-tools: Read, Bash(git:*)
model: sonnet
---

Command prompt content...
```

字段规格：

| 字段 | 作用 | 值 |
|---|---|---|
| `description` | `/help` 中显示的短描述 | 字符串，建议 60 字符以内 |
| `allowed-tools` | 限定工具权限 | `Read`, `Write`, `Edit`, `Bash(git:*)` 等 |
| `model` | 指定执行模型 | `haiku`, `sonnet`, `opus` |
| `argument-hint` | 参数提示 | `[pr-number] [priority]` |
| `disable-model-invocation` | 禁止 SlashCommand tool 程序化调用 | boolean，默认 false |

模型选择建议：

- `haiku`：快速、简单、公式化命令。
- `sonnet`：默认、均衡。
- `opus`：复杂架构分析、深度推理。

`allowed-tools` 应遵循最小权限：

```yaml
allowed-tools: Read, Grep
allowed-tools: Bash(git:*)
allowed-tools: Bash(npm:*), Bash(jest:*)
```

避免：

```yaml
allowed-tools: "*"
```

### Step 6：设计参数接口

全部参数：

```markdown
Fix issue #$ARGUMENTS following our coding standards.
```

位置参数：

```markdown
Review pull request #$1 with priority $2. Assign follow-up to $3.
```

参数文档与 `argument-hint` 对齐：

```yaml
argument-hint: [pr-number] [priority] [assignee]
```

缺参时应给出 usage，而不是沉默失败：

```markdown
If $1 is missing, explain:
Usage: /review-pr [pr-number]
```

### Step 7：组装上下文

文件引用：

```markdown
Review @$1 for code quality and security issues.
```

静态文件引用：

```markdown
Review @package.json and @tsconfig.json for consistency.
```

Bash 执行：

```markdown
Files changed: !`git diff --name-only`
Current status: !`git status`
Recent commits: !`git log --oneline -5`
```

插件资源：

```markdown
Run analysis: !`node ${CLAUDE_PLUGIN_ROOT}/scripts/analyze.js $1`
Template: @${CLAUDE_PLUGIN_ROOT}/templates/report.md
Config: @${CLAUDE_PLUGIN_ROOT}/config/settings.json
```

关键思想：命令不是静态 prompt，而是可以通过文件、参数、命令输出动态构造执行上下文。

### Step 8：加入验证和错误处理

参数验证：

```markdown
Validate environment: !`echo "$1" | grep -E "^(dev|staging|prod)$" || echo "INVALID"`

If invalid:
Explain valid environments and show usage.
```

文件存在性：

```markdown
Check file exists: !`test -f $1 && echo "EXISTS" || echo "MISSING"`

If missing:
Explain expected path and provide example.
```

插件资源验证：

```markdown
- Script: !`test -x ${CLAUDE_PLUGIN_ROOT}/bin/analyze && echo "✓" || echo "✗"`
- Config: !`test -f ${CLAUDE_PLUGIN_ROOT}/config.json && echo "✓" || echo "✗"`
```

错误处理：

```markdown
Execute build: !`bash ${CLAUDE_PLUGIN_ROOT}/scripts/build.sh 2>&1 || echo "BUILD_FAILED"`

If failed:
Analyze error output and suggest likely fixes.
```

### Step 9：测试、文档化、分发

`testing-strategies.md` 将测试分层：

1. 结构测试：`.md` 扩展名、frontmatter 分隔符。
2. frontmatter 测试：`model` 合法、description 长度等。
3. 手动调用：确认 `/help` 可见。
4. 参数测试：缺参、非法参数、多参数。
5. 文件引用测试：存在、不存在、大文件、多文件。
6. Bash 测试：允许命令能执行，禁止命令被拒绝。
7. 集成测试：hooks、MCP、multi-command workflow、状态文件。
8. 性能和 UX 测试。
9. marketplace 发布前 checklist。

## 5. 调用的工具 / 脚本 / MCP / API

该 Skill 本身没有独立 `scripts/`、`agents/`、`templates/` 目录；关键机制以文档和 examples 的方式说明。

### Claude Code slash command 机制

- Markdown `.md` 命令文件。
- YAML frontmatter。
- `/help` 自动发现。
- `$ARGUMENTS` 和 `$1/$2/...` 参数替换。
- `@path` 文件内容引用。
- ``!`bash command` `` Bash 输出注入。
- `allowed-tools` 工具权限控制。
- `model` 模型选择。
- `disable-model-invocation` 控制是否允许程序化调用。

### 插件机制

插件命令通过 `plugin-name/commands/` 自动发现。`${CLAUDE_PLUGIN_ROOT}` 用于可移植引用插件内部文件：

```markdown
!`node ${CLAUDE_PLUGIN_ROOT}/scripts/analyze.js`
@${CLAUDE_PLUGIN_ROOT}/templates/report.md
@${CLAUDE_PLUGIN_ROOT}/config/deploy-config.json
```

### 工具调用

文档涉及：

- `Read`
- `Write`
- `Edit`
- `Grep`
- `Glob`
- `Bash(...)`
- `AskUserQuestion`
- `Task`（用于启动 plugin agent）

### AskUserQuestion 机制

交互式命令适合：

- 多选项决策。
- 复杂配置。
- 多选场景。
- 用户偏好采集。
- 根据答案分支的 workflow。

参数结构示例：

```typescript
{
  questions: [
    {
      question: "Which authentication method should we use?",
      header: "Auth method",
      multiSelect: false,
      options: [
        {
          label: "OAuth 2.0",
          description: "Industry standard, supports multiple providers"
        }
      ]
    }
  ]
}
```

规则：

- 用户总可选择 Other。
- `multiSelect: true` 支持多选。
- 每题建议 2–4 个选项。
- 每次工具调用建议 1–4 个问题。

### MCP / API

该 Skill 没有提供具体 MCP server 或外部 API 实现。`testing-strategies.md` 提到 MCP 工具集成测试：确认 MCP server 可访问、工具调用成功、结果进入输出。因此 MCP 是可集成对象，不是核心依赖。

## 6. 关键决策点

### 6.1 命令还是 Skill？

`SKILL.md` 开头提示：`.claude/commands/` 是 legacy format；新 skills 推荐 `.claude/skills/<name>/SKILL.md`。因此：

- 长期知识能力：优先 Skill。
- 快捷工作流、团队命令、插件命令：slash command。

### 6.2 项目 / 个人 / 插件命令？

| 场景 | 位置 |
|---|---|
| 团队共享 | 项目命令 |
| 个人习惯 | 用户命令 |
| 可分发能力 | 插件命令 |

### 6.3 参数还是 AskUserQuestion？

- 简单、可脚本化输入：参数。
- 复杂选择、偏好、解释型决策：`AskUserQuestion`。

### 6.4 是否需要 Bash？

- 需要动态状态：使用 Bash。
- 纯文本分析：不需要。
- Bash 权限必须最小化。

### 6.5 使用什么模型？

- 高频简单：`haiku`。
- 默认标准：`sonnet`。
- 深度复杂：`opus`。

### 6.6 扁平还是 namespace？

- 5–15 个命令：扁平。
- 15+ 或有清晰分类：namespace。
- 插件 5+ 命令也建议 namespace。

### 6.7 是否状态化？

- 单次任务：无状态。
- 多阶段部署/发布：用 `.local.md` 状态文件、checkpoint、resume/cleanup 命令。

### 6.8 是否面向 marketplace？

面向未知用户时必须增加跨平台、依赖检测、清晰 onboarding、优雅降级、beta 测试和反馈路径。

## 7. 验证方法 / 交付 Gate

一个高质量命令的 gate：

1. 文件在正确目录。
2. 文件扩展名为 `.md`。
3. frontmatter YAML 合法，有成对 `---`。
4. `description` 简短、动词开头、可发现。
5. `argument-hint` 与正文参数一致。
6. `allowed-tools` 最小权限。
7. `model` 只使用 `haiku/sonnet/opus`。
8. 命令出现在 `/help`。
9. 无参调用有合理提示。
10. 有参调用能完成预期任务。
11. 文件引用能处理存在、不存在、多文件、大文件。
12. Bash 命令能执行，失败时有明确恢复建议。
13. 插件命令不写死路径，使用 `${CLAUDE_PLUGIN_ROOT}`。
14. 多阶段命令有状态文件、checkpoint、resume 或 cleanup。
15. 可分发命令有依赖检测、跨平台策略和降级行为。
16. 文档包含 usage、arguments、examples、requirements、troubleshooting、changelog。

## 8. 可迁移到 Hermes Skills / 长线自动化的写法

### 8.1 frontmatter 等价为 Skill metadata

用 name、description、trigger 说明能力边界。

### 8.2 argument-hint 等价为输入契约

在 Skill 中明确输入格式、必填/可选参数、默认值、示例。

### 8.3 allowed-tools 等价为工具权限策略

在 Hermes 中声明或约束 terminal、文件、网络、API 行为，遵循最小权限。

### 8.4 命令正文等价为 agent 执行规程

不要写“这个 Skill 会……”，而要写“当触发时，执行以下步骤……”。

### 8.5 references/examples 做渐进式披露

- Skill 主体：核心流程。
- references：完整规范。
- examples：典型案例。

### 8.6 长线自动化采用 workflow pattern

对部署、报告生成、数据巡检、PR 审查，可拆为：

- init
- validate
- execute
- report
- cleanup/resume

### 8.7 验证 gate 转为自动化验收标准

每次执行后输出：

- 输入是否完整。
- 前置条件是否满足。
- 执行动作。
- 产物路径。
- 验证命令结果。
- 失败恢复建议。

### 8.8 marketplace 思路迁移为组织内可复用标准

长期运行的 Hermes Skills 也应有版本、变更记录、兼容性、依赖检测和异常降级。

## 9. 适合归类的任务类型

- 开发者工具构建
- Agent workflow 设计
- Claude Code 插件开发
- Slash command 设计
- Prompt 产品化
- 自动化 runbook
- 多步骤工程流程
- 代码审查 / 测试 / 部署 / 文档生成命令
- 交互式配置向导
- Marketplace / 插件分发质量规范
- Hermes Skill 设计方法论参考

## 10. 风险与注意事项

- Slash command 正文必须是执行指令，不是功能介绍。
- `allowed-tools` 不能过宽，否则命令成为隐性权限扩大入口。
- Bash 输出注入很强大，但要处理失败、权限、跨平台差异。
- 参数接口必须清楚，否则用户很难复用。
- 插件命令不能硬编码路径，必须用 `${CLAUDE_PLUGIN_ROOT}`。
- 多阶段 workflow 如果没有状态恢复，会在中断后难以继续。
- 面向 marketplace 时必须考虑未知环境和依赖缺失。
- 若任务本质是长期知识能力，可能更适合 Skill 而不是 legacy slash command。

## 11. 原文归档说明

已归档目录：

```text
originals/anthropic/claude-plugins-official/plugins/plugin-dev/skills/command-development/
```

已保存：

```text
README.md
SKILL.md
examples/plugin-commands.md
examples/simple-commands.md
references/advanced-workflows.md
references/documentation-patterns.md
references/frontmatter-reference.md
references/interactive-commands.md
references/marketplace-considerations.md
references/plugin-features-reference.md
references/testing-strategies.md
.source-commit
```

## 12. 一句话方法论

把 slash command 当作一个小型、可复用、可测试、可分发的 agent workflow 产品：用 Markdown 写给 Claude 的执行指令，用 frontmatter 声明接口和权限，用参数/文件/Bash 注入上下文，用验证、文档和测试 gate 保证长期可靠。
