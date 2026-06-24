# OpenAI Plugins `frontend-app-builder` 方法论研究

- 来源：https://github.com/openai/plugins
- 本仓库原文路径：`originals/openai/plugins/plugins/build-web-apps/skills/frontend-app-builder/`
- 原始链接：https://github.com/openai/plugins/blob/main/plugins/build-web-apps/skills/frontend-app-builder/SKILL.md
- 分类：前端 / UI / 设计到代码 / 视觉 QA
- 研究方式：独立 sub agent 只读研究

## 0. 为什么认为这个 Skill 优秀

- **官方来源**：来自 OpenAI 官方 `openai/plugins` 仓库。
- **流程完整**：覆盖需求提取、Image Gen 概念设计、用户批准、设计系统提取、前端实现、浏览器验证、视觉对比。
- **质量 gate 明确**：包含 Hard Rules、Hard Stops、fidelity ledger、above-the-fold copy diff、final response contract。
- **工具编排清晰**：明确 Image Gen、Browser/IAB、Playwright fallback、`view_image`、React/Vite 的使用顺序和边界。
- **可迁移价值高**：它把“做好 UI”这种主观任务拆成可执行、可验证的流程，非常适合学习高质量 Skill 的写法。

## 1. 它要完成的任务

`frontend-app-builder` 用于构建视觉驱动型前端产品：先以资深前端设计师身份生成高质量 Image Gen 视觉概念，再以工程师身份将被接受的设计忠实实现为可运行、可交互、可验证的前端应用。

它适合新建前端应用、dashboard、admin、数据工具、创意网站、landing page、hero section、游戏 UI、redesign/restyle/modernization。它不是“直接写页面”，而是**设计先行 + 精确还原 + 浏览器验证**。

## 2. 触发条件

适用：

- 新建前端应用。
- 构建 dashboard/admin/tool UI。
- 设计 landing page、官网、创意网站、hero section。
- 用户要求 redesign / restyle / modernization。
- 用户强调视觉风格、审美、polish、现代化。

可跳过 Image Gen 的例外：用户明确 opt out，或只是已有设计系统内的小 UI fix。

## 3. 输入 / 输出

输入包括：产品目的、受众、页面范围、section/state、工作流、文案、导航、CTA、数据字段、媒体需求、响应式要求、技术约束、现有项目框架、资产目录、图标库、截图/参考图/已有设计。

输出包括：

- 设计阶段：一个或多个 Image Gen 视觉概念图，必要时每个 section/state/detail 单独生成。
- 实现阶段：可运行前端实现、design tokens、组件、响应式布局、本地交互状态、素材。
- 验证阶段：accepted concept path、浏览器截图、视觉对比 ledger、copy diff、交互验证结果和剩余 intentional deviations。

## 4. 完成任务的步骤流程

1. **判断触发**：确认任务是否是视觉驱动前端构建。
2. **提取需求形成 Image Gen brief**：不能写“modern landing page”这种空泛描述，要包含范围、受众、可见内容、页面结构、交互模型、视觉系统、实现约束和负面约束。
3. **生成完整视觉概念**：先设计完整 surface 再编码。多 section 页面默认每个主要 section 生成一张；dense app 需要主屏 + detail/state；游戏需要 asset pass。
4. **概念审查与用户确认**：review/approval 模式下，用户批准前不能实现。被接受的设计稿是 production design spec，不是灵感板。
5. **编码前提取设计系统**：从概念图提取 copy、nav、CTA、section 顺序、首屏构图、颜色 tokens、字体、spacing、radius、shadow、border、icon inventory、组件家族、container model、media treatment、asset needs 和核心 workflow。
6. **实现**：遵循现有项目规范；新复杂 app 默认 React + Vite；复杂 UI 拆组件；用 tokens 实现；不添加未批准的首屏文案、badge、pill、overlay、hero image 等。
7. **运行与浏览器验证**：优先 Browser/IAB，fallback 才用 Playwright Chromium 并说明原因。检查首屏、滚动、核心 workflow、桌面和移动 viewport。
8. **图像对比**：必须同时查看 accepted concept 和最新浏览器截图，用 `view_image` 对比，不允许只用功能测试替代视觉 QA。
9. **Fidelity ledger**：至少记录 5 个具体对比点：文案、nav/CTA、section 顺序、首屏平衡、palette、gradient、typography、spacing、radius、asset blending、responsive behavior 等。
10. **最终回复合同**：必须包含 accepted concept path、screenshot method、Browser/IAB 或 Playwright fallback、view_image 检查、native-size viewport、copy diff、intentional deviations、已修复 mismatch、交互路径验证，并声明 faithfully verified。

## 5. 工具 / 脚本 / MCP / API

该 Skill 自身没有专属 `scripts/`，但明确依赖或调用：

- **Image Gen / `@imagegen` Skill**：生成视觉概念、section/state/detail、standalone assets、游戏素材。
- **`request_user_input`**：plan mode 下先生成设计并请求批准，再规划实现。
- **Browser plugin / Built-in Browser / IAB**：默认视觉验证工具。
- **Playwright Chromium**：仅作为 Browser/IAB 不可用或不可靠时的 fallback。
- **`view_image`**：最终对比 accepted concept 与 implementation screenshot 的硬 gate。
- **React + Vite**：新复杂 app UI 的默认实现栈。
- **OpenAI platform API key**：若应用需要 AI/model output，默认使用配置好的 OpenAI key，不写 fake keys。

相关引用：`references/imagegen-website-concepts.md`、`agents/openai.yaml`、所属插件 `plugin.json`。

## 6. 关键决策点

- **是否必须 Image Gen**：新建视觉 UI、redesign、modernization 必须；小 UI fix 或用户 opt out 可跳过。
- **生成多少概念图**：单 section 一张；多 section 每 major section 一张；dense app 加 detail/state；模糊就重生 standalone detail；游戏加 asset pass。
- **何时开始编码**：完整 requested surface 设计完成、必要状态清楚、approval 模式已批准、设计系统 inventory 已提取。
- **是否允许偏离概念图**：默认不允许，除非用户批准、有 blocker 或功能必要并记录为 intentional deviation。
- **选择实现栈**：已有 repo 跟随 repo；用户指定则跟随；新复杂 app 默认 React + Vite。
- **Browser 还是 Playwright**：Browser/IAB 优先；Playwright 只能作为 fallback 且说明原因。
- **能否交付**：必须通过浏览器运行、截图对比、ledger、copy diff、核心交互验证。

## 7. 验证方法

它的关键判断是：**Functional QA does not count as fidelity QA.** build 通过、页面能打开、按钮能点都不等于可交付。

验证包括：

- 浏览器中检查桌面、当前 viewport、移动 viewport。
- 检查首屏、滚动后 section、核心 workflow、本地 state。
- 用 `view_image` 同时检查 accepted concept 和 browser screenshot。
- above-the-fold copy diff：不允许新增未批准 eyebrow、pill、badge、subtitle、proof text 等。
- typography audit：heading/body/caption/labels/sidebar/tabs/inputs/table/chart/mobile line breaks。
- icon audit：metaphor、outline/fill、stroke width、size、color、alignment、optical weight。
- hard stops：主内容裁切、placeholder、stock-like assets、invented visible copy、颜色不匹配、debug artifacts、mobile overflow、任何可见 drift。

## 8. 可迁移到 Hermes Skills 的写法

- frontmatter description 写明适用任务、触发条件、主要方法和质量标准。
- 开头定义 Core Standard，明确哪些优先级高于一切。
- 用 Hard Rules 固化不可违反事项，而不是泛泛建议。
- 把流程写成阶段化工作流：Discovery → Design → Approval → Inventory → Implementation → Verification → Final Response。
- 编码前必须提取 inventory，把模糊任务转成明确清单。
- 写清工具优先级与 fallback 条件，并要求说明 fallback 原因。
- 定义 final response contract，强迫 agent 给验证证据。
- 引入 Fidelity/Evidence Ledger，避免“感觉完成了”。
- 写 Hard Stops：满足任一不可交付条件就继续修，不要结束。

## 9. 分类

一级分类：前端应用构建 / 视觉驱动 UI 实现。

二级分类：Frontend App Builder、Visual UI Generation、Design-to-Code、Landing Page Builder、Dashboard Builder、Creative Website Builder、Game UI Builder、UI Redesign Agent、Browser QA Agent、Fidelity QA Agent。

不适合：纯后端 API、CLI 工具、小型 CSS 修补、已有设计系统中的简单组件 bug、纯算法、纯文档。

## 10. 一句话方法论

先把用户需求变成可批准的完整视觉规范，再把规范转译成设计系统与组件，最后用真实浏览器截图和设计图做证据化对比，直到视觉忠实度可交付。
