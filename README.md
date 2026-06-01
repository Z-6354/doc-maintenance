# doc-maintenance

> 可复用的 **文档维护 Agent Skill** — 让 AI 按规范初始化、编写、归档项目 `docs/`，并自动沉淀问答知识。

**仓库**：<https://github.com/Z-6354/doc-maintenance>

[![Skill Version](https://img.shields.io/badge/skill-v1.9.0-blue)](SKILL.md)
---

## 简介

**doc-maintenance** 是一套与具体业务无关的文档维护流程，可安装到任意支持 Agent Skill 的项目（如 Cursor、Codex 等兼容 `SKILL.md` 的工具链）。

它针对常见问题：文档分散、命名不统一、模块说明与源码脱节、团队问答无法沉淀。Skill 将维护流程**结构化、可重复、可交给 Agent 执行**。

### 你能得到什么

| 能力 | 说明 |
|------|------|
| **文档库初始化** | 扫描源码模块，生成 `docs/` 分层目录与模块 doc 骨架 |
| **规范写作** | 统一标题层级、文件命名、模块元数据与内链约定 |
| **模块文档同步** | 源码增删改时，更新对应 `docs/modules/{category}/` |
| **问题自动归档** | 项目相关 Q&A 先写入 `docs/questions/` 总库，可映射模块时同步至 `modules/{category}/questions/` |
| **连续问答合并** | 同日、主题连贯的多轮追问合并为同一文档（`## 问答一/二`） |
| **变更历史** | minor / major / consolidated 三级归档，大范围改动须用户确认 |

---

## 适用场景

- 多模块源码项目（Java、TypeScript、Go、Python 等）
- 使用 **Cursor Agent** 或其他支持 Skill 的 AI 编程助手
- 希望 `docs/` 与代码结构对应、可持续维护
- 希望「问过的问题」形成可检索的知识库

**不适用**：与项目 docs 无关的闲聊、IDE 配置、Skill 安装脚本本身（本 Skill 只维护**目标项目**的 `docs/`）。

---

## 文档分层（默认）

```text
docs/
├── README.md                 # 总索引
├── meta/                     # 规范、问题库、初始化状态
├── overview/                 # 系统概览
├── guides/                   # 跨模块指南
├── architecture/             # 架构设计
├── questions/                # 问答归档总库（主副本，NN-主题-YYYYMMDD.md）
├── modules/
│   ├── {category}/           # 按项目自定义，如 frontend / backend / core
│   │   ├── README.md
│   │   ├── {module}.md
│   │   └── questions/        # 可映射 category 时的模块副本（与总库同名）
│   └── ...
└── history/                  # 文档变更历史
```

`{category}` 与模块扫描规则在 `references/project-profile.md` 中按项目配置，无固定分类名称。

---

## 快速安装

### 项目级（推荐）

```bash
cd your-project
git clone https://github.com/Z-6354/doc-maintenance.git .skills/doc-maintenance
```

实际路径取决于你的 Skill 加载约定，常见位置：

| 工具 / 约定 | 典型路径 |
|-------------|----------|
| 项目级 Skill | `.cursor/skills/doc-maintenance/`、`.skills/doc-maintenance/` |
| 用户级 Skill | `~/.cursor/skills/doc-maintenance/` 等 |

也可直接克隆到上表路径，例如：

```bash
git clone https://github.com/Z-6354/doc-maintenance.git ~/.cursor/skills/doc-maintenance
```

### 配置

1. 编辑 **`references/project-profile.md`**：项目名、文档根目录、模块发现路径、category 映射
2. （可选）在目标项目创建 `docs/meta/conventions.md` 作为人类可读规范
3. **复制** `.cursor/rules/doc-maintenance-qna-archive.mdc` 到目标项目（与 Skill 同仓库分发；`alwaysApply: true`，弥补 Cursor 不自动写盘）
4. 重新加载 Skill（如 `/skill reload` 或重启 Agent 会话）

### 首次运行

Agent 加载后会自动检查：

1. 是否存在 `docs/`
2. `docs/meta/doc-maintenance-state.json` 是否 `initialized: true`
3. 目录架构是否与规范一致

未初始化时会询问是否扫描源码并生成文档骨架。

---

## 仓库结构

```text
doc-maintenance/
├── SKILL.md                          # Agent 主指令
├── README.md                         # 本文件
├── references/
│   ├── generic-conventions.md        # 通用规则（一般不改）
│   ├── project-profile.md            # 项目差异配置（安装后必改）
│   └── conventions-summary.md        # 规范速查
└── templates/
    ├── module-doc.template.md
    ├── question-record.template.md
    ├── question-record-multi.template.md
    └── …
```

| 文件 | 职责 |
|------|------|
| `SKILL.md` | 前置检查、初始化、归档、禁止事项 |
| `project-profile.md` | **按目标项目改动的唯一入口** |
| `generic-conventions.md` | 跨项目复用的文档框架 |

---

## 核心工作流

### 文档编写 / 修订

```text
前置检查 → 读 profile → 判断新建/修订/模块变更
  → 落盘到正确分层 → 更新 README 索引
  → 大范围改动 → 询问是否写 history/major/
```

### 问答沉淀（自动）

```text
解答项目相关问题（同一回合内落盘，Cursor 不代写）
  → § 零 + agent-completion-gate.md
  → 判定是否归档（源码/架构/配置/用法等 → 是；无关话题 → 否）
  → 先写 docs/questions/ 总库；检查当日连续问答 → 续写或新建
  → 可映射 category → 同步 modules/{category}/questions/ 同名文件
  → 同步 question-bank 摘要（链接优先指向总库）
  → 回复末尾：📁 已归档：docs/questions/…
```

用户说「不要归档」时跳过。

---

## 复制到新项目 checklist

- [ ] 克隆或复制本仓库到项目的 Skill 目录
- [ ] 重写 `references/project-profile.md`（模块发现、category、废弃路径）
- [ ] 重新加载 Skill
- [ ] 首次对话触发初始化（或手动对齐已有 `docs/`）
- [ ] 确认 `docs/meta/doc-maintenance-state.json` 中 `initialized: true`

---

## 版本

当前 Skill 版本：**v1.8.0**（见 `SKILL.md` frontmatter）

| 版本 | 要点 |
|------|------|
| v1.8.0 | **§ 0.0 Skill 加载即默认归档**；用户无需说「归档」；Rule/检查清单同步强化 |
| v1.7.0 | § 零 回合结束强制门；`agent-completion-gate.md`；`.cursor/rules/doc-maintenance-qna-archive.mdc` |
| v1.6.0 | 新增 `docs/questions/` 总库；可映射 module 时同步 category/questions/ |
| v1.5.0 | 同日连续问答合并归档 |
| v1.4.0 | 项目相关 Q&A 自动写入，无需询问 |
| v1.3.0 | `questions/` 独立问题记录模块 |
| v1.2.0 | 文档历史 minor / major / consolidated |

---

## License

请根据你的发布策略添加 LICENSE 文件（如 MIT、Apache-2.0）。
