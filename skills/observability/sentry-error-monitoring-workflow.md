# Sentry Error Monitoring workflow 方法论研究

**元数据**

- **来源**：Sentry 官方文档、Sentry GitHub 主仓 README
- **原始链接**：
  - https://docs.sentry.io/
  - https://docs.sentry.io/product/issues/
  - https://docs.sentry.io/product/issues/issue-details/
  - https://docs.sentry.io/product/issues/issue-details/error-issues/
  - https://docs.sentry.io/product/issues/issue-details/breadcrumbs/
  - https://docs.sentry.io/product/issues/states-triage/
  - https://docs.sentry.io/product/issues/issue-priority/
  - https://docs.sentry.io/product/issues/suspect-commits/
  - https://docs.sentry.io/product/issues/issue-owners/
  - https://docs.sentry.io/product/releases/
  - https://docs.sentry.io/product/releases/setup/release-automation/
  - https://docs.sentry.io/product/performance/
  - https://docs.sentry.io/product/performance/trace-view/
  - https://docs.sentry.io/product/alerts/
  - https://docs.sentry.io/api/events/
  - https://docs.sentry.io/cli/releases/
  - https://github.com/getsentry/sentry
- **本仓库报告路径**：`skills/observability/sentry-error-monitoring-workflow.md`
- **候选池备注状态**：`研究中` → `已有报告`
- **所属任务类型**：可观测性 / 错误监控 / Issue Triage / 生产反馈闭环
- **研究日期**：2026-06-24
- **研究方式**：只读阅读公开 Sentry docs、GitHub README、API 与 CLI 文档；未运行 Sentry，未连接账号，未安装依赖，未下载或归档原文。

## 0 为什么优秀

Sentry 的优秀不在于“收集错误”本身，而在于把一次生产异常转换成可行动的修复工作单元：事件被 SDK 捕获后，按 fingerprint 聚合为 issue；issue 页面把 stack trace、breadcrumbs、tags、用户影响、首次/最后出现时间、release、commit、owner、外部工单和活动历史放在同一上下文；triage 状态、优先级、告警和自动归属把“谁该看、何时看、看什么”前置；release 与 commit 元数据又把线上现象反向映射到代码变更；修复后通过 resolved、regressed、escalating 等状态继续验证闭环。它的方法论是：**不要只给错误日志，而要给可以分派、定位、修复、验证的生产事实包**。

## 1 任务

Sentry Error Monitoring workflow 的核心任务是：在生产环境中捕获错误、性能问题或相关事件，将大量事件压缩为可管理的 issue，并帮助团队完成从发现、判断影响、定位代码、分派负责人、创建告警/工单、修复发布，到验证关闭的闭环。

具体包括：

1. 发现错误：自动捕获 uncaught exception、unhandled rejection、平台相关错误和手动上报事件。
2. 聚合问题：基于 fingerprint，尤其是 error issue 的 stack trace，将相似事件归为同一 issue。
3. 补充上下文：保留 breadcrumbs、tags、request/user/environment/release 等维度。
4. 决策优先级：根据错误级别、影响用户数、事件频率、是否回归、是否升级等排序。
5. 路由负责人：通过 ownership rules、CODEOWNERS、suspect commits 或手动 assignment 分派。
6. 验证修复：通过 release、resolved/regressed 状态与新事件监测判断是否真的关闭。

## 2 触发条件

Sentry workflow 的触发来自几类信号：

- **新错误事件**：SDK 捕获异常并发送到 Sentry，形成新 issue 或加入已有 issue。
- **事件量变化**：已有 issue 的事件量超过预测值时进入 escalating。
- **回归**：已 resolved 的 issue 再次出现时进入 regressed。
- **发布变更**：新 release 上报后，可识别新问题、回归、已修复问题和 release health。
- **规则匹配**：Alert 规则在 issue 创建、升级、分派、从 resolved 回到 unresolved 等状态变化时触发。
- **人工操作**：开发者手动 resolve、archive、assign、link GitHub/Jira issue 或调整 priority。

## 3 输入/输出

**输入**

- SDK 上报的错误事件、异常、unhandled rejection、日志级别。
- stack trace、source map 或平台对应的源码映射文件。
- breadcrumbs：错误前发生的结构化事件轨迹。
- tags、environment、user、request、device、browser 等上下文。
- release 名称、commit metadata、repository integration、code mappings。
- ownership rules、CODEOWNERS、团队/用户权限。
- alert 条件、过滤器、通知渠道、webhook 或 Jira/GitHub 集成。

**输出**

- 聚合后的 issue，包括状态、优先级、影响用户数、事件趋势。
- Issue Details：stack trace、breadcrumbs、tags 分布、事件列表、活动历史。
- suspect commit、commit author、相关 pull request、建议 assignee。
- 自动或手动 assignment、通知、Slack 消息、Jira/GitHub 工单、webhook 调用。
- resolved、archived、regressed、escalating 等生命周期状态。
- release 维度的回归与修复判断。

## 4 步骤流程

1. **采集**：应用集成 Sentry SDK，自动或手动捕获错误；可配置 release、environment、user、tags、breadcrumbs 和 tracing。
2. **归组**：Sentry 将相似事件按 fingerprint 聚合为 issue。error issue 主要依赖 stack trace；Issue Details 中可查看 grouping 信息。
3. **初筛**：Issues 页面按 unresolved、for review、regressed、archived、escalating 等视图组织问题，并支持按 browser、device、impacted users、handled/unhandled 等过滤。
4. **判断影响**：查看事件数量、用户数量、首次/最后出现时间、24 小时/30 天发生频率、tag facet 分布和环境范围。
5. **定位上下文**：进入 Issue Details，阅读 stack trace，结合 breadcrumbs 判断错误前的操作轨迹；查看相关 replay、attachments、feedback（如有）。
6. **关联代码**：通过 release、commit metadata、repository integration、source map/code mapping 识别 suspect commits、文件路径、作者和 PR。
7. **分派处理**：根据 ownership rules、CODEOWNERS、suspect assignee 或人工判断分派给用户/团队；必要时创建 Jira/GitHub issue。
8. **告警联动**：Alert 根据 issue 状态触发通知、工单、webhook 或其他集成，避免只依赖人工巡检。
9. **修复发布**：开发者修复并发布新 release；Sentry 用 release 信息辅助判断新问题、回归与修复状态。
10. **验证关闭**：人工 resolve 或 archive；若 resolved issue 再次出现则 regressed，若 archived issue 事件量显著上升则 escalating，重新进入 triage。

## 5 工具/API/CLI/外部服务

- **Sentry SDK**：采集异常、breadcrumbs、tags、release、environment、tracing 数据。
- **Sentry Issues / Issue Details UI**：执行 triage、定位、分派、resolve/archive。
- **Sentry Alerts**：按预定义规则发送通知、创建 ticket、调用 webhook。
- **Sentry Releases**：上报 release，关联 commit，识别新问题、回归和修复。
- **sentry-cli releases**：创建、编辑、删除、finalize release，上传 release artifacts；finalize 时间影响“next release”和自动关联 commit 的基准。
- **Repository integrations**：GitHub、GitLab 等，用于 suspect commits、PR、CODEOWNERS、code mappings。
- **外部服务**：Slack、Jira、GitHub issue、webhook 等。
- **API**：官方 API 提供 Events & Issues 能力，包括 list project issues、retrieve issue、update issue、bulk mutate issues、list issue events、retrieve event 等。具体自动化字段和鉴权细节需按 API 文档复查。

## 6 关键决策点

- **是否是可行动问题**：看 priority、level、handled/unhandled、用户影响、发生频率和环境。
- **新问题还是已有问题**：由 grouping/fingerprint 决定；必要时查看 grouping information。
- **立即修还是归档**：高影响、高频、回归、升级优先处理；低影响或噪声可 archive。
- **分派给谁**：优先使用 ownership rules/CODEOWNERS/suspect commits；若已有人工 assignment，后续自动分派会关闭。
- **是否告警**：对新 issue、regression、escalation、高严重级别和关键环境设置 alert；避免对低价值噪声过度通知。
- **是否已修复**：不能只看代码合并，应观察 release 后是否仍有新事件；回归会自动重新暴露。

## 7 验证方法 / 交付 Gate

可迁移为以下 Gate：

1. **采集完整性 Gate**：事件必须包含 stack trace、environment、release、必要 tags；前端压缩代码需 source map。
2. **可归组 Gate**：同类事件应聚合，误聚合/误拆分需检查 fingerprint/grouping。
3. **上下文 Gate**：Issue 必须能回答“谁受影响、何时发生、在哪个版本、错误前发生了什么”。
4. **归属 Gate**：高优先级 issue 必须有 owner 或 team；未归属不得进入修复队列。
5. **通知 Gate**：告警必须有明确触发条件、过滤范围和动作。
6. **修复 Gate**：修复必须关联 release/commit/PR。
7. **关闭 Gate**：关闭后继续观察；resolved 后再出现即 regressed，archived 后显著增量即 escalating。

## 8 可迁移到 Hermes Skills 的写法

迁移到 agent workflow 时，可把每个线上异常包装为结构化任务对象：

```yaml
incident_issue:
  title: error_message
  fingerprint: stacktrace_based_group_key
  status: new|ongoing|escalating|regressed|archived|resolved
  priority: high|medium|low
  evidence:
    stack_trace: required
    breadcrumbs: optional_but_preferred
    tags: environment/user/browser/device/release
    impact: event_count/user_count/time_window
  code_context:
    release: version
    suspect_commits: commits
    files: stacktrace_files
    owners: codeowners_or_rules
  actions:
    assignee: user_or_team
    alert: notification_or_ticket
    fix_pr: link
  verification:
    resolved_in_release: version
    regression_watch: enabled
```

Agent 不应只总结报错，而应先归组、补上下文、判断影响、找 owner、提出修复入口，并在修复后继续观察是否回归。

## 9 适合归类的任务类型

- **主分类**：Observability / Error Monitoring
- **工作流分类**：Issue triage、Incident debugging、Release verification
- **数据类型**：错误事件、性能 trace、breadcrumbs、release/commit metadata
- **自动化强度**：半自动；采集、聚合、告警、归属可自动，最终修复与关闭需人工或 agent 决策
- **适合迁移对象**：生产错误处理、CI/CD 发布验证、代码修复 agent、on-call 辅助系统

## 10 风险与注意事项

- **噪声风险**：低价值异常过多会稀释注意力，需要 archive、priority、alert filter。
- **归组风险**：fingerprint 不准会导致不同 bug 混合或同一 bug 拆散。
- **源码映射风险**：缺少 source map/code mapping 时，suspect commits 与源码定位能力下降。
- **归属风险**：ownership rules/CODEOWNERS 过期会误派。
- **隐私风险**：breadcrumbs、tags、request/user 信息可能包含敏感数据，需脱敏。
- **验证风险**：手动 resolve 不等于真实修复，必须结合 release 后事件观察。
- **资料缺口**：部分 API 字段、具体 escalating 算法细节、不同语言 SDK 默认采集差异未在本次上游资料摘读中完整展开，需复查对应专项文档。

## 11 一句话方法论

Sentry 的方法论是：**把生产错误从“日志事件”升级为带上下文、影响面、代码归因、责任人、告警动作和发布验证的可闭环 issue。**

## 12 候选池状态更新说明

- 报告写入路径：`skills/observability/sentry-error-monitoring-workflow.md`
- `catalog/by-task-type.md` 对应行是否已改为 `已有报告`：应在本报告落盘后更新。
- 是否需要补充/修正入选理由：建议补充“把错误事件按 fingerprint 聚合为带 stack trace、breadcrumbs、release/commit、owner、priority、alert 与 regressed/escalating 状态的可闭环 issue”。
- 后续可继续深挖的问题：Sentry API 字段细节、不同 SDK 默认采集差异、issue grouping/fingerprint 定制、告警噪声治理和与 GitHub/Jira 的自动化修复闭环。
