# 通用文档维护框架

> 与项目无关的可复用规则。项目差异见同目录 `project-profile.md`；两者冲突时 **project-profile 优先**。

---

## 一、适用前提

- 仓库存在文档根目录（默认 `docs/`，可在 project-profile 覆盖）。
- 维护者希望：**分类清晰、命名一致、标题规范、问答可沉淀**。

本框架不绑定语言、构建工具或目录命名；落地时读取项目 profile 即可。

---

## 二、标准文档分层（推荐）

任意项目可映射到以下 **6 层**（目录名可改，职责不变）：

| 层级 | 职责 | 典型目录名 |
|------|------|------------|
| 索引 | 总入口、阅读顺序 | `docs/README.md` |
| 元数据 | 规范、状态、问题库 | `docs/meta/` |
| 概览 | 一页纸、能力、配置速查 | `docs/overview/` |
| 指南 | 跨模块专题、选型 | `docs/guides/` |
| 架构 | 分层、数据流、全局图 | `docs/architecture/` |
| 模块 | 与代码模块一一对应 | `docs/modules/{category}/` |
| 历史 | 变更归档与整合 | `docs/history/` |

### 2.1 初始化判定

**已初始化** 需同时满足：

1. 存在 `docs/meta/doc-maintenance-state.json` 且 `"initialized": true`
2. 存在 § 二 表中 **必需** 目录：`meta/`、`overview/`、`guides/`、`architecture/`、`modules/`、`history/`
3. 存在 `docs/README.md`

三者一致 → **跳过** 全量初始化，仅做增量维护。

### 2.2 模块发现（初始化时）

| 项目类型 | 扫描路径 | 模块粒度 |
|----------|----------|----------|
| Java/Maven | `src/main/java/{basePackage}/` 一级子目录 | 包名 → module doc |
| Node/TS | `src/` 或 `packages/` 顶层 | 目录名 |
| Go | 根目录下含 `go.mod` 的包目录 | 目录名 |
| 其他 | profile `module_discovery.paths` | 由 profile 指定 |

扫描结果经 profile **category 映射表** 归入 `docs/modules/{category}/`；无法映射时询问用户或归入 `misc/`。

每个 **category 目录** 必须有 `README.md`，说明该文件夹代表的架构层/领域（模板 `category-readme.template.md`）。

**模块分类**（`modules/` 下子目录）由 project-profile 定义，常见模式：

- 按架构层：`ui` / `core` / `extension` / `infra`
- 按领域：`frontend` / `backend` / `ops`
- 扁平：仅 `modules/*.md`（小项目）

---

## 三、命名与标题（可配置）

| 项 | 默认约定 | 可覆盖项（profile） |
|----|----------|---------------------|
| 文件名 | 小写英文 kebab-case | `naming.file_case` |
| H1 | `{中文标题}（{模块标识}）` | `headings.h1_pattern` |
| H2 | `## 一、` `## 二、` … | `headings.h2_style: chinese-numeral` 或 `decimal` |
| 禁止 | 中文文件名、随意 `00-` 前缀 | — |

---

## 四、模块文档模板（通用）

```markdown
# {中文定位}（{模块标识}）

| 项 | 值 |
|----|-----|
| 分类 | `{category}` · {分类中文名} |
| 源码 | `{package_or_path}` |

---

## 一、模块定位
## 二、职责 / 关键类
## 三、流程
## 四、配置（如有）
## 五、依赖关系
## 六、扩展指南
## 七、测试
## 八、源码清单（可选）
```

`源码` 行按项目填写：Java 包名、Python 模块路径、Go package 等。

---

## 五、内链规则

1. **同目录**：`[peer.md](peer.md)`
2. **跨目录**：相对路径，如 `[tool.md](../extension/tool.md)`
3. **禁止**：profile 中 `legacy_paths` 列出的已废弃路径
4. **代码互引**：文档 → 源码用反引号标识符；源码 → 文档在 profile 的 `code_doc_sync` 说明

---

## 六、问题库（双库模型）

| 库 | 用途 | 默认路径（可 profile 覆盖） |
|----|------|------------------------------|
| **总览库** | 跨模块、流程、规范、工具链 | `docs/meta/question-bank-overview.md` |
| **分模块库** | 按 category 归档实现细节 | `docs/meta/question-bank-modules.md` |

### 6.1 入库流程（所有项目统一）

1. 解答完成后 **归纳**：问题（一句）+ 结论（1～3 句）+ 关联文档/模块。
2. **项目相关 Q&A**：**自动写入**问题记录 + 问题库摘要，**无需询问用户**（判定规则见 `SKILL.md` § 7.0）。
3. 用户明确说「不要归档」或问题与项目无关 → 跳过。
4. 结论已写入正式文档时，问题库只保留摘要 + 链接。

### 6.2 ID 规则

- 总览：`Q-YYYYMMDD-NNN`
- 分模块：`Q-{category}-NNN`（category 来自 project-profile）

### 6.3 问题记录模块（独立文件）

除双库摘要外，**完整问答**自动归档到各 category 下的 `questions/` 子目录（项目相关时，**无需询问**）：

| 项 | 默认约定 |
|----|----------|
| 路径 | `docs/modules/{category}/questions/` |
| 文件名 | `{NN}-{主题}-{YYYYMMDD}.md` |
| 连续合并 | **同日**且与锚点文件**主题连续** → 续写同一文件（`## 问答二`…），不递增 NN |
| 必含 | 时间、问题、答复、关键代码（含源码路径） |
| 模板 | `templates/question-record.template.md` |
| 索引 | 各 `questions/README.md` 维护记录列表 |

与 § 6.1 问题库**同一轮次自动同步**。详见 `SKILL.md` § 六–§ 七。

---

## 七、维护工作流（通用）

```text
1. 读取 project-profile → 若存在 docs/meta/conventions.md 再读项目规范
2. 判断任务类型：新建 / 修订 / 重组 / 问答沉淀
3. 按分层放入正确目录；更新 docs/README.md 索引
4. 改代码行为 → 同步模块 doc + 必要时概览/架构
5. 项目相关问答 → 自动归档（§ 6.1 / § 6.3），无需询问
6. 提交前对照 profile 的 maintenance_checklist
```

---

## 八、复制到新项目

1. 复制整个 `doc-maintenance/` 目录到：
   - 目标项目：`.hancli/skills/doc-maintenance/`，或
   - 用户全局：`~/.hancli/skills/doc-maintenance/`
2. 编辑 `references/project-profile.md`（必填）
3. 在目标项目创建 `docs/meta/conventions.md`（可选，人类可读规范）
4. 执行 `/skill reload`

仅需改 **project-profile** 与 **conventions**；`SKILL.md` 与 `generic-conventions.md` 保持不变。

---

## 十、历史记录（`docs/history/`）

```text
docs/history/
├── README.md           # 历史机制说明
├── index.md            #  chronological 索引
├── minor/              # 小更新（待整合）
├── major/              # 大范围变更（独立成篇）
├── consolidated/       # 小更新整合后的总记录
└── archive/minor/      # 已并入 consolidated 的 minor 原文归档
```

| 类型 | 触发 | 须询问用户 |
|------|------|------------|
| **major** | 模块增删改、文档库大范围重组 | 是 |
| **minor** | 单模块小修订、索引微调 | 可选记录；累积 ≥10 条未整合时 **须询问是否整合** |
| **consolidated** | 用户确认整合后 | 整合前必须询问 |

`docs/meta/doc-maintenance-state.json` 字段：

- `pending_minor_count`：待整合 minor 数量
- `last_consolidation_at`：上次整合日期

整合步骤：合并正文 → 写入 `consolidated/` → minor 移至 `archive/minor/` → 更新 state 与 `index.md`。

---

## 十一、触发范围（Skill description 对齐）

**应触发**：当前仓库 `docs/`、源码模块文档、文档初始化、文档历史、项目内 Q&A 入库。

**不应触发**：编写/复制 Skill 本身、`.hancli/skills/` 维护、IDE 配置、与项目 docs 无关的外部工具。

---

## 十二、模板文件

| 文件 | 用途 |
|------|------|
| `templates/module-doc.template.md` | 新建模块 doc |
| `templates/category-readme.template.md` | category 目录说明 |
| `templates/docs-readme.template.md` | 初始化 docs/README |
| `templates/question-overview.template.md` | 总览库新条目 |
| `templates/question-module.template.md` | 分模块库新条目 |
| `templates/question-record.template.md` | 问题记录独立文件 |
| `templates/question-record-multi.template.md` | 同日连续问答（多节结构） |
| `templates/question-record-continued-section.template.md` | 续写追加一节 |
| `templates/category-questions-readme.template.md` | category 下 questions/ 索引 |
| `templates/history-minor.template.md` | 小更新历史 |
| `templates/history-major.template.md` | 大范围变更历史 |
| `templates/history-consolidated.template.md` | minor 整合记录 |

复制模板后替换 `{占位符}`，勿改模板文件本身。
