# Agent 运行时兼容

> 本 Skill 使用标准 `SKILL.md` frontmatter + Markdown 正文，**不绑定单一 IDE 或厂商**。  
> 项目差异见 `project-profile.md`；归档协议见 `every-turn-protocol.md`。

---

## 一、已验证 / 常见兼容 Agent

| Agent / 工具链 | Skill 典型安装路径 | 加载说明 |
|----------------|-------------------|----------|
| **Cursor** | `.cursor/skills/doc-maintenance/` | 项目级 Skill 自动发现；用户级 `~/.cursor/skills/` |
| **Claude Code** | `.claude/skills/doc-maintenance/` | 项目或用户 Skill 目录；依 Claude Code 版本配置 |
| **Codex** | `.codex/skills/doc-maintenance/` 或用户级等价路径 | 依 Codex / OpenAI CLI 约定 |
| **Hermes** | `.skills/doc-maintenance/` 或宿主配置的 skills 根 | 将本仓库克隆到宿主声明的 skills 目录 |
| **OpenCode / 其它 CLI Agent** | `.skills/`、`skills/`、宿主文档指定路径 | 保证 `SKILL.md` 在 skills 根下可被读取 |
| **HANCLI CLI** | `.hancli/skills/doc-maintenance/` | 与 Cursor 路径可并存；CLI 按自身 loader 读取 |

路径仅为**常见约定**；以各 Agent 官方文档为准。安装后确认宿主能读到 `SKILL.md` 与 `references/` 即可。

---

## 二、Skill 已生效的信号（§ 0.0）

下列 **任一** 成立 → 本会话默认启用问答归档（直至用户说「不要归档」）：

| 信号 | 说明 |
|------|------|
| **宿主已注入本 Skill** | 出现在系统 `available_skills`、工具列表、system prompt 或等价机制 |
| **用户显式引用** | `@doc-maintenance`、粘贴 SKILL 路径、或明确要求按 doc-maintenance 执行 |
| **本会话已读协议** | 读过 `every-turn-protocol.md` 或 `SKILL.md` 文首「首读」 |

**禁止**假设「只有 Cursor 才需要归档」或「只有用户说归档才写盘」。

---

## 三、「自动归档」在各 Agent 中的含义

| 误解 | 正解 |
|------|------|
| 对话结束由 IDE 后台写 `docs/questions/` | **否**；任何 Agent 均不会代写仓库文件 |
| Skill 出现在列表里 = 已归档 | **否**；须 Agent **同回合**调用写文件工具 |
| 只有 Cursor Rule / Hook 才能强制 | **否**；本 Skill 正文 + `every-turn-protocol.md` 即强制协议 |

写文件工具名因 Agent 而异（`Write` / `edit` / `create_file` 等），**语义相同**：落盘后再发用户可见最终答复。

---

## 四、安装到新项目（通用）

```bash
# 将 {SKILLS_DIR} 替换为上表对应路径，例如 .cursor/skills 或 .claude/skills
git clone https://github.com/Z-6354/doc-maintenance.git {SKILLS_DIR}/doc-maintenance
```

1. 编辑 `references/project-profile.md`（项目名、模块发现、category）
2. 重载 Skill 或重启 Agent 会话（Cursor：`/skill reload`；其它 Agent 依宿主文档）
3. 首次运行走 `SKILL.md` § 一、§ 二 检测 `docs/` 是否初始化

**无需**额外 Rule 文件（v1.10.0+ 强制逻辑已全部在 Skill 内）。
