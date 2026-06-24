# Cloudflare MCP Server Skill 方法论研究

**元数据**

- **来源**：Cloudflare 官方 GitHub 仓库与公开 README / 源码目录 / tool definitions
- **原始链接**：https://github.com/cloudflare/mcp-server-cloudflare
- **本仓库报告路径**：`skills/mcp/cloudflare-mcp-server-skill.md`
- **候选池备注状态**：`研究中` → `已有报告`
- **所属任务类型**：MCP / 云平台资源管理 / Agent 工具系统方法论研究
- **研究日期**：2026-06-24
- **研究方式**：只读研究；阅读公开 GitHub README、仓库目录、`apps/*` 服务实现、`packages/mcp-common` 公共框架、部分 tool definitions；未运行目标 MCP server，未安装依赖，未连接 Cloudflare 账号，未下载或归档原文，未修改任何文件。

## 0 为什么优秀

Cloudflare MCP Server 的优秀之处不在于“把 API 包一层工具”这么简单，而在于它把 Cloudflare 庞大的云平台能力拆成一组**远程、领域化、带认证与账号解析能力的 MCP Server**。仓库根 README 明确区分两类形态：一类是统一 Code Mode server，适合广覆盖和代码执行；另一类是本仓库的 domain-specific servers，适合有边界、强类型、面向具体产品域的交互。

它的核心方法是：用 Cloudflare Workers / Durable Object 承载远程 MCP；用 OAuth Provider 处理授权；用公共 `CloudflareMCPServer` 包装 MCP SDK；用 `accountTool` 把“工具参数校验、账号选择、Cloudflare API 调用、错误返回、指标记录”做成统一模板。这样每个产品服务只需要注册自己的工具，不必重复实现鉴权、账号选择、传输协议、指标和错误处理。

## 1 任务

该项目要完成的任务是：让 MCP Client，例如 Cursor、Claude、Cloudflare AI Playground 或支持 remote MCP 的客户端，通过自然语言访问 Cloudflare 资源与数据。任务覆盖配置读取、日志查询、文档检索、Workers 绑定管理、Browser Rendering、AI Gateway、Audit Logs、DNS Analytics、DEX、CASB、GraphQL、Logpush、Workers Builds、Observability 等。

其任务不是提供一个单体万能 API 代理，而是把不同 Cloudflare 产品拆成多个远程 MCP endpoint，例如 `https://observability.mcp.cloudflare.com/mcp`、`https://bindings.mcp.cloudflare.com/mcp`、`https://browser.mcp.cloudflare.com/mcp`。客户端可以只接入自己需要的产品域，从而减少工具噪音与权限面。

## 2 触发条件

适合触发该类 Skill / MCP Server 的条件包括：

1. 用户希望用自然语言管理或查询 Cloudflare 资源。
2. 用户的问题能落到某一产品域，例如 Worker 日志、KV / R2 / D1、DNS 报告、AI Gateway 日志、GraphQL Analytics、Browser Rendering 截图等。
3. 用户已经具备 Cloudflare 授权，或客户端支持 remote MCP OAuth 登录。
4. 任务需要结构化工具调用，而不是泛泛网页搜索。
5. 对权限、安全和账号边界有要求，希望工具只暴露特定领域能力。

不适合触发的情况：需要跨 Cloudflare 全 API 自由探索且能接受代码执行时，上游建议考虑统一 Code Mode server；Radar README 中也提示 Radar 领域服务已 deprecated，建议迁移到统一 server，此点应在后续使用时复查。

## 3 输入/输出

**输入**主要来自 MCP Client 的 tool call 参数、请求 header、OAuth/API token 认证结果，以及 Cloudflare account context。典型输入包括：OAuth 授权后的 access token / refresh token；Bearer API token；`cf-account-id` 请求头；多账号场景下 tool 参数 `account_id`；各工具自己的 Zod schema 参数，例如 URL、zone id、query、timeframe、namespace id、gateway id、build id 等。

**输出**遵循 MCP content 结构，常见为：

- `text`：JSON 字符串、TSV、Markdown 风格说明、错误信息；
- `image`：Browser Rendering screenshot 返回 base64 PNG；
- `resource`：Container server 文件读取返回资源对象；
- `isError: true`：工具级错误，不一定抛出进程异常。

方法论上，它把“给模型看的结果”尽量压缩成结构化 JSON、TSV 或明确错误文本，便于 LLM 继续推理。

## 4 步骤流程

一个典型请求流程如下：

1. **客户端连接远程 MCP endpoint**：支持 remote MCP 的客户端直接配置 URL；不支持时可用 `mcp-remote` bridge 指向 `/mcp`。仓库也保留 `/sse`，但 README 标注 SSE deprecated。
2. **认证入口判断**：每个 app 的 `fetch` 中先判断是否为 API token 请求；否则进入 OAuth Provider。
3. **OAuth 授权**：`createAuthHandlers`、`handleTokenExchangeCallback`、`cloudflare-auth.ts` 负责生成授权 URL、PKCE code challenge、authorization code 换 token、refresh token。
4. **用户与账号验证**：`getUserAndAccounts` 并行请求 Cloudflare `/user` 与 `/accounts`，区分 user token 和 account token。
5. **初始化 MCP Agent**：各 app 继承 `McpAgent`，在 `init()` 中创建 `CloudflareMCPServer`，注入 server name/version、metrics、accountManager、instructions。
6. **注册工具**：产品 app 调用各自 `register*Tools`，例如 Browser 注册 `get_url_html_content`、`get_url_markdown`、`get_url_screenshot`；Workers Bindings 注册 KV、Workers、R2、D1、Hyperdrive 与 docs tools。
7. **账号解析**：account-scoped 工具通过 `accountTool` 调用 `AccountManager.resolve()`，按 auth-pinned account → `cf-account-id` header → `account_id` 参数顺序确定账号。
8. **调用 Cloudflare API**：工具使用官方 `cloudflare` SDK 或 `fetchCloudflareApi` / 产品 API helper 访问 Cloudflare REST / GraphQL / Browser Rendering / Observability 等接口。
9. **返回 MCP content**：成功时返回 JSON/TSV/image/resource；失败时返回 `isError: true` 和可读错误。

## 5 工具/API/CLI/外部服务

主要外部服务与依赖模式：

- **MCP SDK**：`@modelcontextprotocol/sdk/server/mcp.js`，用于 `McpServer`、`tool`、`registerTool`。
- **Cloudflare Agents**：`agents/mcp` 的 `McpAgent`，用于远程 MCP agent 生命周期。
- **Cloudflare Workers OAuth Provider**：`@cloudflare/workers-oauth-provider`，用于 `/oauth/authorize`、`/token`、`/register`。
- **Cloudflare API**：`https://api.cloudflare.com/client/v4`，覆盖 account、user、GraphQL、产品 API。
- **Cloudflare SDK**：`cloudflare` npm 包，工具中通过 `new Cloudflare({ apiToken })` 调用资源。
- **Cloudflare Workers 基础设施**：Durable Object、KV、Analytics Engine、Vectorize、AI binding、Workers 环境变量。
- **mcp-remote**：客户端不原生支持 remote MCP 时作为桥接 CLI。
- **Hono / Zod**：Hono 处理 HTTP，Zod 定义和验证工具输入/响应结构。
- **Sentry / Metrics**：上游实现了工具调用指标与错误记录；具体生产配置未在本次只读研究中完整验证。

## 6 关键决策点

关键决策点包括：

1. **Code Mode vs domain-specific**：如果需要全 API 覆盖与代码执行，选统一 server；如果需要低噪音、强类型、具体产品域，选本仓库服务。
2. **OAuth vs API Token**：远程交互默认 OAuth；OpenAI Responses API 或某些客户端可传 API token。代码中也存在 dev disable OAuth 模式，但不应迁移到生产。
3. **单账号 vs 多账号**：单账号 token 自动 pin；多账号 token 需要 `cf-account-id` header 或 `account_id`。
4. **只读 vs 可写工具**：工具 annotations 使用 `readOnlyHint`、`destructiveHint` 标注，例如 KV delete 标为 destructive。
5. **工具结果格式**：日志/聚合类结果倾向 TSV，资源详情倾向 JSON，截图返回 MCP image。
6. **服务边界**：每个 app 只暴露该领域工具，避免一个 server 混入过多权限。

## 7 验证方法 / 交付 Gate

可迁移时应设置以下验证 Gate：

- **Schema Gate**：所有工具输入必须有 Zod schema；响应若可控也应 schema parse 或格式化。
- **Auth Gate**：access token 必须通过 `/user`、`/accounts` 或等价 API 验证，不能只信任 header 存在。
- **Account Gate**：账号 ID 必须来自 token 可访问账号列表；不能允许模型任意传 account id。
- **Permission Gate**：OAuth scopes 与工具能力一致；写工具需要显式 scope 与 destructive 标识。
- **Transport Gate**：`/mcp` 可用；`/sse` 如保留需标明 deprecated。
- **Error Gate**：4xx 返回安全错误，5xx 映射为上游不可用并可上报；原始 body 应截断，避免泄露。
- **Observability Gate**：记录 session start、tool call、error code，但避免记录敏感 token / payload。
- **Context Gate**：长结果工具需限制输出，README 中也提示 Observability 多链路 tool call 可能触发上下文长度问题。

## 8 可迁移到 Hermes Skills 的写法

可迁移模式可以概括为：

```text
Remote MCP Worker
  → OAuth/API token auth
  → verify user/accounts
  → create MCP Agent
  → create common MCP Server wrapper
  → register domain tools
  → accountTool resolves tenant/account
  → call product API
  → return compact MCP content
```

迁移到其他云平台时，应抽象出四层：

1. **Auth Adapter**：OAuth、API token、refresh token、scope。
2. **Tenant Resolver**：账号/组织/项目/订阅选择，带 header 与参数兜底。
3. **Tool Registry**：每个产品域一个 `registerXTools()`，工具 schema 与 API 调用靠近。
4. **API Client Layer**：统一错误映射、重试、响应校验、输出压缩。

最值得复用的是 `accountTool` 思路：不要让每个工具重复写账号选择逻辑，而是在注册工具时自动追加 `account_id`，并在 callback 前统一校验。

## 9 适合归类的任务类型

该候选可归类为：

- **MCP Server Skill**
- **Remote MCP / Streamable HTTP**
- **Cloud Platform Resource Management**
- **OAuth-enabled Agent Tooling**
- **Domain-specific Tool Server**
- **Typed API Tool Facade**
- **Observability / Security / Dev Platform Ops**

工具类别包括但不限于：文档检索、Workers 绑定管理、日志与可观测性、Browser Rendering、AI Gateway 日志、审计日志、DNS 分析、DEX、CASB、GraphQL Analytics、Logpush、Workers Builds、Radar。Radar 上游 README 标注 deprecated，需复查是否仍应纳入新方案。

## 10 风险与注意事项

主要风险如下：

1. **权限扩大**：部分工具具备写能力或 destructive 操作，例如创建/删除 KV、D1、R2 等，必须最小化 scope。
2. **账号误选**：多账号用户若未设置 `cf-account-id`，模型传错 `account_id` 可能影响错误账号；上游通过可访问账号校验降低风险。
3. **上下文爆炸**：日志、GraphQL schema、Observability 查询可能返回大量内容，需要分页、limit、TSV 压缩。
4. **OAuth 复杂度**：PKCE、refresh、client registration、state/CSRF 都是高风险环节；迁移时不能简化为“保存 token”。
5. **远程依赖不可控**：Cloudflare API 429/5xx、产品权限不足、付费特性限制会影响工具结果。
6. **工具描述注入风险**：工具返回外部网页 markdown、日志内容或用户数据时，Agent 应防止把数据当指令执行。
7. **上游变动**：仓库包含多个 app，部分服务状态不同；未在上游资料中找到统一稳定性等级清单，需复查具体服务成熟度。

## 11 一句话方法论

把云平台 MCP 化的关键，不是暴露所有 API，而是用远程 MCP + OAuth + 租户解析 + 领域工具注册，把高风险云资源操作变成可验证、可限权、可观测、可组合的 Agent 工具面。

## 12 候选池状态更新说明

- 报告写入路径：`skills/mcp/cloudflare-mcp-server-skill.md`
- `catalog/by-task-type.md` 对应行是否已改为 `已有报告`：应在本报告落盘后更新。
- 是否需要补充/修正入选理由：建议补充“通过公共 `mcp-common` 层沉淀 `CloudflareMCPServer`、`AccountManager`、`accountTool`、OAuth/API token 模式、错误映射和各产品 `register*Tools`，是云平台能力 MCP 化的高价值样本”。
- 后续可继续深挖的问题：具体服务成熟度、OAuth scopes 覆盖、各工具 destructive 标注完整性、长结果压缩策略，以及与统一 Code Mode server 的取舍边界。
