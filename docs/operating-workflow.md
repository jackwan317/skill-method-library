# Skill Method Library 运行流程

本项目分成两个长期工作流：

## A. 候选池发现：根据信息源查找优质 Skills

目标：持续从官方、社区、高星仓库、热门索引中发现值得学习的 Skills / Plugins / Workflows，并加入候选池。

### 信息源

优先扫描：

1. 官方来源：OpenAI Skills / Codex Plugins、Anthropic Claude Skills / Plugins、Microsoft、Cloudflare、Sentry、Trail of Bits、GitHub 等。
2. 高星 GitHub 仓库：优先 stars/forks 高、维护活跃、近期热门、被多个 awesome list 收录的项目。
3. 社区/共享平台：OfficialSkills、awesome-claude-code、awesome-mcp-servers、各类 agent skill marketplace。
4. 与大哥目标强相关的商业/产品/自动化方法库。

### 入池字段

加入 `catalog/by-task-type.md` 或 `sources/*.md` 时，每个候选至少记录：

- 名称
- 来源链接
- 任务类型 / 用途
- 入选理由：官方/权威、stars/forks、社区热度、维护活跃度、结构完整性、验证强度、可迁移价值
- 报告路径：未研究时写 `待生成`
- 备注状态

### 备注状态规范

候选池状态统一使用：

- `新增候选`：刚入池，尚未排期。
- `待研究`：已确认值得研究，等待 sub agent。
- `研究中`：当前正在由一个 sub agent 研究。
- `已有报告`：报告已写入 `skills/<category>/<name>.md` 并推送到 GitHub。
- `跳过`：质量不足、链接失效、与目标不相关，暂不研究。
- `需复查`：信息不完整、来源变化、质量信号待确认。

## B. 候选池执行：逐项用 Sub Agent 输出报告到 GitHub

目标：从候选池选择一个未完成项目，用一个独立 sub agent 只读研究，并把报告直接写入 GitHub 仓库。

### 硬规则

1. **不用下载或归档原文**。只阅读上游内容并输出方法论报告。
2. **一个 Skill / Workflow = 一个独立 sub agent**，避免上下文污染。
3. Sub agent 只读研究：不运行目标 Skill、不修改目标上游仓库、不向本仓库直接推送。
4. 主会话负责把报告落盘、更新候选池备注状态、commit、push、远端验证。
5. **跑完一个就输出一个**：每个 sub agent 完成后，立即写报告、更新状态、提交推送，不等待整批完成。
6. 每完成一个节点，都必须更新 `catalog/by-task-type.md` 的报告路径和备注状态，避免下一个 sub agent 重复作业。

### 单个候选执行步骤

1. 从 `catalog/by-task-type.md` 选择状态为 `待研究` 或 `新增候选` 的项目。
2. 先把该行备注状态改为 `研究中`。如果后续可能启动新的 sub agent 或任务会持续较久，先提交并推送这个状态，确保仓库里的候选池就是最新锁定状态。
3. 启动一个 sub agent，只传该项目的链接、任务类型、固定输出 schema。
4. Sub agent 输出中文方法论报告，必须覆盖：
   - 为什么优秀 / 入选理由
   - 任务目标
   - 触发条件
   - 输入 / 输出
   - 步骤流程
   - 工具 / 脚本 / MCP / API / CLI / 外部服务
   - 关键决策点 / 分支
   - 验证方法 / 交付 gate
   - 可迁移到 Hermes Skills / 业务自动化的写法
   - 适合归类的任务类型
   - 风险与注意事项
   - 一句话方法论
5. 主会话将报告写入 `skills/<category>/<name>.md`。
6. 更新 `catalog/by-task-type.md`：
   - `报告路径` 改为报告文件路径。
   - `备注状态` 改为 `已有报告`。
   - 入选理由可补充 sub agent 发现的质量证据。
7. 更新 `backlog/context-brief.md` 和 `backlog/next-batches.md` 的进度。
8. `git add && git commit && git push`。
9. 用 GitHub Contents API 或 raw URL 验证：
   - 报告文件远端存在。
   - `catalog/by-task-type.md` 对应行状态为 `已有报告`。
10. 再进入下一个候选。

### Sub agent 提示词要点

Sub agent 必须被明确告知：

- 只读研究，不要修改任何文件。
- 不需要运行目标 Skill。
- 不需要下载或归档原文。
- 必须给出具体流程，不要泛泛复述 README。
- 如果发现工具、脚本、MCP、API、配置字段、命令参数、验证 gate，要明确列出。
- 输出中文 Markdown，控制在可落盘为单篇报告的长度。

## 与旧流程的差异

旧流程要求保存原文到 `originals/`。从本流程生效后，**新研究项不再下载原文**，只保存方法论报告与候选池状态。历史上已经归档的 `originals/` 保留不删，但不再作为后续必做项。
