# 项目配置（Project Profile）

> **本文件是项目差异的唯一入口**。复制 `doc-maintenance` Skill 到新项目时，主要改此文件。
> 通用流程见 [generic-conventions.md](generic-conventions.md)。

---

## 一、项目标识

| 项 | 值 |
|----|-----|
| 项目名称 | HANCLI |
| 文档根目录 | `docs/` |
| 权威规范 | `docs/meta/conventions.md` |
| 文档索引 | `docs/README.md` |
| 初始化状态 | `docs/meta/doc-maintenance-state.json` |

---

## 二、目录映射

```text
docs/
├── README.md
├── meta/
│   ├── conventions.md
│   ├── question-bank-overview.md
│   ├── question-bank-modules.md
│   └── doc-maintenance-state.json
├── history/
│   ├── README.md
│   ├── index.md
│   ├── minor/
│   ├── major/
│   ├── consolidated/
│   └── archive/minor/
├── overview/system-overview.md
├── guides/execution-modes.md
├── architecture/overview.md
├── questions/                  # 问题记录总库（主副本）
│   └── README.md
└── modules/
    ├── ui/
    │   └── questions/          # 问题记录（独立问答归档）
    ├── agent/
    │   └── questions/
    ├── extension/
    │   └── questions/
    └── infra/
        └── questions/
```

| 文档类型 | 路径模式 |
|----------|----------|
| 系统概览 | `docs/overview/system-overview.md` |
| 架构总览 | `docs/architecture/overview.md` |
| 模块文档 | `docs/modules/{category}/{module}.md` |
| 问题记录（总库） | `docs/questions/{NN}-{主题}-{YYYYMMDD}.md` |
| 问题记录（模块副本） | `docs/modules/{category}/questions/{NN}-{主题}-{YYYYMMDD}.md`（与总库同名，可映射时同步） |
| 问题总库索引 | `docs/questions/README.md` |
| 问题记录索引（category） | `docs/modules/{category}/questions/README.md` |
| 总览问题库 | `docs/meta/question-bank-overview.md` |
| 分模块问题库 | `docs/meta/question-bank-modules.md` |
| 初始化状态 | `docs/meta/doc-maintenance-state.json` |
| 历史索引 | `docs/history/index.md` |

---

## 三、模块发现（本项目）

| 项 | 值 |
|----|-----|
| 类型 | Java / Maven |
| 扫描路径 | `src/main/java/com/hancli/` |
| 粒度 | 一级子包（目录） |
| 排除 | `package-info.java` 所在根包 `com.hancli` 本身不单独成 module doc |

### category 映射规则

| 源码包 | category |
|--------|----------|
| cli, render, tui, image | `ui` |
| agent, llm, prompt, plan, memory, context | `agent` |
| tool, hitl, policy, mcp, skill, web, browser, rag | `extension` |
| config, snapshot, runtime, lsp, util | `infra` |

未命中映射的包 → 询问用户归入哪一 category 或新建 category。

---

## 四、模块分类（modules/ 子目录）

| category | 标签 | 模块 doc |
|----------|------|----------|
| `ui` | L1 · 交互与呈现 | cli, cli-jline, render, tui, image |
| `agent` | L2-L3 · 智能体与模型 | agent, llm, prompt, plan, memory, context |
| `extension` | L4 · 工具与安全扩展 | tool, hitl, policy, mcp, skill, web, browser, rag |
| `infra` | 基础设施 | config, snapshot, runtime, lsp, util |

新建模块 doc 时：`docs/modules/{category}/{module}.md`，并在 `docs/README.md` 对应小节登记。

---

## 五、标题与命名

| 项 | 本项目取值 |
|----|------------|
| 文件名 | kebab-case 英文 |
| H1 | `# {中文}（{module}）` |
| H2 | `## 一、` `## 二、` …（chinese-numeral） |
| 源码元数据列名 | `源码包`（Java：`com.hancli.*`） |

---

## 六、代码 ↔ 文档同步

| 源码位置 | 文档指向约定 |
|----------|--------------|
| `package-info.java` | 优先 `docs/modules/{category}/{module}.md`；根包 → `docs/overview/system-overview.md` |
| 行为/API 变更 | 同步对应模块 doc + 必要时 `system-overview.md` / `architecture/overview.md` |

---

## 七、已废弃路径（禁止在新内链中使用）

| 旧路径 | 新路径 |
|--------|--------|
| `docs/系统说明.md` | `docs/overview/system-overview.md` |
| `docs/执行模式对比.md` | `docs/guides/execution-modes.md` |
| `docs/modules/00-架构总览.md` | `docs/architecture/overview.md` |
| `docs/modules/{name}.md`（扁平） | `docs/modules/{category}/{name}.md` |

根目录保留上述中文文件的 **重定向 stub**。

---

## 八、常见任务路由

| 用户意图 | 更新目标 |
|----------|----------|
| 新增/改 API 或模块行为 | `docs/modules/{category}/{module}.md` |
| ReAct / Plan / Team | `docs/guides/execution-modes.md` |
| 全局架构 / 数据流 | `docs/architecture/overview.md` |
| 文档规范 / 分类 | `docs/meta/conventions.md` + `docs/README.md` |
| 重复 Q&A | 自动写入 `questions/` + 问题库；**同日连续**合并同一文件 |
| 文档/模块大范围变更 | `docs/history/major/`（须询问用户） |
| 小更新累积 ≥10 | 询问整合 → `docs/history/consolidated/` |

---

## 九、项目特有约定

- 历史 phase 文档：`paicli-main/paicli-main/docs/`，**不要**与 `docs/` 混写。
- Skill 加载顺序：JAR 内置 → `~/.hancli/skills/` → **本项目** `.hancli/skills/`（同名覆盖）。
- 语言/runtime：Java 21，入口 `com.hancli.cli.Main`。

---

## 十、维护检查清单

- [ ] 新 doc 位于 profile 定义的分层与 category
- [ ] 元数据表含 `分类`、`源码包`
- [ ] 内链无 `legacy_paths` 中的旧路径
- [ ] `docs/README.md` 索引已更新（新模块）
- [ ] 项目相关 Q&A 已自动写入问题记录与问题库（若适用）
- [ ] 必要时同步 `package-info.java` 文档路径

- [ ] 大范围变更已询问并记录 history（若用户同意）
- [ ] `pending_minor_count` 与 `history/minor/` 一致

---

## 十一、复制到其他项目时的改法

保留 `SKILL.md`、`generic-conventions.md`、`templates/` **不动**。

仅重写本文件（project-profile）：

1. `项目名称`、`文档根目录`
2. `modules/` 下的 **category** 列表与模块名
3. `legacy_paths`（若有迁移）
4. `code_doc_sync` 规则（如 Python 的 `__init__.py` docstring 指向）
5. `常见任务路由` 表

可选：在新项目创建 `docs/meta/conventions.md` 作为人类可读规范（内容可从本 profile 展开）。
