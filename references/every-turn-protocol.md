# 每回合执行协议（强制 · 先于回复用户）

> **本文件 ≤ 60 行，Agent 答复本项目问题前必须先读。**  
> 配合 `SKILL.md` § 零、§ 七；Agent 兼容见 `agent-runtime.md`；检查清单见 `agent-completion-gate.md`。

---

## 1. 何时生效（无需用户说「归档」）

下列 **任一** 成立 → 本回合 **默认须归档**（直至用户说「不要归档」）：

- 宿主 Agent 已加载 `doc-maintenance`（skill 列表 / system prompt / 等价机制）
- 用户显式引用本 Skill（`@doc-maintenance` 或粘贴路径）或本会话已读 `SKILL.md` / 本文件

**禁止**因用户未出现「归档 / docs/questions / doc-maintenance」而跳过。

---

## 2. 执行顺序（不可颠倒）

| 步 | 动作 | 禁止 |
|----|------|------|
| **1** | 检索源码 / 文档，**构思完整答复** | — |
| **2** | 调用 **Write / StrReplace**（或宿主等价写文件工具）落盘 `docs/questions/`（§7.3～7.5） | 未写文件就向用户发最终答复 |
| **3** | 可映射 category → 同步 `docs/modules/{category}/questions/` 同名文件；更新 README + 问题库 § 六 | 把归档推到「下一轮」 |
| **4** | **最后**发送用户可见答复；末尾一行 `📁 已归档：docs/questions/…` 或 `📁 未归档：<§7.0 原因>` | 口头声称「已自动归档」 |

**任何 Agent 运行时均不会**在后台代写 `docs/questions/`；「自动归档」= Agent **同回合**写文件，且**无需用户确认**。

---

## 3. 什么算「应归档」

| 应归档（默认） | 不归档 |
|----------------|--------|
| 说明 MCP / CLI / 架构 / Skill / 配置 | 与当前项目仓库无关 |
| 如何 / 为什么 / 在哪实现 | 纯寒暄、无实质答复 |
| 修复 doc-maintenance → **必须**归档当次 Q&A | 用户明确「不要归档」「仅回答」 |

---

## 4. 落盘速查

1. 读 `docs/questions/`：取**当日**最大 `NN-主题-YYYYMMDD.md` → §7.3 决定**续写**或**新建**。
2. **先写总库** → 模板见 `../templates/question-record*.md`。
3. category 可映射（见 `project-profile.md`）→ **同名**写入模块 `questions/`。
4. 更新 `docs/questions/README.md`；续写时更新顶栏 `问答条数`。

详细规则：`../SKILL.md` § 七。
