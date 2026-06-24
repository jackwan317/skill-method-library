# Skill Method Library

> **给新对话里的三代 / Agent 看的启动说明：先读本 README，再读 `docs/operating-workflow.md`，然后按 `catalog/by-task-type.md` 的备注状态继续执行。**

这个仓库用于系统研究优秀 AI Agent Skills / Plugins / Workflows。目标不是收集提示词，而是拆解优秀 Skill **如何完成任务**：触发条件、输入输出、步骤、工具/脚本/MCP/API、关键决策、验证 gate、失败恢复、可迁移模式。

## 0. 新对话快速恢复

如果你在新窗口接到大哥继续这个长期任务，请按下面顺序恢复上下文：

1. 仓库本地路径：`/home/ubuntu/skill-method-library`
2. GitHub 仓库：<https://github.com/jackwan317/skill-method-library>
3. 先读：`docs/operating-workflow.md`
4. 再读：`catalog/by-task-type.md`
5. 查找候选池里 `备注状态` 为 `新增候选` 或 `待研究` 的项目。
6. 不要下载/归档原文；新流程只输出方法论报告。
7. 每次只处理一个 Skill / Workflow，并用一个独立 sub agent 只读研究。
8. 一个 sub agent 结束后，马上写报告、更新 catalog、commit、push、验证远端；不要等整批结束。
9. 完成后把对应行的 `报告路径` 改为 `skills/<category>/<name>.md`，`备注状态` 改为 `已有报告`。
10. 下一项优先看 `backlog/context-brief.md` 和 `backlog/next-batches.md`，但最终以 `catalog/by-task-type.md` 的状态为准，避免重复。

## 1. 长线任务分成两部分

### A. 候选池发现：根据信息源查找优质 Skills

持续从这些来源找优质 Skill / Workflow，并加入候选池：

- OpenAI Skills / Codex Plugins
- Anthropic Claude Skills / Plugins
- OfficialSkills
- 高星 GitHub 仓库
- Claude Code / Codex / Cursor / MCP / Agent workflow 社区
- Microsoft、Cloudflare、Sentry、Trail of Bits、GitHub 等官方或权威来源
- 和大哥商业目标相关的产品、增长、自动化、商业分析方法库

入池时必须写清：

- 名称
- 来源链接
- 任务类型 / 用途
- 入选理由：官方/权威、stars/forks、社区热度、维护活跃、结构完整、验证强、可迁移价值
- 报告路径：未研究时写 `待生成`
- 备注状态：`新增候选` / `待研究` / `研究中` / `已有报告` / `跳过` / `需复查`

### B. 候选池执行：逐项用 Sub Agent 输出报告

从候选池选择一个未完成项目，用一个独立 sub agent 研究，然后把报告输出到 GitHub。

硬规则：

- **不用下载或归档原文**。历史 `originals/` 保留，但新研究项不再写入 `originals/`。
- 一个 Skill / Workflow = 一个独立 sub agent，避免上下文污染。
- Sub agent 只读研究，不运行目标 Skill，不修改上游仓库。
- 每个 sub agent 跑完后，立即把报告写入仓库并推送。
- 每完成一个节点，都更新 `catalog/by-task-type.md` 的 `报告路径` 和 `备注状态`，避免后续重复作业。

## 2. 单个 Skill 的标准执行流程

1. 检查 `catalog/by-task-type.md`，选择 `新增候选` 或 `待研究` 的项目。
2. 将该行状态改为 `研究中`。如果任务会持续较久，先提交并推送这个锁定状态。
3. 启动一个 sub agent，只给它这个项目的链接和固定报告 schema。
4. Sub agent 必须只读研究，输出中文 Markdown 报告。
5. 主会话把报告写到：`skills/<category>/<name>.md`
6. 更新 `catalog/by-task-type.md`：
   - `报告路径` → 报告文件路径
   - `备注状态` → `已有报告`
   - 必要时补充入选理由 / 质量证据
7. 更新 `backlog/context-brief.md` / `backlog/next-batches.md` 的进度。
8. `git add && git commit && git push`
9. 用 GitHub Contents API 或 raw URL 验证：
   - 报告文件远端存在
   - catalog 对应行已经是 `已有报告`
10. 再进入下一个项目。

## 3. 报告必须覆盖的内容

每个报告按 `templates/skill-research-template.md` 写，至少覆盖：

1. 为什么认为这个 Skill / Workflow 优秀
2. 它要完成的任务
3. 触发条件
4. 输入 / 输出
5. 完成任务的步骤流程
6. 调用的工具 / 脚本 / MCP / API / CLI / 外部服务
7. 关键决策点 / 分支
8. 验证方法 / 交付 gate
9. 可迁移到 Hermes Skills / SOP / 业务自动化的写法
10. 适合归类的任务类型
11. 风险与注意事项
12. 一句话方法论
13. 候选池状态更新说明

报告重点是“它怎么完成任务”，不是复述 README。

## 4. 仓库结构

```text
catalog/                    # 候选池 + 进度表，按任务类型归类
sources/                    # 来源扫描与扩展候选池
skills/<category>/<skill>.md # 每个 Skill / Workflow 一份方法论报告
docs/                       # 项目运行流程与规范
templates/                  # 报告模板
backlog/                    # 后续批次计划与上下文 brief
originals/                  # 历史归档区；新流程不再要求下载原文
```

## 5. 关键文件

- `docs/operating-workflow.md`：完整运行流程，最重要。
- `catalog/by-task-type.md`：候选池 + 进度表；新任务从这里选。
- `templates/skill-research-template.md`：报告模板。
- `backlog/context-brief.md`：压缩后的上下文 brief。
- `backlog/next-batches.md`：后续批次与当前进度。
- `sources/expanded-candidates-2026-06-24.md`：第二批 40+ 优质候选来源扫描。

## 6. 当前已完成的报告

已完成并在 catalog 中标记为 `已有报告` 的新流程相关报告：

- `skills/agent-automation/anthropic-hook-development.md`
- `skills/agent-automation/anthropic-command-development.md`
- `skills/agent-automation/anthropic-claude-automation-recommender.md`

历史已完成报告还包括：

- `skills/meta-skill-development/openai-plugin-creator.md`
- `skills/meta-skill-development/anthropic-skill-creator.md`
- `skills/frontend-ui/openai-frontend-app-builder.md`

## 7. 当前下一步建议

当前队列下一项通常是：

- Anthropic `knowledge-synthesis`

但开始前必须重新读取 `catalog/by-task-type.md`，确认它的 `备注状态` 仍不是 `已有报告` 或 `研究中`。

## 8. 给 Sub Agent 的固定提示要点

每次只传一个项目给 sub agent，并明确：

- 只读研究，不要修改任何文件。
- 不运行目标 Skill / Workflow。
- 不下载或归档原文。
- 读取上游 README、SKILL.md、docs、examples、scripts、references 等公开内容。
- 输出中文 Markdown。
- 不要泛泛复述；必须总结具体流程、工具、脚本、MCP、API、CLI、验证 gate、关键分支、可迁移方法。
- 如果找不到足够资料，要明确写“需复查”，不要编造。

## 9. 提交规范

每完成一个报告或一次候选池更新：

```bash
git status --short
git add <changed-files>
git commit -m "docs: study <skill-name> workflow"
git push origin main
```

验证远端：

- 用 GitHub Contents API 或 raw URL 检查报告文件。
- 检查 `catalog/by-task-type.md` 对应行是否已更新为 `已有报告`。

## 10. 绝对不要做

- 不要把多个 Skill 合在一个 sub agent 里研究。
- 不要等一批全部完成后才写仓库；跑完一个就输出一个。
- 不要下载/归档原文作为新流程必做项。
- 不要忘记更新 `catalog/by-task-type.md` 的状态。
- 不要只写 URL 列表；每个候选必须写为什么优秀。
- 不要编造 stars/forks、社区热度、API、脚本或流程；查不到就标 `需复查`。
