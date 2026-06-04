---
name: doc-maintenance
description: |
  [每回合强制 · 项目级 Skill] HANCLI docs 维护 + 问答归档。本 Skill 在 available_skills 即生效（无需单独 Rule）：用户问本项目技术问题（说明/如何/MCP/CLI/Skill…）→ Agent 须先 Write docs/questions/ 再回复；禁止等用户说「归档」。答复前必读 references/every-turn-protocol.md。Cursor 不代写文件。
version: "1.10.0"
author: HanRuo
tags: [docs, markdown, maintenance, reusable, qna-archive]
---

# 文档维护（doc-maintenance）

## 首读：每回合协议（最高优先级，先于 § 一及正文其余部分）

> Skill 正文 400+ 行；**不得因未读全文而跳过归档**。答复用户**之前**必须先读并执行：

**→ [`references/every-turn-protocol.md`](references/every-turn-protocol.md)**（≤60 行）

| 步 | 动作 |
|----|------|
| 1 | 构思答复 |
| 2 | **Write/StrReplace** → `docs/questions/`（及模块副本、README） |
| 3 | **最后**回复用户 + `📁 已归档：…` 或 `📁 未归档：…` |

用户**不必**说「归档 / doc-maintenance / docs/questions」。**仅依赖本 Skill**（`available_skills` 或 `@doc-maintenance`），**不**再使用单独的 `.cursor/rules/` 文件。

### 问答自动归档（强制摘要）

本仓库启用 **doc-maintenance**（Skill 在 `available_skills` 即生效）。Cursor **不会**后台写 `docs/questions/`。

| 执行顺序（禁止颠倒） | |
|----------------------|--|
| 1 | 检索并构思答复 |
| 2 | **Write / StrReplace** → `docs/questions/`（§7.3～7.5；可映射则同步 `docs/modules/{category}/questions/`） |
| 3 | 更新 README + `docs/meta/question-bank-*.md` |
| 4 | **最后**回复用户；末尾 `📁 已归档：docs/questions/…` 或 `📁 未归档：<原因>` |

**默认契约**：本项目技术问题 → **必须**归档；**禁止**问「是否需要归档」、**禁止**先回复下轮再写盘、**禁止**未调用写文件工具就声称「已自动归档」。**跳过**：与仓库无关、无实质答复、用户明确「不要归档」。

---

**范围**：仅 **当前项目仓库** 内的 `docs/` 与源码模块对应文档。  
**不处理（非 docs 维护任务）**：Skill 安装/复制到其它仓库、`.hancli/skills/` 目录维护、用户级 Skill 管理、与**本仓库无关**的外部工具。  
**仍须归档**：用户在本仓库内问 Skill/Rule/Hooks/归档机制、MCP、CLI 等——只要给出实质答复且 § 7.0 未排除，**一律 § 七落盘**，不因话题涉及 doc-maintenance 而跳过。

通用流程见 `references/generic-conventions.md`；项目差异见 `references/project-profile.md`。  
**回合结束强制门**见 [§ 零](#零回合结束强制门cursor--agent-最高优先级)；检查清单见 `references/agent-completion-gate.md`。

---

## 零、回合结束强制门（Cursor / Agent，最高优先级）

> **「自动归档」** 在本 Skill 中指：**Agent 在同一回合内必须用写文件工具落盘**；**不是** Cursor 平台在对话结束后自动写 `docs/questions/`。  
> **「无需用户提问归档」**：用户**不必**在消息里出现「归档」「docs/questions」「doc-maintenance」「问题库」等词；只要本 Skill 已加载且用户问的是**本项目**技术问题，答复后**默认**执行 § 七。

### 0.0 Skill 加载即生效（默认契约，优先于一切可选触发）

**Agent 第一步**：读 [`references/every-turn-protocol.md`](references/every-turn-protocol.md)（完整协议 ≤60 行）。

下列 **任一** 成立，即视为本会话已启用问答归档（直至用户说「不要归档」）：

| 信号 | 说明 |
|------|------|
| **Skill 在可用列表** | `doc-maintenance` 出现在 Cursor Agent 的 `available_skills` / 用户 `@doc-maintenance` / 系统已注入本 Skill 描述 |
| **本会话已读过协议** | 读过 `every-turn-protocol.md` 或 `SKILL.md` 文首「首读」节 |

**启用后的强制行为**（每个 Agent 回合、**向用户发送最终答复之前**）：

1. 用户消息是**普通项目问题**（如「MCP 是什么」「怎么实现 hooks」）→ **必须** § 七归档；**不得**因用户未提「归档」而跳过。  
2. **禁止**向用户询问「是否需要归档」「是否写入问题库」——项目 Q&A **默认写入**。  
3. **必须先调用写文件工具**，再发用户可见的最终消息；仅当 [§ 7.0](#70-自动归档判定) 明确**不归档**时，在回复末写 `📁 未归档：<原因>`。

### 0.1 何时本回合须执行 § 零 + § 七

在 **§ 0.0 已生效** 的前提下，用户对**本仓库**给出需实质答复的问题 → **默认须归档**。

下列仅为**补充示例**（**不是**必要条件；用户**无需**主动提及）：

| 用户问题类型 | 示例 |
|--------------|------|
| 源码 / 运行时 | MCP、Agent 循环、CLI、`ToolRegistry` |
| 文档 / 流程 | 模块 doc、`docs/` 结构（即使用户只说「说明一下 mcp」） |
| 本 Skill / Cursor 机制 | Hooks 与 Skill 区别、为何不会自动写盘（**仍要归档该次 Q&A**） |
| 修改 doc-maintenance | 修复/升级本 Skill 后，**必须**归档当次相关 Q&A |

**错误理解（禁止）**：只有用户说「归档」「写进 docs/questions」才触发 → **错误**；§ 0.0 下**一律默认触发**。

### 0.2 回合结束前（禁止跳过）

在向用户发送**最终答复**之前，按序自检：

1. 是否已对当前问题（及本回合内尚未落盘的项目 Q&A）给出**实质答复**？→ 否：可跳过归档。
2. [§ 7.0](#70-自动归档判定) 是否**应归档**？→ 否：回复末写 `📁 未归档：<原因>`。
3. **必须**执行 [§ 7.3](#73-连续问答判定归档前必查)～[§ 7.5](#75-落盘步骤)：`docs/questions/` 总库 → 模块副本（可映射时）→ 问题库 → README。
4. **禁止**在未落盘时声称「已自动归档」「已写入问题库」。
5. 最终答复**末尾必须**有一行：`📁 已归档：docs/questions/…`（续写注明「问答 N」）或 `📁 未归档：<原因>`。

完整勾选表：`references/agent-completion-gate.md`。

### 0.3 常见失效与纠正

| 错误做法 | 正确做法 |
|----------|----------|
| Skill 在列表里 = 已完成归档 | 同一回合内 `Write`/`StrReplace` 落盘 |
| 只口头回答项目问题 | 先读 `every-turn-protocol.md` → 写文件 → 再回复用户 |
| 等用户说「归档一下」才写 `docs/questions/` | § 0.0 启用后**每轮**项目 Q&A 默认归档 |
| 认为用户没问 doc-maintenance 就不用写盘 | 加载 Skill 后**任意**本项目技术问都要写盘 |
| 未读 400 行 SKILL 就跳过 § 七 | **首读** `every-turn-protocol.md`，不必读完全文 |
| 回合结束先回复用户、下轮再归档 | **禁止**；须**同一回合**先落盘再发最终答复 |
| 修复 Skill 但不记录当次 Q&A | 修复与说明本次修复的问答一并 § 七归档 |

### 0.5 v1.10.0 相对 v1.9.0 的变更

| 变更 | 说明 |
|------|------|
| 移除 `.cursor/rules/` | 原 `doc-maintenance-qna-archive.mdc` 内容并入本 Skill 文首「问答自动归档」；**仅安装 Skill 即可** |
| `description` | 显式写「无需单独 Rule」 |
| § 0.0 | 生效信号仅保留 Skill / 协议，不再引用 Rule |

### 0.6 v1.9.0 相对 v1.8.0 的修复点

v1.8.0 已在 § 0.0 写明「加载即归档」，但 Agent 仍常**只答不写盘**——根因是 Skill 过长、归档规则埋在 § 七。v1.9.0：

| 变更 | 说明 |
|------|------|
| `references/every-turn-protocol.md` | 独立 ≤60 行协议；**答复前必读** |
| `SKILL.md` 文首「首读」 | 强制顺序：读协议 → 写文件 → 回复 |
| `description` | 显式写「先 Write 再回复」 |
| § 0.0 | 第一步改为读 `every-turn-protocol.md` |

### 0.4 与 § 一 的关系

- **§ 一** 在**首次写 docs 结构**或**大改文档**前执行。
- **§ 零 + § 七** 在**每次**项目相关答复后执行；`docs/` 已存在时**不得**因跳过 § 一 而跳过归档。

---

## 一、每次执行的前置检查（强制）

在写/改任何文档之前，按序执行：

### 1.1 检查 `docs/` 是否存在

在项目**根目录**执行 `list_dir`（或等价）：

| 结果 | 动作 |
|------|------|
| **无 `docs/`** | 进入 [§ 二、首次初始化流程](#二首次初始化流程) |
| **有 `docs/`** | 继续 1.2 |

### 1.2 检查是否已初始化

读取 `docs/meta/doc-maintenance-state.json`（路径以 profile 为准）：

| 结果 | 动作 |
|------|------|
| 文件存在且 `initialized: true` | 继续 1.3 |
| 文件不存在或 `initialized: false` | 进入 [§ 二、首次初始化流程](#二首次初始化流程) |

### 1.3 校验目录架构是否与 Skill 一致

对照 `references/generic-conventions.md` § 标准文档分层，检查 **必需目录** 是否齐全（profile 可增减 optional）：

```text
docs/
├── README.md
├── meta/
├── overview/
├── guides/
├── architecture/
├── questions/          # 问题记录总库（主副本）
├── modules/
└── history/
```

| 结果 | 动作 |
|------|------|
| 架构一致 | **跳过初始化**，进入 [§ 三、常规启动顺序](#三常规启动顺序) |
| 架构不一致（缺目录/缺 state） | 视为未完整初始化 → 询问用户是否 **补全/重新对齐** 架构（见 § 二） |

**已初始化且架构一致时，不得重复执行全量初始化。**

---

## 二、首次初始化流程

### 2.1 询问用户

> 检测到本项目文档库尚未按 doc-maintenance 规范初始化（或缺少 `docs/`）。**是否现在初始化？**（是 / 否）

用户选 **否** → 仅回答当前问题，不创建目录。  
用户选 **是** → 继续 2.2。

### 2.2 扫描项目模块

按 profile `module_discovery` 规则扫描源码（默认见 generic-conventions § 模块发现）：

1. 列出顶层模块/包/目录
2. 按职责归入 **category**（profile 映射表；无映射时用 `misc` 或询问用户）
3. 输出拟议的 `docs/modules/{category}/` 结构供用户确认（有歧义时询问）

### 2.3 创建目录与文件

创建标准分层（`templates/` 与 generic-conventions 一致）：

| 路径 | 内容 |
|------|------|
| `docs/README.md` | 总索引（可基于 `templates/docs-readme.template.md`） |
| `docs/meta/conventions.md` | 规范（可从 profile 展开） |
| `docs/meta/doc-maintenance-state.json` | 初始化标记 |
| `docs/meta/question-bank-*.md` | 空问题库骨架 |
| `docs/questions/` | 问题记录总库 + `README.md`（模板：`docs-questions-readme.template.md`） |
| `docs/overview/`、`guides/`、`architecture/` | 各放占位 README 或 profile 指定文件 |
| `docs/modules/{category}/` | **每个 category 必须有 `README.md`**，说明该文件夹代表什么模块层/领域 |
| `docs/modules/{category}/questions/` | 问题记录目录 + `README.md`（模板：`category-questions-readme.template.md`） |
| `docs/modules/{category}/{module}.md` | 每个扫描到的模块一份初始 doc（用 `module-doc.template.md`） |
| `docs/history/README.md` | 历史记录说明 |
| `docs/history/minor/`、`major/`、`consolidated/`、`archive/` | 空目录占位 |

**category 的 `README.md`** 必须写清：分类 ID、中文标签、包含哪些源码模块、与架构层关系。模板：`templates/category-readme.template.md`。

### 2.4 写入 state

更新 `doc-maintenance-state.json`：

```json
{
  "initialized": true,
  "initialized_at": "YYYY-MM-DD",
  "schema_version": "1.0",
  "categories": ["ui", "agent", "..."],
  "pending_minor_count": 0,
  "last_consolidation_at": null
}
```

初始化完成后进入 § 三。

---

## 三、常规启动顺序

| 顺序 | 文件 |
|------|------|
| 1 | `references/project-profile.md` |
| 2 | `references/generic-conventions.md` |
| 3 | `docs/meta/doc-maintenance-state.json` |
| 4 | `docs/meta/conventions.md`（若有） |
| 5 | `docs/README.md` |
| 6 | 问题库（profile 路径） |

---

## 四、编写 / 修订流程

```text
前置检查（§ 一）→ 读 profile + state
  → 判断：新建 / 修订 / 模块增删改 / 问答沉淀
  → 若模块增删改或大范围文档重构 → § 五 历史记录
  → 按 profile 落盘 + 更新 README 索引
  → 小改动 → 可选记入 history/minor/（见 § 五）
  → 项目相关问答 → § 六 + § 七 自动归档（无需询问）
```

---

## 五、历史记录（`docs/history/`）

### 5.1 何时询问「是否记录历史」

完成下列 **任一** 操作后，**必须询问**（禁止静默写入）：

| 类型 | 示例 |
|------|------|
| **大范围改动** | 文档库重组、多 category 迁移、规范重写 |
| **系统模块增删改** | 新增/删除/重命名源码包或顶层模块、模块合并拆分 |

询问话术：

> 本次变更属于文档/模块 **大范围改动**。是否写入 **历史记录**？（是 / 否）

用户选 **是** → 在 `docs/history/major/` 新建 `YYYY-MM-DD-{slug}.md`（模板：`history-major.template.md`）。

### 5.2 小更新（minor）

单次小修订（单模块 doc、索引修正、少量段落）可写入 `docs/history/minor/`，文件名 `YYYY-MM-DD-{slug}.md`（模板：`history-minor.template.md`）。

每新增 1 条 minor，`doc-maintenance-state.json` 的 `pending_minor_count` +1（或 re-scan `minor/` 目录计数）。

### 5.3 小更新整合（≥ 10 条）

当 **未整合** 的 minor 记录 **≥ 10** 时，**必须询问**：

> 当前有 {N} 条未整合的小更新历史。**是否整合为一条总历史记录？**（是 / 否）

- 用户选 **否** → 不整合，继续累积
- 用户选 **是** → 执行 5.4

### 5.4 整合执行步骤

1. 新建或 **编辑** `docs/history/consolidated/YYYY-MM-DD-batch-{NNN}.md`（模板：`history-consolidated.template.md`）
2. 将全部待整合 minor 的**正文合并**进该 consolidated 文档（保留日期与 slug 索引）
3. 将已合并的 minor 文件 **移动** 到 `docs/history/archive/minor/`（勿直接删除）
4. 重置 `pending_minor_count` 为 0，更新 `last_consolidation_at`
5. 更新 `docs/history/index.md` 追加本条 consolidated 链接

**整合前必须得到用户明确肯定答复；不得自动整合。**

---

## 六、问题库工作流（摘要索引）

与 [§ 七](#七问题记录模块独立归档) 联动：**项目相关 Q&A 归档时，同步自动写入**问题库摘要，无需单独询问。

| 写入目标 | 条件 |
|----------|------|
| **分模块库** `question-bank-modules.md` | 默认；问题主要涉及某一 category |
| **总览库** `question-bank-overview.md` | 跨多 category、产品流程、全局规范类 |
| **两者** | 既跨模块又涉及具体实现细节 |

模板：`question-overview.template.md` / `question-module.template.md`。摘要 1～3 句 + **链接优先指向 `docs/questions/` 总库**（§ 七）。续写同一文件时更新原条目，不新增重复 Q 号。

---

## 七、问题记录（总库 + 模块同步）

用户就**本项目**提问且已给出实质性答复时，**自动**归档完整问答，**无需询问用户**。  
**§ 0.0 已生效时**：下列「应归档」为**默认假定成立**；仅当命中「不归档」表时才跳过。

### 7.0 自动归档判定

**应自动归档**（§ 0.0 下，对本仓库的实质技术答复**默认满足**；下列仅为典型场景）：

| 类型 | 示例 |
|------|------|
| 源码 / 架构 | ReAct 如何退出、Agent 循环、模块职责 |
| 文档 / 配置 | 执行模式对比、`.hancli` 目录、模块 doc 内容 |
| CLI / 使用 | `/plan`、`/team`、HITL、工具行为 |
| 实现细节 | 某类如何工作、关键代码在哪、数据流 |

**不归档**（满足任一即跳过）：

| 类型 | 示例 |
|------|------|
| 与项目无关 | 通用 Java 语法、外部框架、非本仓库话题 |
| 用户明确排除 | 「不要归档」「仅回答」「不用记录」 |
| 无实质解答 | 仅确认、寒暄、无法回答的追问 |
| 纯执行指令 | 「帮我改 X 文件」且未形成可复用的 Q&A 结论 |

**category 判定**：按问题涉及的**主要源码模块**映射（见 profile `category 映射`）；跨类以主模块为准；无法判定时仅写总库，**不中断归档**。

### 7.1 目录与命名（双层结构）

```text
docs/questions/                              ← 主副本（先写这里）
├── README.md
├── 01-react模式与循环-20260529.md
└── 02-工具注册与使用-20260529.md

docs/modules/{category}/questions/           ← 模块副本（可映射时同步）
├── README.md
└── 01-react模式与循环-20260529.md         ← 与总库同名
```

| 项 | 规则 |
|----|------|
| **主路径** | `docs/questions/{NN}-{主题}-{YYYYMMDD}.md` |
| **模块路径** | 判定可映射 category 后，**同文件名**写入 `docs/modules/{category}/questions/` |
| **序号 NN** | 在 **`docs/questions/`** 内递增；模块侧沿用总库 NN，不单独编号 |
| **连续合并** | **同日**且主题连续 → 续写总库同一文件（`## 问答二`…），并**同步更新**模块副本 |
| **主题** | 简短中文（如 `react模式与循环`、`工具同步调用`） |
| **日期** | 答复落盘日 `YYYYMMDD` |
| **模板** | 新建：`question-record.template.md` / `question-record-multi.template.md`；续写：`question-record-continued-section.template.md` |

**链接修正**：总库内链指向 `../modules/`、`../guides/`、`../meta/`；模块副本内链指向 `../{module}.md`、`../../../questions/`（总库）、`../../../meta/`。

### 7.2 单条 / 合并记录结构

**新建单条**：元数据表 + `## 一、问题` / `## 二、答复` / `## 三、关键代码`（见 `question-record.template.md`）。

**合并多篇**（同日连续）：顶栏 `问答条数`；正文 `## 问答一`、`## 问答二`…（见 `question-record-multi.template.md`）。

**续写**：文末追加 `## 问答{N}`（`question-record-continued-section.template.md`），更新顶栏 `问答条数`。

元数据表建议含：`总库正文` / `模块副本` 互链（落盘时按所在目录填相对路径）。

### 7.3 连续问答判定（归档前必查）

落盘前读取 **`docs/questions/`**，取**当日**（`YYYYMMDD` = 今天）**序号最大**的 `NN-主题-YYYYMMDD.md` 作为候选锚点文件（**不再**仅扫描 module 目录）。

**续写同一文件**（须**同时满足**）：

| # | 条件 |
|---|------|
| 1 | 总库存在锚点文件，且文件名日期 = **今天** |
| 2 | 锚点与当前问题 **同一 category**（若可判定） |
| 3 | **主题连续**：同一模块/同一概念 |
| 4 | **方向未偏离**：延伸、澄清或深入；非跳到无关模块 |

**新建文件**（任一满足即新建，在总库分配下一 `NN`）：

- 当日无锚点文件
- 换 category、换核心主题
- 与上一问无明显承接关系

### 7.4 工作流（每次解答后）

与 [§ 零](#零回合结束强制门cursor--agent-最高优先级) 绑定；**未执行步骤 4～7 不得结束回合**。

1. **先完成解答**（检索源码、给出准确答复）。
2. **判定** § 7.0 是否应归档 → **否**则回复末 `📁 未归档：…` 后结束。
3. **判定** § 7.3 续写总库锚点 or 新建。
4. **自动落盘总库** → **判定 category** → 可映射则**同步模块副本**（同内容、修正链接）。
5. **同步** § 六（续写时**更新**原有问题库条目，不重复新建 Q 号）。
6. **更新** `docs/questions/README.md`；若写入模块副本，更新对应 `modules/{category}/questions/README.md`。
7. **最终答复末尾** `📁 已归档：…`；续写时注明「已续写至 …（问答 N）」。

### 7.5 落盘步骤

**路径 A — 续写当日锚点文件**

1. 读取总库锚点全文，文末追加 `## 问答{N}` 节。
2. 更新顶栏 `问答条数`（+1）。
3. **同步**同文件至已存在的模块副本（若 category 可映射且副本已存在）。
4. 更新总库与模块 `README.md` 该行摘要；§ 六 更新条目。

**路径 B — 新建文件**

1. 确认 `docs/questions/` 与 `README.md` 存在（缺失则创建）。
2. 扫描 **`docs/questions/`** 取下一 `NN`；生成 `{NN}-{主题}-{YYYYMMDD}.md`。
3. 写入总库；**判定 category**：
   - 可映射 → 写入 `docs/modules/{category}/questions/` **同名文件**（修正内链）；更新模块 `questions/README.md`。
   - 不可映射 → 仅总库；元数据 `模块副本` 留空或注明「无」。
4. 总库 `README.md` 追加一行；§ 六 新增摘要 + **链接指向总库路径**。

### 7.6 与模块文档、问题库的分工

| 载体 | 何时用 |
|------|--------|
| `modules/{category}/{module}.md` | 正式规格、行为说明（非单次问答） |
| `docs/questions/*.md` | 问答归档**主副本**；同日连续合并为一文件多节 |
| `modules/{category}/questions/*.md` | 可映射 category 时的**模块侧副本**（与总库同名同步） |
| `meta/question-bank-*.md` | 跨会话快速检索的短摘要；**链接优先指向总库** |

---

## 八、禁止事项

- 不处理 Skill 编写、Skill 复制、`.hancli/skills/` 目录变更（除非用户**明确**要求改 doc-maintenance 且与 docs 初始化无关）
- **不得**在应归档时仅口头答复、不写 `docs/questions/`（§ 零、§ 7.4）
- **不得**在未落盘时声称「已自动归档」
- 不跳过初始化检查与历史记录询问（**历史记录仍须询问**；**问题记录/问题库除外，项目相关 Q&A 自动写入**）
- 已初始化且架构一致时不重复全量初始化
- 不自动整合 minor 历史（须用户确认）
- 不修改 `templates/*.template.md` 原文件

---

## 九、复制到新项目

1. 克隆整个 `doc-maintenance/` 到 `.cursor/skills/`（HANCLI CLI 如需加载，另复制到 `.hancli/skills/`）。
2. 确认 `references/every-turn-protocol.md` 一并复制（v1.9.0+ 每回合必读协议）。
3. 仅改 `references/project-profile.md`；`/skill reload` 或重启 Agent 会话。
4. 新项目首次 load 时走 § 一、§ 二 自动检测。

**无需**再复制 `.cursor/rules/`（v1.10.0 起归档强制逻辑已全部在本 Skill 内）。

详见 `README.md`。
