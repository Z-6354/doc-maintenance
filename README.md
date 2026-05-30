# 文档维护 Skill（doc-maintenance）

可复用的**项目内**文档维护 Skill。通用流程在 `SKILL.md`；项目差异在 `references/project-profile.md`。

## 触发范围

| 会触发 | 不会触发 |
|--------|----------|
| 本项目 `docs/` 编写/初始化/修订 | Skill 本身的编写或复制 |
| 源码模块对应文档 | `.hancli/skills/` 目录维护 |
| 文档历史、问题库 | IDE / Cursor 配置 |

## 首次运行

1. 检查根目录是否有 `docs/`
2. 读取 `docs/meta/doc-maintenance-state.json`
3. 校验目录架构 → 已初始化则**跳过**；否则询问是否初始化
4. 初始化时扫描源码模块 → 创建 `modules/{category}/` + 各 category 的 `README.md`

## 历史记录

- 大范围 / 模块增删改 → 询问 → `history/major/`
- 小更新 → `history/minor/`；≥10 条 → 询问整合 → `history/consolidated/`

## 问题记录

- 项目相关 Q&A → **自动**归档；**同日连续**、主题连贯 → **合并入同一文件**（`## 问答一/二/…`）
- 同步写入 `meta/question-bank-*.md` 摘要；**无需询问用户**
- 归档后一句话告知路径；用户说「不要归档」或与项目无关时跳过

## 复制到新项目

复制整个目录到目标项目的 `.hancli/skills/doc-maintenance/`（或 `~/.hancli/skills/doc-maintenance/`），只改 `references/project-profile.md`，执行 `/skill reload`。

### 从 Git 安装

```bash
# 项目级（推荐）
git clone <本仓库 URL> .hancli/skills/doc-maintenance

# 用户级（所有项目可用）
git clone <本仓库 URL> ~/.hancli/skills/doc-maintenance
```

安装后编辑 `references/project-profile.md` 指向目标项目的 `docs/` 结构与模块映射。

## 仓库说明

本目录为 **独立 Git 仓库**，与 HANCLI 主仓库分开维护；主项目 `.gitignore` 已排除本路径，避免嵌套仓库冲突。

