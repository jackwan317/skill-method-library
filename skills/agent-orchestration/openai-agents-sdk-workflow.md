# OpenAI Agents SDK workflow 方法论研究

**来源**：OpenAI 官方 GitHub / 官方文档  
**原始链接**：https://github.com/openai/openai-agents-python  
**本仓库报告路径**：`skills/agent-orchestration/openai-agents-sdk-workflow.md`  
**候选池备注状态**：`研究中` → `已有报告`  
**所属任务类型**：Agent 编排 / 多 Agent workflow / handoff / tool use / tracing / guardrails  
**研究日期**：2026-06-24  
**研究方式**：只读阅读 GitHub README、官方 docs：agents、running_agents、multi_agent、tools、handoffs、tracing、guardrails、results、context、human-in-the-loop；通过 GitHub API 只读核对仓库元数据；未安装依赖、未运行 SDK 或示例、未写入文件。  
**仓库概况**：OpenAI 官方 Python Agents SDK；GitHub API 显示约 27.4k stars、4.2k forks，MIT License。

## 0 为什么优秀

OpenAI Agents SDK 的优秀点不在于“又封装了一层 LLM 调用”，而在于它把生产级 agent workflow 的关键运行时问题做成统一抽象：`Agent` 定义角色、指令、工具、handoff、guardrails、结构化输出；`Runner` 执行循环；`RunResult` 暴露 final output、运行项目、原始响应、可恢复状态；tracing 默认记录 LLM 生成、工具调用、handoff、guardrail 等跨度。

它特别适合作为方法论样本，因为它把“让模型自主决策”和“由代码确定流程”放在同一套框架内：既允许 manager agent 调用 specialist agent，也允许 triage agent handoff 给专家，还允许代码用结构化输出来路由、串联、并行或循环评审。其价值在于提供了任务完成闭环：意图识别 → 工具行动 → 专家转交 → 安全检查 → 可观测追踪 → 结果续接。

## 1 任务

OpenAI Agents SDK workflow 的核心任务是：让一个或多个 LLM agent 在受控边界内完成复杂工作。典型任务包括客服分流、检索问答、代码/数据分析、文档处理、工作流自动化、需要人工审批的敏感操作等。

SDK 把任务拆成三类对象：

1. **Agent**：承载系统指令、模型、工具、handoff 目标、guardrails、输出类型。
2. **Runner**：负责执行 agent loop，处理模型输出、工具调用、handoff、turn 限制。
3. **Result/State/Trace**：记录输出、运行项目、审批中断、可恢复状态和调试轨迹。

任务的完成不是单次 prompt，而是循环：模型判断下一步，SDK 执行工具或切换 agent，直到产生 final output 或触发中断/异常。

## 2 触发条件

触发入口主要是 `Runner.run()`、`Runner.run_sync()`、`Runner.run_streamed()`。输入可以是字符串、Responses API 格式的 input items，或可恢复的 `RunState`。

适合触发 workflow 的条件包括：用户发起一个开放任务、系统收到需要自动处理的工单、后台任务消费队列消息、前端会话继续一个历史线程、人工审批后恢复暂停的 run。

如果任务是开放式、需要模型规划，则让 agent 自主选择工具或 handoff；如果任务需要确定性、成本可控、流程固定，则由代码进行分类、串联、并行或评审循环。官方文档明确区分了 LLM orchestration 与 code orchestration，并建议可混合使用。

## 3 输入/输出

**输入**包括用户文本、消息列表、上下文对象、会话状态、审批恢复状态等。`RunContextWrapper.context` 用于传递本地依赖和业务状态，例如用户 ID、logger、数据访问器；该 context 不会发送给 LLM，是本地运行时数据。

**输出**以 `RunResult` 或 `RunResultStreaming` 返回。关键字段包括：

- `final_output`：最终回复；如果 agent 设置了 `output_type`，可为结构化对象。
- `new_items`：包含 agent、tool、handoff、approval 等元数据的运行项目，适合审计和 UI。
- `last_agent`：最后处理任务的 agent，可用于下一轮会话。
- `raw_responses`：原始模型响应，适合诊断。
- `to_input_list()` / `to_state()`：用于续接对话或恢复中断任务。

若存在人工审批，`final_output` 可能为空，结果中会出现 `interruptions`，需审批后以 `RunState` 恢复。

## 4 步骤流程

SDK 的标准 agent loop 是该 workflow 的核心：

1. 传入 starting agent 与输入。
2. Runner 调用当前 agent 的 LLM。
3. LLM 产出结果。
4. 如果结果是 final output，结束并返回。
5. 如果结果是 handoff，SDK 更新当前 agent 和输入，再次进入循环。
6. 如果结果包含工具调用，SDK 执行工具、追加工具结果，再次进入循环。
7. 如果超过 `max_turns`，抛出 `MaxTurnsExceeded`；可设置 `max_turns=None` 关闭限制。

这个循环把模型“想做什么”与运行时“实际执行什么”连接起来。工具调用和 handoff 都被建模成模型可选择的动作，但实际执行由 SDK 接管。对于 streaming，`Runner.run_streamed()` 会边执行边产生事件，最终 `RunResultStreaming` 仍保留完整运行信息。

## 5 工具/API/CLI/外部服务

工具体系是 SDK 完成任务的行动层。官方文档列出五类：

1. **Hosted OpenAI tools**：如 WebSearchTool、FileSearchTool、CodeInterpreterTool、HostedMCPTool、ImageGenerationTool、ToolSearchTool。
2. **本地/运行时工具**：ComputerTool、ApplyPatchTool、ShellTool 等，其中部分可本地或容器执行。
3. **Function calling**：用 `@function_tool` 将 Python 函数包装成工具。
4. **Agents as tools**：用 `Agent.as_tool()` 让一个 agent 作为另一个 agent 的可调用工具。
5. **实验性 Codex tool**：用于 workspace-scoped Codex 任务。

主要 API 包括 `Agent`、`Runner`、`RunConfig`、`RunResult`、`RunState`、`handoff()`、`function_tool`、`trace()`、`flush_traces()`、guardrail 相关装饰器/结果对象。

CLI 方面，官方资料中未在 workflow 主线中找到独立 CLI 作为核心操作面；主要用法是 Python API。外部服务包括 OpenAI Responses API、Traces dashboard、Vector Stores、Hosted MCP、web search、code interpreter 等。

## 6 决策点

关键决策点包括：

- **Agents as tools 还是 handoffs**：如果希望 manager agent 保持对话控制、整合多个专家输出，用 agents as tools；如果希望 triage 后由专家直接接管后续交互，用 handoffs。
- **LLM 编排还是代码编排**：开放任务适合 LLM 自主计划；强约束流程适合代码用结构化输出分类、串联、并行或循环评审。
- **工具前置还是延迟加载**：工具面很大时，可用 ToolSearchTool 与 deferred-loading 降低 schema token。
- **guardrail 并行还是阻塞**：默认并行延迟低，但可能先消耗 token 或执行工具；阻塞模式可在 agent 启动前拦截输入，适合成本和副作用敏感场景。
- **是否需要人工审批**：取消订单、发邮件、shell、patch、MCP 等敏感工具可声明 approval，触发中断并恢复。
- **是否需要 trace 分组**：单次 run 默认 trace；多次 run 可用外层 `trace()` 归为同一 workflow。

## 7 验证Gate

SDK 的验证 Gate 分布在多个层次：

1. **输入 guardrail**：只在链路第一个 agent 的初始用户输入上运行；tripwire 触发则抛出异常。
2. **输出 guardrail**：只在产生 final output 的最后 agent 上运行；用于检查最终答案。
3. **工具 guardrail**：包裹 function tool，每次调用前后都可检查、替换输出或阻断。
4. **结构化输出**：通过 `output_type` 约束 final output 类型，便于代码判断。
5. **审批 Gate**：`needs_approval` / `require_approval` 让敏感工具暂停，人工 approve/reject 后恢复。
6. **turn 限制**：`max_turns` 防止无限工具循环或 handoff 循环。
7. **tracing Gate**：默认记录 generation、tool、handoff、guardrail span，帮助定位失败环节。
8. **evals**：官方建议对 agent workflow 投入 evals，以持续改进提示词、工具描述和路由策略。

## 8 可迁移到 Hermes Skills 的写法

可迁移到其他 agent 框架的方法论如下：

```text
定义角色边界：
- manager/triage 只负责判断和整合
- specialist 只负责窄领域任务
- tool 只暴露明确、可验证的动作

执行循环：
while not final:
    call current_agent
    if final_output: return
    if handoff: current_agent = target
    if tool_call: execute tool and append result
    if turn_limit exceeded: fail safely

安全层：
input guardrail → tool approval/tool guardrail → output guardrail

观测层：
workflow trace → agent span → generation span → tool/handoff/guardrail span
```

实践上，应把“什么时候用哪个专家/工具”写进 handoff description、tool description 和 instructions；把“不能做什么”写进 guardrails 与审批规则；把“如何复盘”交给 trace 和 `new_items`。

## 9 适合归类的任务类型

该候选属于 **Agent Orchestration / Multi-Agent Workflow Runtime**。

二级分类可标为：

- 多 agent 编排框架
- 工具调用运行时
- handoff/manager-agent 模式
- tracing/observability 框架
- guardrails 与 human-in-the-loop 安全执行框架

它不是单纯 prompt 模板库，也不是纯粹的模型 SDK；其核心是把模型调用、工具执行、专家路由、安全检查和运行观测组合成可生产化的 workflow。

## 10 风险与注意事项

1. **模型自主路由不可完全确定**：handoff 和工具选择依赖模型判断，需要 evals 与 trace 迭代。
2. **并行 guardrail 可能已有副作用**：默认输入 guardrail 并行执行，失败时 agent 可能已消耗 token 或触发工具；敏感场景应用阻塞模式。
3. **工具描述质量决定执行质量**：工具 schema、description、参数命名不清会导致误用。
4. **handoff 历史与上下文过滤复杂**：跨 agent 传递历史时需明确 input_filter 和 nested handoff history 策略。
5. **本地 context 不进 LLM**：若误以为 `RunContextWrapper.context` 会被模型看到，会造成信息缺失。
6. **tracing 数据政策限制**：官方说明 ZDR 组织不可用 tracing。
7. **CLI 信息不足**：未在上游 workflow 资料中找到独立 CLI 主流程，需复查后续版本是否新增。

## 11 一句话方法论

OpenAI Agents SDK 的方法论是：用 `Agent` 定义角色和边界，用 `Runner` 维护“模型决策—工具执行—handoff 转交—结果返回”的循环，用 guardrails/approval 控制风险，用 tracing/results 把每一步变成可观察、可恢复、可评估的生产级 agent workflow。

## 12 候选池状态更新说明

- 报告写入路径：`skills/agent-orchestration/openai-agents-sdk-workflow.md`
- `catalog/by-task-type.md` 对应行是否已改为 `已有报告`：应在本报告落盘后更新。
- 是否需要补充/修正入选理由：建议补充“以 `Agent + Runner + Result/State/Trace` 组织模型决策、工具执行、handoff、guardrails、approval 和 tracing，是生产级 agent workflow 的高价值样本”。
- 后续可继续深挖的问题：实作阶段可补充最小 demo、eval 用例、trace 截图，以及与 LangGraph、CrewAI 等编排框架的对比。
