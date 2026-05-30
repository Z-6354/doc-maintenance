---
name: doc-maintenance
description: |
  当前仓库 docs/ 文档库的编写、初始化、修订与历史归档。仅处理本项目内文档与源码模块说明，不处理 Skill 安装/编写、IDE 或项目外工具链。
  触发：整理 docs、写/改模块说明、文档初始化、模块增删改后的文档同步、文档历史记录、问题库入库、问题记录自动归档（项目相关 Q&A；同日连续问答合并入同一文件）。
version: "1.5.0"
author: HanRuo
tags: [docs, markdown, maintenance, reusable]
---

# 文档维护（doc-maintenance）

**范围**：仅 **当前项目仓库** 内的 `docs/` 与源码模块对应文档。  
**不触发、不处理**：Skill 本身的编写/复制、`.hancli/skills/` 目录维护、用户级 Skill、Cursor/IDE 配置、与项目文档无关的外部工具。

通用流程见 `references/generic-conventions.md`；项目差异见 `references/project-profile.md`。

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

模板：`question-overview.template.md` / `question-module.template.md`。摘要 1～3 句 + 链接到 § 七 的完整记录文件。**续写同一文件时更新原条目，不新增重复 Q 号。**

---

## 七、问题记录模块（独立归档）

用户就**本项目**提问且已给出实质性答复时，**自动**将完整问答归档到对应 category 的 `questions/` 子目录，**无需询问用户**。

### 7.0 自动归档判定

**应自动归档**（满足任一即可）：

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
| 纯执行指令 | 「帮我改 X 文件」且未形成可复用的 Q&A 结论（改完代码即可，不必建档） |

**category 判定**：按问题涉及的**主要源码模块**映射（见 profile `category 映射`）；跨类以主模块为准；无法判定时归入最相关的 category，**不中断归档**。

### 7.1 目录与命名

```text
docs/modules/{category}/questions/
├── README.md                      # 本分类问题记录索引
├── 01-rag相关-20260529.md
├── 02-cli补全-20260530.md
└── ...
```

| 项 | 规则 |
|----|------|
| **路径** | `docs/modules/{category}/questions/`（profile 的 category 之一） |
| **文件名** | `{NN}-{主题}-{YYYYMMDD}.md`；**同日连续问答**续写同一文件，不新增序号 |
| **序号 NN** | 仅**新建文件**时递增；续写时沿用当日最近文件的 NN |
| **主题** | 简短中文，概括问题领域（如 `rag相关`、`agent执行流程`） |
| **日期** | 答复落盘日 `YYYYMMDD`（如 `20260529`） |
| **模板** | `templates/question-record.template.md` |

| **模板** | 新建：`question-record.template.md` 或 `question-record-multi.template.md`；续写：`question-record-continued-section.template.md` |

**category 判定**：见 § 7.0。

### 7.2 单条 / 合并记录结构

**新建单条**（首问或非连续）：元数据表 + `## 一、问题` / `## 二、答复` / `## 三、关键代码`（见 `question-record.template.md`）。

**合并多篇**（同日连续）：文档顶栏 `问答条数`；正文用 `## 问答一`、`## 问答二`…，每节含 `### 问题` / `### 答复` / `### 关键代码`（见 `question-record-multi.template.md`）。

**续写已有合并文档**：在文末追加 `## 问答{N}` 节（`question-record-continued-section.template.md`），更新顶栏 `问答条数`。

### 7.3 连续问答判定（归档前必查）

落盘前读取 `docs/modules/{category}/questions/`，取**当日**（`YYYYMMDD` = 今天）**序号最大**的 `NN-主题-YYYYMMDD.md` 作为候选锚点文件。

**续写同一文件**（须**同时满足**）：

| # | 条件 |
|---|------|
| 1 | 存在锚点文件，且文件名日期 = **今天** |
| 2 | 锚点与当前问题 **同一 category** |
| 3 | **主题连续**：同一模块/同一概念（如均为 ReAct、均为 `Agent` 循环）；非明显换题 |
| 4 | **方向未偏离**：当前问是对上一问的延伸、澄清或深入（含指代上一轮的「也就是说」「继续」「刚才」等）；非跳到无关模块 |

**新建文件**（任一满足即新建，分配下一 `NN`）：

- 当日无锚点文件
- 换 category、换核心主题（如 ReAct → RAG）
- 与上一问无明显承接关系

主题短语：续写时可不改文件名；新建时取能涵盖连续问答的**略宽主题**（如 `react模式与循环`）。

### 7.4 工作流（每次解答后）

1. **先完成解答**（检索源码、给出准确答复）。
2. **判定** § 7.0 是否应归档 → **否**则结束。
3. **判定** § 7.3 续写锚点文件 or 新建。
4. **自动落盘** + **同步** § 六（续写时**更新**原有问题库条目摘要，不重复新建 Q 号）。
5. **告知用户**路径；续写时注明「已续写至 …（问答 N）」。

### 7.5 落盘步骤

**路径 A — 续写当日锚点文件**

1. 读取锚点文件全文，在文末追加 `## 问答{N}` 节（模板 `question-record-continued-section.template.md`）。
2. 更新文档顶栏 `问答条数`（+1）。
3. **不**新增 `NN` 文件名；**不**在 `README.md` 新增行，仅更新该行「问题摘要」（合并简述多条）。
4. 更新 § 六 中指向该文件的条目（合并问题与结论，一条摘要链到同一文件）。

**路径 B — 新建文件**

1. 确认 `questions/` 与 `README.md` 存在。
2. 扫描目录取下一 `NN`；生成 `{NN}-{主题}-{YYYYMMDD}.md`。
3. 若预判本会话可能连续多轮，首条可用 `question-record-multi.template.md`（`## 问答一` 结构）。
4. `README.md` 追加一行；§ 六 新增一条摘要 + 链接。

### 7.6 与模块文档、问题库的分工

| 载体 | 何时用 |
|------|--------|
| `modules/{category}/{module}.md` | 正式规格、行为说明（非单次问答） |
| `modules/{category}/questions/*.md` | 问答归档；**同日连续**合并为一文件多节 |
| `meta/question-bank-*.md` | 跨会话快速检索的短摘要 |

---

## 八、禁止事项

- 不处理 Skill 编写、Skill 复制、`.hancli/skills/` 目录变更（除非用户**明确**要求改 doc-maintenance 且与 docs 初始化无关）
- 不跳过初始化检查与历史记录询问（**历史记录仍须询问**；**问题记录/问题库除外，项目相关 Q&A 自动写入**）
- 已初始化且架构一致时不重复全量初始化
- 不自动整合 minor 历史（须用户确认）
- 不修改 `templates/*.template.md` 原文件

---

## 九、复制到新项目

复制整个 `doc-maintenance/`，仅改 `references/project-profile.md`，`/skill reload`。新项目首次 load 时走 § 一、§ 二 自动检测。

详见 `README.md`。
