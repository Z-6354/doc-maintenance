# 文档规范摘要

> 完整版：`docs/meta/conventions.md`  
> Skill：`references/project-profile.md` + `generic-conventions.md`

## 触发范围

**仅本项目 docs**；不处理 Skill 安装/编写、IDE 配置。

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

完整问答自动归档至 `docs/modules/{category}/questions/{NN}-{主题}-{YYYYMMDD}.md`。**同日连续**、主题未偏离的问答续写同一文件。归档后简短告知路径。用户说「不要归档」或与项目无关时跳过。
