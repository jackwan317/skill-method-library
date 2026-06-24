# Anthropic `hook-development` 方法论研究

- 来源：Anthropic 官方 `claude-plugins-official`
- 原始链接：https://github.com/anthropics/claude-plugins-official/tree/main/plugins/plugin-dev/skills/hook-development
- 本仓库原文路径：`originals/anthropic/claude-plugins-official/plugins/plugin-dev/skills/hook-development/`
- 所属任务类型：Agent 工具调用治理 / 事件驱动自动化 / Claude Code Plugin Hooks
- 研究方式：独立 sub agent 只读研究

## 0. 为什么认为这个 Skill 优秀

- **官方/权威来源**：来自 Anthropic 官方 `claude-plugins-official` 仓库，直接面向 Claude Code Plugin 的 hooks 机制，是一手实践而不是第三方猜测。
- **社区质量信号强**：调研时官方仓库约 **30,987 stars / 3,371 forks**，Apache-2.0 许可，持续更新。
- **结构完整**：不仅有 `SKILL.md`，还包含 `references/patterns.md`、`references/advanced.md`、`references/migration.md`、`examples/` 和 `scripts/`。
- **流程可执行**：它不只讲概念，还提供 schema validate、hook lint、单 hook test 的工具脚本。
- **可迁移价值高**：虽然服务于 Claude Code hooks，但它的“生命周期事件 + 策略挂点 + 验证 gate”模型，可以迁移到 Hermes 的工具治理、长线自动化、MCP 风险控制、任务完成前验收等场景。

## 1. 它要完成的任务

`hook-development` 指导开发者创建、配置、测试、迁移和维护 Claude Code plugin hooks。它的目标是让 Claude Code 在关键生命周期节点自动执行策略，而不是只被动响应用户请求。

典型任务包括：

- 工具调用前校验：阻止危险 Bash 命令、敏感文件写入、MCP 删除操作。
- 工具调用后分析：检查编辑结果、漏洞、语法风险或破坏性变更。
- 停止前验收：检查 agent 是否运行测试、构建成功、完成用户请求。
- 会话开始加载上下文：识别项目类型、设置环境变量、加载项目配置。
- 用户 prompt 提交前治理：补上下文、阻断不合规请求。
- 会话结束、压缩前、通知时做日志、审计、上下文保存和清理。
- 把旧式 command hook 迁移为 prompt-based hook。
- 对 hook 配置和脚本做 schema 校验、lint、单元测试和集成验证。

## 2. 触发条件

Skill frontmatter 的触发语义覆盖两类表达：

### 用户直接提到 hooks 机制

- `create a hook`
- `add a PreToolUse hook`
- `PostToolUse`
- `Stop hook`
- `SubagentStop`
- `SessionStart`
- `SessionEnd`
- `UserPromptSubmit`
- `PreCompact`
- `Notification`
- `implement prompt-based hooks`
- `${CLAUDE_PLUGIN_ROOT}`

### 用户只表达目标

- “阻止危险命令”
- “每次启动时加载项目配置”
- “工具调用前验证权限”
- “任务结束前检查测试是否运行”
- “给 Claude Code 加自动化安全守门”
- “拦截 MCP 删除操作”

这个触发设计值得学习：它既覆盖专业术语，也覆盖用户的自然目标描述。

## 3. 输入 / 输出

### Hook 输入

所有 hooks 通过 stdin 接收 JSON，公共字段包括：

```json
{
  "session_id": "abc123",
  "transcript_path": "/path/to/transcript.txt",
  "cwd": "/current/working/dir",
  "permission_mode": "ask|allow",
  "hook_event_name": "PreToolUse"
}
```

事件特定字段：

- `PreToolUse` / `PostToolUse`：`tool_name`、`tool_input`、`tool_result`
- `UserPromptSubmit`：`user_prompt`
- `Stop` / `SubagentStop`：`reason`

Prompt hooks 中可用变量包括：

- `$TOOL_INPUT`
- `$TOOL_RESULT`
- `$USER_PROMPT`

### Hook 输出

通用 JSON 输出：

```json
{
  "continue": true,
  "suppressOutput": false,
  "systemMessage": "Message for Claude"
}
```

字段含义：

- `continue`：是否继续处理，默认 true。
- `suppressOutput`：是否隐藏 transcript 输出，默认 false。
- `systemMessage`：给 Claude 的系统消息。

`PreToolUse` 特定输出：

```json
{
  "hookSpecificOutput": {
    "permissionDecision": "allow|deny|ask",
    "updatedInput": {"field": "modified_value"}
  },
  "systemMessage": "Explanation for Claude"
}
```

`Stop` / `SubagentStop` 决策输出：

```json
{
  "decision": "approve|block",
  "reason": "Explanation",
  "systemMessage": "Additional context"
}
```

退出码语义：

- `0`：成功，stdout 展示在 transcript。
- `2`：阻断错误，stderr 反馈给 Claude。
- 其他：非阻断错误。

## 4. 完成任务的步骤流程

### Step 1：识别事件点

先判断需求属于哪个生命周期事件：

| 目标 | 推荐事件 |
|---|---|
| 阻止危险操作 | `PreToolUse` |
| 分析工具结果 | `PostToolUse` |
| 防止过早结束 | `Stop` / `SubagentStop` |
| 初始化项目上下文 | `SessionStart` |
| 用户输入治理 | `UserPromptSubmit` |
| 保存压缩前上下文 | `PreCompact` |
| 审计通知 | `Notification` |
| 结束清理 | `SessionEnd` |

### Step 2：选择 hook 类型

- `prompt`：适合复杂语义判断、上下文推理、边界情况多的策略。
- `command`：适合确定性检查、文件系统操作、外部工具集成、性能敏感场景。
- `hybrid`：command 做 quick check，prompt 做 deep analysis。

### Step 3：编写配置

插件中放在：

```text
hooks/hooks.json
```

Plugin hooks 使用 wrapper：

```json
{
  "description": "optional",
  "hooks": {
    "PreToolUse": []
  }
}
```

用户 `.claude/settings.json` 中则事件直接在顶层，无 wrapper。

### Step 4：设置 matcher

支持精确匹配、多工具、通配和正则：

```json
"Write"
"Read|Write|Edit"
"*"
"mcp__.*__delete.*"
"mcp__plugin_asana_.*"
```

### Step 5：编写 prompt 或脚本

Prompt hook 示例：

```json
{
  "type": "prompt",
  "prompt": "Evaluate if this tool use is appropriate: $TOOL_INPUT",
  "timeout": 30
}
```

Command hook 示例：

```json
{
  "type": "command",
  "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh",
  "timeout": 60
}
```

### Step 6：使用可移植路径

命令中优先使用 `${CLAUDE_PLUGIN_ROOT}`，不要硬编码绝对路径：

```json
{
  "type": "command",
  "command": "bash ${CLAUDE_PLUGIN_ROOT}/scripts/validate.sh"
}
```

### Step 7：配置 timeout

- command hooks 默认 60 秒。
- prompt hooks 默认 30 秒。
- 复杂 prompt 通常 15-30 秒。
- 命令检查应尽量短。

### Step 8：验证配置和脚本

使用：

- `validate-hook-schema.sh`
- `hook-linter.sh`
- `test-hook.sh`
- `claude --debug`

### Step 9：重启 Claude Code 测试

hooks 在 session start 时加载。修改 `hooks.json` 或脚本后不能热更新，必须重启 Claude Code。

### Step 10：文档化

在插件 README 中说明：

- hook 目的
- 触发条件
- 开关机制
- 失败行为
- 调试方法

## 5. 调用的工具 / 脚本 / MCP / API

### Claude Code hooks 事件

| Event | 时机 | 用途 |
|---|---|---|
| `PreToolUse` | 工具执行前 | 校验、拒绝、要求确认、修改输入 |
| `PostToolUse` | 工具执行后 | 分析结果、反馈、日志 |
| `UserPromptSubmit` | 用户提交 prompt 时 | 补上下文、验证、阻断 |
| `Stop` | 主 agent 准备停止 | 完成度 gate |
| `SubagentStop` | 子 agent 准备停止 | 子任务完成度 gate |
| `SessionStart` | 会话开始 | 加载上下文、设置环境 |
| `SessionEnd` | 会话结束 | 清理、日志、状态保存 |
| `PreCompact` | 上下文压缩前 | 保存关键上下文 |
| `Notification` | Claude 通知时 | 审计、外部联动 |

### 环境变量

- `$CLAUDE_PROJECT_DIR`：项目根目录。
- `$CLAUDE_PLUGIN_ROOT`：插件目录，推荐用于所有插件相对路径。
- `$CLAUDE_ENV_FILE`：`SessionStart` 中用于持久化环境变量。
- `$CLAUDE_CODE_REMOTE`：远程上下文标记。

### 示例脚本

- `examples/load-context.sh`  
  `SessionStart` 示例，检测 `package.json`、`Cargo.toml`、`go.mod`、`pyproject.toml`、`pom.xml`、`build.gradle`，设置 `PROJECT_TYPE`、`USES_TYPESCRIPT`、`BUILD_SYSTEM`、`HAS_CI`。

- `examples/validate-bash.sh`  
  `PreToolUse` 示例。读取 `.tool_input.command`，快速允许 `ls/pwd/echo/date/whoami`，拒绝 `rm -rf`、`rm -fr`、`dd if=`、`mkfs`、`> /dev/`，对 `sudo`、`su` 返回 `ask`。

- `examples/validate-write.sh`  
  检查 `.tool_input.file_path`，拒绝 `..` 路径穿越、`/etc`、`/sys`、`/usr` 系统路径，对 `.env`、`secret`、`credentials` 返回 `ask`。

### 工具脚本

- `scripts/validate-hook-schema.sh`  
  校验 JSON 语法、事件名、`matcher`、`hooks` 数组、`type`、`command/prompt` 字段、timeout、硬编码路径、prompt hook 事件兼容性。

- `scripts/test-hook.sh`  
  支持 `--create-sample <event-type>` 生成测试输入；设置 `CLAUDE_PROJECT_DIR`、`CLAUDE_PLUGIN_ROOT`、`CLAUDE_ENV_FILE`；执行 hook，显示退出码、耗时、输出 JSON、env file 内容。

- `scripts/hook-linter.sh`  
  检查 shebang、可执行权限、`set -euo pipefail`、是否读 stdin、是否用 `jq`、变量引用、硬编码路径、退出码、stderr、长运行风险、输入校验。

### MCP 相关

该 Skill 不绑定某个固定 MCP API，但给出治理 MCP 工具的 matcher 模式，例如：

```json
"matcher": "mcp__.*__delete.*"
```

这可用于拦截所有 MCP 删除类工具。

## 6. 关键决策点

### 6.1 prompt hook 还是 command hook？

| 情况 | 选择 |
|---|---|
| 语义判断、上下文推理、意图分析、安全解释 | prompt hook |
| 确定性、低延迟、外部工具、文件检查、正则黑名单 | command hook |
| 既要快速拦截又要深度推理 | hybrid |

### 6.2 选择哪个事件？

- 阻止危险行为：`PreToolUse`
- 发现结果问题：`PostToolUse`
- 防止过早结束：`Stop` / `SubagentStop`
- 初始化上下文：`SessionStart`
- 用户输入治理：`UserPromptSubmit`
- 审计通知：`Notification`
- 压缩前保留信息：`PreCompact`

### 6.3 allow / deny / ask？

`PreToolUse` 可返回：

- `allow`
- `deny`
- `ask`

例如危险删除 `deny`，写敏感文件 `ask`，普通读写 `allow`。

### 6.4 常驻启用还是临时启用？

常见模式：

- flag file：存在 `.enable-strict-validation` 时启用。
- config-driven：读取 `.claude/plugin-config.json` 中的 `strictMode` 等字段。

### 6.5 是否依赖 hook 顺序？

官方明确指出所有匹配 hooks 并行运行，不保证顺序。因此不能设计成 Hook A 输出被 Hook B 立即读取的强依赖链。若需要跨事件状态，应使用临时文件、session ID、事务化写入，并考虑并发。

## 7. 验证方法 / 交付 Gate

该 Skill 的交付 gate 明确：

1. **JSON 配置有效**：用 `jq` 或 `validate-hook-schema.sh` 检查。
2. **事件名合法**：只能使用支持的 Claude Code hook events。
3. **hook group 完整**：每组必须有 `matcher` 和 `hooks` 数组。
4. **hook 类型合法**：只能是 `command` 或 `prompt`。
5. **类型字段完整**：`command` 必须有 `command`；`prompt` 必须有 `prompt`。
6. **timeout 合理**：提示过高如 >600 秒、过低如 <5 秒。
7. **脚本 lint 通过**：shebang、`set -euo pipefail`、stdin、`jq`、变量引用、stderr、退出码。
8. **单 hook 可测试**：用 `test-hook.sh --create-sample PreToolUse` 生成输入并执行脚本。
9. **Claude Code debug 验证**：用 `claude --debug` 查看 hook 注册、执行日志、输入输出 JSON、耗时。
10. **重启验证**：修改配置或脚本后必须重启 Claude Code。

核心标准：**配置能加载、脚本能单测、hook 能在 debug session 实际触发、阻断/允许行为符合预期。**

## 8. 可迁移到 Hermes Skills / 长线自动化的写法

最值得迁移的是“事件驱动治理模型”：

1. **触发语义写清楚**  
   Skill 描述不仅写“hooks 开发”，还列出用户可能说的目标：阻止危险命令、会话开始加载上下文、完成前检查测试等。

2. **采用主文档 + references + examples + scripts**

```text
SKILL.md                 # 主流程与决策树
references/patterns.md   # 常用自动化模式
references/advanced.md   # 跨事件工作流、缓存、审计
examples/                # 最小可运行样例
scripts/                 # validate/lint/test 工具
```

3. **把完成标准写进 Skill**  
   每次生成自动化规则后必须 schema validate、lint、单测、集成验证、文档化。

4. **在 Hermes 中引入类似 gate**

可迁移为：

- 用户输入前置分类。
- 工具调用前权限检查。
- 工具调用后结果审计。
- 任务结束前验收。
- cron 中的健康检查。
- MCP 工具危险操作二次确认。

5. **使用可移植路径和环境变量**  
   Claude 用 `${CLAUDE_PLUGIN_ROOT}`；Hermes Skills 也应避免硬编码 profile、workspace、插件路径。

6. **保留 prompt + command 混合模式**  
   LLM 管语义，脚本管确定性、速度和外部系统。

## 9. 适合归类的任务类型

- Agent 工具调用治理
- 安全策略自动化
- Claude Code plugin 开发
- 事件驱动自动化
- DevEx / 开发流程增强
- CI-like completion gate
- MCP 工具风险控制
- Prompt-based policy enforcement
- 会话生命周期管理
- 脚本化验证与 lint 工具链
- 从规则脚本到 LLM 策略的迁移方法论

## 10. 风险与注意事项

- Hooks 并行运行，不保证顺序，不能假设前一个 hook 的输出被后一个 hook 消费。
- Prompt hooks 适合语义判断，但成本和延迟更高，需要 timeout 控制。
- Command hooks 速度快，但要严格处理 stdin、JSON、退出码和 stderr。
- 拦截型 hooks 容易误伤正常工作，危险操作应区分 `deny` 与 `ask`。
- 修改 hooks 后需要重启 Claude Code，不能假设热更新。
- 路径必须用 `${CLAUDE_PLUGIN_ROOT}`，否则插件迁移后容易失效。
- 安全 hooks 应优先防止破坏性写入、系统路径修改、敏感文件泄露和 MCP 删除类操作。

## 11. 原文归档说明

已归档目录：

```text
originals/anthropic/claude-plugins-official/plugins/plugin-dev/skills/hook-development/
```

已保存：

```text
SKILL.md
references/patterns.md
references/advanced.md
references/migration.md
examples/load-context.sh
examples/validate-bash.sh
examples/validate-write.sh
scripts/README.md
scripts/hook-linter.sh
scripts/test-hook.sh
scripts/validate-hook-schema.sh
.source-commit
```

## 12. 一句话方法论

把 agent 的关键生命周期事件变成可配置、可验证、可组合的策略挂点：简单确定性逻辑用 command hook，复杂语义判断用 prompt hook，并用 schema、lint、单测和 debug session 把自动化从“提示词建议”提升为“可交付的治理机制”。
