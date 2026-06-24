# Cloudflare Agents Skill 方法论研究

**元数据**

- **来源**：Cloudflare 官方 GitHub 仓库与官方文档
- **原始链接**：https://github.com/cloudflare/agents
- **本仓库报告路径**：`skills/agent-orchestration/cloudflare-agents-skill.md`
- **候选池备注状态**：`研究中` → `已有报告`
- **所属任务类型**：Edge-native AI Agents / Agent Orchestration / Durable Objects Agent Runtime
- **研究日期**：2026-06-24
- **研究方式**：只读研究；阅读 GitHub README、仓库元数据、官方 docs 目录中的 Agent、state、routing、WebSocket、callable、scheduling、durable execution、agent tools、sub-agents、workflows、observability、configuration 等文档；未安装依赖、未运行 SDK 或示例、未下载归档原文。
- **仓库概况**：Cloudflare 官方仓库 `cloudflare/agents`；GitHub API 查询显示约 5163 stars、604 forks，MIT License，默认分支 `main`。

## 0 为什么优秀

Cloudflare Agents 的核心价值不是“又一个 Agent SDK”，而是把 Agent 运行时直接建模为 Cloudflare Workers + Durable Objects 上的**可寻址、可休眠、带 SQLite 状态的边缘 Actor**。每个 Agent 实例可以对应一个用户、会话、房间、任务或项目；空闲时休眠，不持续占用计算资源；被 HTTP、WebSocket、RPC、alarm、email 等事件唤醒后继续处理。

它优秀的地方主要有四点：

1. **状态与执行位置合一**：Agent 本身就是 Durable Object，拥有独立 SQLite、生命周期、WebSocket 连接与调度能力，不必额外拼接外部数据库、队列和长连接服务。
2. **实时交互原生化**：`useAgent`、`AgentClient`、WebSocket lifecycle、`setState()` 广播和 `@callable()` RPC 让前端像调用本地对象一样调用边缘 Agent。
3. **中断恢复意识强**：文档明确区分普通内存、SQLite 持久状态、scheduled task、fiber checkpoint、resumable stream。长任务可用 `keepAlive()` 降低 eviction 风险，用 `runFiber()` + `stash()` 在重启后恢复。
4. **组合能力完整**：支持 sub-agents、agent tools、AIChatAgent、Think、MCP、Workflows、voice、email、observability 等，适合构建多 Agent、长流程、实时协作与人类审批系统。

## 1 任务

Cloudflare Agents 解决的任务是：在边缘网络上构建**长期存在但间歇运行**的 AI Agent。它不是让 Agent 作为传统服务器进程常驻，而是让 Agent 作为 Durable Object 实例存在：有名字、有状态、有路由、有调度、有连接；需要处理事件时启动，空闲时休眠。

典型任务包括：用户级或会话级 AI assistant；多人实时房间、游戏、协作工作区；长时间项目管理、提醒、定时检查；带工具调用和子 Agent 的研究、执行、审批流程；Webhook、email、push、voice 等事件驱动 Agent；需要断线续传、流式回复恢复、状态同步的 chat agent。

## 2 触发条件

Agent 可被多种事件唤醒：

- **HTTP request**：通过 `onRequest()` 处理 REST API、webhook、文件上传等。
- **WebSocket**：通过 `onConnect()`、`onMessage()`、`onClose()` 处理实时双向通信。
- **RPC / callable method**：浏览器通过 `@callable()` 暴露的方法进行 WebSocket RPC；Worker 或 Agent 内部可通过 Durable Object RPC、`getAgentByName()` 调用。
- **Scheduled alarm**：`schedule()`、`scheduleEvery()`、cron 表达式底层使用 Durable Object alarms 唤醒 Agent。
- **工作流事件**：Cloudflare Workflows 负责长流程和重试，Agent 负责状态、实时通知与入口。
- **外部通道**：文档提到 email、webhooks、push、voice 等可作为事件入口。

## 3 输入/输出

**输入**包括：

- URL 路由中的 agent class 和 instance name，例如 `/agents/{agent-name}/{instance-name}`；
- HTTP request body、headers、query；
- WebSocket 消息；
- `@callable()` 方法参数，要求可序列化；
- scheduled task 参数；
- workflow params 和 workflow event；
- LLM chat messages、tool call、MCP tool、client-side tool 结果。

**输出**包括：HTTP Response；WebSocket 消息或状态广播；`setState()` 后同步给所有连接客户端的状态；callable method 返回值或 stream；AIChatAgent 的 streaming response、持久化消息、resumable chunks；schedule/workflow 执行结果；observability event、日志、metrics；持久化到 SQLite 的业务数据、消息、fiber checkpoint、计划任务等。

## 4 步骤流程

可迁移的方法论流程如下：

1. **定义 Agent 边界**：先确定“一个 Agent 实例代表什么”：用户、会话、项目、文档、房间还是任务。Cloudflare Agents 的实例命名是架构核心，名称决定路由、状态隔离和生命周期。
2. **继承合适基类**：纯状态、实时和业务逻辑继承 `Agent`；Chat UI 且自定义 LLM loop 用 `AIChatAgent`；需要内建 reasoning loop、memory、tools、recovery 考虑 `Think`；多步骤后台流程用 Workflows 与 Agent 组合。
3. **配置 Durable Object 绑定和 SQLite migration**：在 `wrangler.jsonc` 中声明 `durable_objects.bindings`，并通过 `migrations.new_sqlite_classes` 启用 SQLite 存储。官方文档还强调 `nodejs_compat` compatibility flag 是 Agents 依赖项之一。
4. **实现路由入口**：Worker `fetch()` 中调用 `routeAgentRequest(request, env)`；未匹配时再走自定义业务路由。服务端内部调用特定实例时用 `getAgentByName()` 或 DO RPC。
5. **建模状态**：使用 `initialState` 定义轻量状态；通过 `this.state` 读取，通过 `setState()` 更新。`setState()` 会持久化 SQLite、广播给连接客户端，并触发 `onStateChanged()`。大数据不应塞进 state，应放 SQL 表，state 只保留摘要和 UI 所需字段。
6. **实现实时交互**：WebSocket lifecycle 负责连接、消息、关闭；React 端使用 `useAgent`，非 React 使用 `AgentClient`。`@callable()` 用于浏览器或外部客户端调用 Agent 方法；内部 Agent-to-Agent 调用优先用 DO RPC。
7. **加入 AI 与工具执行**：Chat 类任务在 `onChatMessage()` 中调用模型并返回 streaming response。Agent tools 可让父 chat agent 调用子 chat-capable agent，并把子运行时间线以 `agent-tool-event` 流给客户端。MCP 可作为工具来源或对外服务，但具体工具安全策略需另行设计。
8. **处理长任务与恢复**：短后台任务可用 queue；定时或周期任务用 schedule；超过普通请求生命周期、需要重试和多步骤编排时用 Cloudflare Workflows；属于 Agent 内部执行且需要 eviction 恢复时用 `runFiber()`，在关键点 `stash()` checkpoint，并在 `onFiberRecovered()` 恢复。
9. **验证与观测**：使用 diagnostics channel、Tail Workers、结构化事件观察 RPC、state、schedule、workflow、chat recovery、connect/disconnect 等生命周期事件。

## 5 工具/API/CLI/外部服务

核心 API/能力：

- `Agent`：基础 Durable Object Agent 类。
- `AIChatAgent`：消息持久化、streaming、resumable chat。
- `routeAgentRequest()`：Worker 入口路由。
- `getAgentByName()`：服务端获取特定 Agent 实例。
- `setState()` / `onStateChanged()`：状态持久化与同步。
- `onRequest()`、`onConnect()`、`onMessage()`、`onClose()`：HTTP 与 WebSocket lifecycle。
- `@callable()`：WebSocket RPC 方法暴露。
- `schedule()`、`scheduleEvery()`：延迟、指定时间、cron、interval 调度。
- `keepAlive()` / `keepAliveWhile()`：长操作期间维持活跃。
- `runFiber()` / `startFiber()` / `stash()` / `onFiberRecovered()`：可恢复执行。
- `subAgent()`、`listSubAgents()`、`deleteSubAgent()`：父子 Agent 组合。
- `runAgentTool()`：把 Agent 作为 AI SDK tool 或确定性工作流步骤调用。
- `AgentWorkflow`：与 Cloudflare Workflows 集成。
- `wrangler.jsonc`：声明 Durable Objects、migrations、AI binding、assets、workflows。
- 外部服务：Workers AI、Vercel AI SDK、MCP servers/clients、Email Service、Voice/STT/TTS、Web Push 等。

## 6 关键决策点

- **Agent 粒度**：一个用户一个 Agent，还是一个会话/任务/项目一个 Agent？粒度越细，隔离越好，但命名和索引更重要。
- **state vs SQL**：广播给客户端的轻量 UI 状态放 state；历史消息、大列表、审计记录放 SQLite。
- **callable vs HTTP vs RPC**：浏览器实时调用用 `@callable()`；公开 webhook/API 用 HTTP；Agent 内部或 Worker 内部用 DO RPC。
- **schedule vs workflow vs fiber**：定时唤醒用 schedule；多步骤、长时间、重试和审批用 Workflow；Agent 内部执行中断恢复用 fiber。
- **top-level agent vs sub-agent**：同一父实体下开放数量的会话、文档、子任务适合 sub-agents；需要作为独立公开入口则绑定为 top-level DO。
- **AIChatAgent vs Think**：需要自己控制 loop 用 AIChatAgent；需要更完整的 reasoning runtime、memory、tool orchestration 和 recovery 可研究 Think。
- **断线恢复需求**：普通 WebSocket 状态同步不等于 LLM turn 恢复；client reconnect 用 resumable streaming，DO eviction 需开启 chat recovery 或 fiber 机制。

## 7 验证方法 / 交付 Gate

落地时建议设置以下 Gate：

1. **配置 Gate**：`wrangler.jsonc` 有 `nodejs_compat`、Durable Object binding、SQLite migration；Agent 类已从 Worker entry point export。
2. **路由 Gate**：`routeAgentRequest()` 能正确匹配 agent class 与 instance name；未匹配请求不误吞。
3. **状态 Gate**：刷新、断连重连、DO 重启后 `this.state` 和 SQL 数据仍存在。
4. **实时 Gate**：多客户端连接同一实例时，`setState()` 能广播一致状态。
5. **RPC Gate**：`@callable()` 方法参数和返回值均可序列化；错误可被客户端处理。
6. **调度 Gate**：schedule 创建、执行、取消、重复调度、长 callback overlap 行为被验证。
7. **恢复 Gate**：模拟断线、刷新、eviction 或 redeploy 场景，确认 stream replay、fiber recovery 或 workflow resume 符合预期。
8. **安全 Gate**：路由前做认证授权；不要把敏感 instance name 直接暴露给不可信客户端；必要时关闭 identity on connect。
9. **观测 Gate**：订阅 diagnostics channel 或 Tail Workers，能看到 RPC、state、schedule、workflow、chat recovery 事件。
10. **成本 Gate**：确认空闲 Agent 可休眠；避免长期 keepAlive、无限 interval、过大 state 广播造成成本和性能问题。

## 8 可迁移到 Hermes Skills 的写法

可迁移到其他 Agent Runtime 的写法是：

- 把 Agent 当作**有身份的 Actor**，而非无状态函数；
- 用“实例名”作为状态隔离键和路由键；
- 把 UI 状态、业务数据、执行 checkpoint 分层存储；
- 所有长任务都显式定义恢复点，不依赖内存变量和 timer；
- 实时层只传状态增量或摘要，完整历史走数据库查询；
- 子 Agent 用于隔离长期子会话，Agent tools 用于单次父任务内的可视化委派；
- 调度和工作流分离：周期唤醒归 Agent，跨时长重试归 Workflow；
- 验证重点从“能跑一次”转为“断线、休眠、重启、重复提交后仍正确”。

## 9 适合归类的任务类型

- **一级分类**：Agent Orchestration / Edge-native Agent Runtime
- **二级分类**：Durable Object Actor、Stateful Agent、Realtime Agent、Long-running Agent
- **适用场景**：实时 AI 聊天、多人协作、长任务编排、子 Agent 委派、Webhook/Email 事件 Agent、边缘部署 AI 应用
- **不适合场景**：纯离线批处理、需要长时间连续 CPU 计算的任务、强依赖传统 VM 常驻进程的系统、完全不需要状态和实时能力的简单 API。

## 10 风险与注意事项

- **平台绑定风险**：架构深度依赖 Cloudflare Workers、Durable Objects、Wrangler、Workflows，迁移到其他云需要重写运行时层。
- **生命周期误解**：Agent “长期存在”不等于进程常驻；内存变量、timer、in-flight fetch 不可靠。
- **状态膨胀风险**：`setState()` 会广播给客户端，大对象或大数组会带来性能和成本问题。
- **恢复语义复杂**：resumable streaming、chat recovery、fiber、workflow 分别解决不同故障边界，混用时需明确责任。
- **装饰器配置风险**：`@callable()` 使用 TC39 decorators，文档提醒不要启用 TypeScript legacy `experimentalDecorators`，并需 Vite 插件支持。
- **安全默认需复查**：认证、授权、租户隔离、工具权限、MCP 暴露面需要业务侧设计；部分 TODO 文档如通用 “Securing your Agents” 未在上游资料中找到完整页面，需复查。
- **实验功能稳定性**：Think、sessions、workspace、codemode 等部分能力在文档中标注 experimental 或位于 experimental 目录，生产采用需复查稳定性。

## 11 一句话方法论

把每个 Agent 设计成边缘上的可寻址 Durable Object Actor：用 SQLite 保存状态和 checkpoint，用 WebSocket/RPC 做实时交互，用 schedule/fiber/workflow 承接时间和故障边界，用 sub-agent/agent tools 组合复杂任务。

## 12 候选池状态更新说明

- 报告写入路径：`skills/agent-orchestration/cloudflare-agents-skill.md`
- `catalog/by-task-type.md` 对应行是否已改为 `已有报告`：应在本报告落盘后更新。
- 是否需要补充/修正入选理由：建议补充“以 Durable Objects Actor + SQLite state + WebSocket/RPC + schedule/fiber/workflow 组合边缘 agent 的状态、实时、调度和恢复能力”。
- 后续可继续深挖的问题：实作阶段可补充一个最小 Agent demo，重点验证 DO 状态持久化、WebSocket 重连、scheduled task、workflow/fiber 恢复和认证边界。
