# OpenAI Plugins `plugin-creator` 方法论研究

- 来源：https://github.com/openai/plugins
- 原始链接：https://github.com/openai/plugins/blob/main/.agents/skills/plugin-creator/SKILL.md
- 分类：Skill / Plugin / Agent 开发
- 研究方式：独立 sub agent 只读研究

## 1. 它要完成的任务

`plugin-creator` 用于为 Codex/OpenAI Plugins 创建插件脚手架：生成标准插件目录、必需的 `.codex-plugin/plugin.json`，按需创建 `skills/`、`hooks/`、`scripts/`、`assets/`、`.mcp.json`、`.app.json`，并可生成或更新 `marketplace.json`，使插件能在 Codex UI/插件市场中出现。

核心目标不是替用户完成业务插件，而是建立**可编辑、可验证、可发布前补全的标准骨架**。

## 2. 触发条件

适用场景：

- 用户要创建新的个人插件或团队/仓库插件。
- 用户要给现有插件补齐标准目录结构。
- 用户要生成或更新个人 `~/.agents/plugins/marketplace.json`。
- 用户要生成或更新仓库级 `<repo-root>/.agents/plugins/marketplace.json`。
- 用户需要插件在 Codex UI 中可见、可排序、可分享。

关键边界：如果当前 Git 仓库已有 `.agents/plugins/marketplace.json`，且用户没说明“个人插件”还是“团队/仓库插件”，在写 marketplace 前必须询问，避免把团队插件写进个人路径或相反。

## 3. 输入 / 输出

最小输入是插件名：

```bash
python3 .agents/skills/plugin-creator/scripts/create_basic_plugin.py <plugin-name>
```

常用参数包括：

- `--path <parent-dir>`：插件父目录，默认 `~/plugins`。
- `--with-marketplace`：创建或更新 marketplace。
- `--marketplace-path <path>`：指定 marketplace 文件。
- `--with-skills` / `--with-hooks` / `--with-scripts` / `--with-assets`：创建可选目录。
- `--with-mcp`：创建 `.mcp.json` 占位文件。
- `--with-apps`：创建 `.app.json` 占位文件。
- `--install-policy`、`--auth-policy`、`--category`、`--force`。

固定输出：

```text
<parent-dir>/<plugin-name>/
└── .codex-plugin/
    └── plugin.json
```

可选输出：`skills/`、`hooks/`、`scripts/`、`assets/`、`.mcp.json`、`.app.json`。若启用 marketplace，还会写入个人或仓库级 `marketplace.json`。

## 4. 完成任务的步骤流程

1. **规范化插件名**：小写 kebab-case，移除多余标点、折叠连续 `-`、限制长度，目录名与 `plugin.json.name` 必须一致。
2. **判断插件范围**：默认个人插件；仓库/团队插件需要指定 `--path ./plugins` 和仓库级 marketplace。
3. **生成插件根目录**：默认 `~/plugins/<plugin-name>/`。
4. **生成 manifest**：创建 `.codex-plugin/plugin.json`，包含 name/version/description/author/repository/license/keywords/skills/hooks/mcpServers/apps/interface 等字段。
5. **保留 TODO 占位**：不臆造作者、仓库、隐私政策、截图、logo 等信息；占位值等待用户或后续步骤补全。
6. **按需创建可选结构**：通过 flags 显式创建 skills/hooks/scripts/assets/MCP/apps。
7. **按需更新 marketplace**：新建 marketplace 时写顶层 name/interface/plugins；插件条目写 name/source/policy/category。
8. **避免隐式覆盖**：已有同名目录、manifest 或 marketplace 条目时，除非用户明确要求，否则不覆盖。
9. **生成 Codex app handoff**：如果创建了 marketplace-backed plugin，最终回复提供 `codex://plugins/...` 查看与分享链接。

## 5. 工具 / 脚本 / MCP / API

核心工具是本地 Python 脚本：

```text
.agents/skills/plugin-creator/scripts/create_basic_plugin.py
```

参考规范：

```text
.agents/skills/plugin-creator/references/plugin-json-spec.md
```

该 Skill 不依赖外部 API。MCP 只体现为可选 `.mcp.json` 占位文件，默认内容为空服务器配置。

## 6. 关键决策点

- **个人还是团队**：默认个人；当前 repo 存在 marketplace 但用户未说明时先询问。
- **是否写 marketplace**：只想要骨架则不写；需要 Codex UI 可见则写。
- **是否覆盖**：默认不覆盖；`--force` 必须来自明确用户意图。
- **是否填真实元数据**：脚手架阶段保留 `[TODO: ...]`，不编造信息。
- **是否加产品 gating**：默认不写 `policy.products`，除非用户明确要求。
- **插件顺序**：`plugins[]` 顺序就是 Codex 渲染顺序，不应隐式重排。

## 7. 验证方法

文档要求编辑 Skill 后运行：

```bash
python3 <path-to-skill-creator>/scripts/quick_validate.py .agents/skills/plugin-creator
```

实际创建插件后建议验证：

- 目录存在。
- `.codex-plugin/plugin.json` 存在且 JSON 可解析。
- `plugin.json.name` 等于标准化后的目录名。
- 可选目录/文件按参数生成。
- marketplace JSON 可解析。
- 同名条目存在且字段完整。
- `source.path` 为 `./plugins/<plugin-name>`。
- 未意外覆盖已有条目。
- `plugins[]` 顺序符合用户要求。

## 8. 可迁移到 Hermes Skills 的写法

它的强模式是“脚手架生成器 Skill”：

- 标准化名称 = 目录名 + manifest 主键。
- 必需 manifest 永远生成。
- 可选能力通过显式 flags 创建。
- 默认不覆盖，覆盖必须显式。
- registry/marketplace 与插件本体解耦。
- registry 条目保持最小但完整：名称、来源、策略、分类。
- UI 展示字段集中放在 `interface`。
- 默认个人路径，团队路径需显式或经确认。
- 保留占位符，避免生成虚假元数据。
- 把排序语义绑定到数组顺序，避免 agent 自作主张。

## 9. 分类

- Skill 类型：脚手架 / 生成器 / 元数据维护
- 目标对象：OpenAI Plugins / Codex plugins
- 主要产物：插件目录、`plugin.json`、`marketplace.json`
- 执行方式：本地 Python 脚本
- 可扩展组件：skills、hooks、scripts、assets、MCP、apps
- 风险点：误覆盖、个人/团队 marketplace 混淆、虚假占位填充、错误 marketplace 路径、错误策略字段

## 10. 一句话方法论

先建立标准插件骨架和 registry 条目，不臆造元数据、不默认覆盖、不混淆个人与团队作用域，让后续业务实现建立在可验证的插件结构上。
