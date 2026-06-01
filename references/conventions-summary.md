# 文档规范摘要

> 完整版：`docs/meta/conventions.md`  
> Skill：`references/project-profile.md` + `generic-conventions.md`

## 触发范围

**仅本项目 docs**；不处理 Skill 安装到其它仓库、用户级 Skill 管理。  
**§ 0.0**：Skill 加载后，用户对 HANCLI 的普通技术问**默认归档**，无需用户说「归档」。

## 启动检查

1. 根目录有 `docs/`？
2. `docs/meta/doc-maintenance-state.json` → `initialized: true`？
3. 目录架构与 Skill 一致？→ **是则跳过初始化**

## 历史

- `history/minor/` 小更新；≥10 条询问整合
- `history/major/` 大范围（须询问）
- `history/consolidated/` 整合结果

## 问题库

项目相关 Q&A **自动**写入问题库摘要 + 问题记录，**无需询问**。

## 问题记录

**§ 零**：Cursor 不代写；Agent **同一回合**须写 `docs/questions/`（见 `agent-completion-gate.md`、`.cursor/rules/doc-maintenance-qna-archive.mdc`）。  
完整问答先写总库 `docs/questions/{NN}-…`，可映射时同步 `docs/modules/{category}/questions/` 同名文件。**同日连续**、主题未偏离的问答续写同一文件。回复末 `📁 已归档：…`。用户说「不要归档」或与项目无关时跳过。
