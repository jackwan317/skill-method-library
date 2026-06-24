# Anthropic 官方 `skill-creator` 方法论研究

- 来源：https://github.com/anthropics/claude-plugins-official
- 本仓库原文路径：`originals/anthropic/claude-plugins-official/plugins/skill-creator/skills/skill-creator/`
- 原始链接：https://github.com/anthropics/claude-plugins-official/tree/main/plugins/skill-creator/skills/skill-creator
- 分类：Skill / Plugin / Agent 开发；Agent 评估；流程产品化
- 研究方式：独立 sub agent 只读研究

## 0. 为什么认为这个 Skill 优秀

- **官方来源**：来自 Anthropic 官方 `claude-plugins-official` 仓库。
- **元 Skill 标杆**：它研究的是如何创建、改进、评估和打包 Skills，本身就是学习 Skill 工程化的核心样本。
- **结构非常完整**：包含 `SKILL.md`、`references/`、`agents/grader.md`、`agents/comparator.md`、`agents/analyzer.md`、`eval-viewer/` 和多种 `scripts/`。
- **验证闭环强**：强调 with-skill vs baseline、assertions、benchmark、人类 review、blind comparison、description trigger eval。
- **可迁移价值极高**：能直接指导我们怎样写 Hermes Skill、怎样测试触发、怎样避免 Skill 只是提示词片段。

## 1. 它要完成的任务

`skill-creator` 是一个“元 Skill”：创建、修改、测试、评估、优化和打包其他 Skills。它不是一次性写 `SKILL.md`，而是建立 Skill 工程闭环：理解意图、写初版、设计 eval、跑 with-skill 与 baseline、让用户评审、定量打分、根据 transcript 迭代、优化 description 触发、最终打包。

## 2. 触发条件

适用场景：

- 用户要从零创建 Skill。
- 用户要把一段成功工作流沉淀成 Skill。
- 用户要修改、优化已有 Skill。
- 用户要测试 Skill 是否有效。
- 用户要跑 eval / benchmark。
- 用户要比较两个 Skill 版本。
- 用户要优化 description，提高触发准确率。
- 用户要打包 `.skill` 文件。

不必触发：用户只是一次性完成普通任务，且没有复用或沉淀意图。

## 3. 输入 / 输出

输入通常包括：用户自然语言需求、已有对话里的可复用流程、已有 Skill 文件、测试样例、输入文件、用户反馈、历史 eval 结果。

输出包括：

- Skill 文件：`SKILL.md`、可选 `scripts/`、`references/`、`assets/`。
- 测试产物：`evals/evals.json`、iteration 目录、outputs、timing、grading、benchmark、feedback。
- description 优化结果：trigger eval set、best description、HTML report。
- 打包结果：`.skill` 文件。

## 4. 完成任务的步骤流程

核心流程：

```text
理解意图 → 访谈/研究 → 写初版 Skill → 设计真实 eval →
并行跑 with-skill 与 baseline → 编写 assertions → 捕获 timing →
grader 评分 → 聚合 benchmark → analyzer 分析 → 生成 review viewer →
用户反馈 → 泛化改进 → 重测 → 优化 description → 校验并打包
```

关键细节：

1. **Capture Intent**：先从已有对话抽取目标、触发场景、输入输出、工具使用方式和用户纠正，不要让用户重复说。
2. **Interview and Research**：补齐边界、输入格式、输出格式、成功标准、依赖项、示例文件、类似 Skill。
3. **Write SKILL.md**：frontmatter description 是主要触发机制，要写清做什么、何时用、用户可能怎么表达、边界和近义场景。
4. **设计 2–3 个真实 eval prompts**：先跑真实任务，再逐步补 assertions。
5. **with-skill 与 baseline 并行跑**：新 Skill 的 baseline 是 without_skill；改进旧 Skill 的 baseline 是 old_skill snapshot。
6. **运行期间写 assertions**：不空等任务完成，利用等待时间编写客观可验证断言。
7. **捕获 timing/token**：subagent 完成通知中的 duration/token 是唯一捕获机会。
8. **评分与聚合**：用 grader 输出 pass/fail/evidence，再聚合 benchmark。
9. **人类评审**：生成 HTML review viewer，展示 prompt、outputs、grades、benchmark、previous iteration，收集 `feedback.json`。
10. **迭代**：从反馈抽象一般原则，不为单例过拟合。
11. **description 优化**：生成 should-trigger / should-not-trigger queries，多次运行，train/test split，选择 held-out test 最好版本。
12. **打包**：校验结构并输出 `.skill`。

## 5. 工具 / 脚本 / MCP / API

目录包含：

```text
SKILL.md
references/schemas.md
agents/grader.md
agents/comparator.md
agents/analyzer.md
eval-viewer/generate_review.py
scripts/improve_description.py
scripts/run_eval.py
scripts/run_loop.py
scripts/aggregate_benchmark.py
scripts/package_skill.py
scripts/quick_validate.py
```

核心脚本用途：

- `aggregate_benchmark.py`：读取 `grading.json`，聚合 pass rate、tokens、duration、均值/方差、delta，输出 `benchmark.json` 与 `benchmark.md`。
- `generate_review.py`：生成评审页面，嵌入输出、grades、benchmark，并支持保存/导出 feedback。
- `run_eval.py`：评估 description 是否触发，使用 `claude -p` 和 available_skills。
- `improve_description.py`：基于失败 case 调用 `claude -p` 改写 description。
- `run_loop.py`：执行 description 优化闭环，使用 train/test split 防止过拟合。
- `quick_validate.py`：检查 `SKILL.md`、frontmatter 和基本结构。
- `package_skill.py`：校验并打包 `.skill`。

Agents：

- `grader.md`：对单次运行按 expectations 打分，必须给 evidence。
- `comparator.md`：盲评 A/B 输出，减少偏见。
- `analyzer.md`：揭盲后分析赢家为什么赢、失败模式和下一步改进。

## 6. 关键决策点

- **是否需要测试**：文件转换、数据抽取、代码生成、固定工作流适合；艺术审美、开放式创意可更依赖人类评审。
- **新建还是改进**：决定 baseline 是 without_skill 还是 old_skill。
- **何时写 scripts**：当多个 eval 都重复写相同 helper code，说明应脚本化。
- **何时写 references**：长规范、API 文档、格式说明不塞进 `SKILL.md`，放 references 并在正文说明何时读取。
- **assertions 是否足够强**：弱断言会制造虚假信心；断言要能区分真正成功与表面成功。
- **是否过拟合 eval**：改进要抽象成一般原则，description 优化用 held-out test。
- **何时停止**：用户满意、反馈为空、没有显著进展或达到可交付质量。

## 7. 验证方法

多层验证：

1. **人类评审**：review viewer 展示 outputs、grades、benchmark 和 feedback box。
2. **定量 assertions**：每个 run 生成 `grading.json`，包含 text/passed/evidence。
3. **benchmark 聚合**：比较 pass rate、tokens、duration、variance、delta。
4. **blind comparison**：A/B 盲评输出质量，再揭盲分析。
5. **trigger eval**：should-trigger 与 should-not-trigger 查询，多轮运行，统计触发率。

## 8. 可迁移到 Hermes Skills 的写法

- description 写成触发策略，而不是简介。
- `SKILL.md` 主体写执行手册：目标、何时使用、输入、输出、工作流、关键决策、验证、失败恢复。
- 使用渐进披露：核心流程放 `SKILL.md`，长文档放 `references/`，确定性步骤放 `scripts/`。
- 建立轻量 eval loop：with-skill、baseline、outputs、grading、benchmark、feedback。
- 人类评审前置，尽早生成 Markdown/HTML/Feishu 可审阅结果。
- 固定 grading schema，避免每个 Skill 自创格式。
- 触发描述要测试 near-miss：既测该触发，也测不该触发。

## 9. 分类

- 元编程 / 元工作流：把流程产品化成 Skill。
- Agent 评估与 benchmark：系统评估 Skill 是否有用。
- 人机协作式开发：把用户反馈作为核心信号。
- 知识产品化：把隐性经验变成可复用资产。
- 触发策略优化：让 Skill 准确触发且少误触发。

## 10. 一句话方法论

Skill 不是提示词片段，而是一个带触发策略、工作流、脚本、评估、用户反馈和打包交付的可复用产品。
