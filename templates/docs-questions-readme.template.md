# 问题记录总库

> 本项目所有问答归档的**主存放目录**（正文唯一来源）。  
> 摘要索引见 [question-bank-modules.md](../meta/question-bank-modules.md) 与 [question-bank-overview.md](../meta/question-bank-overview.md)。

---

## 一、命名规则

| 规则 | 说明 | 示例 |
|------|------|------|
| 格式 | `{NN}-{主题}-{YYYYMMDD}.md` | `01-react模式与循环-20260529.md` |
| 序号 `NN` | **本目录**内两位递增（从 `01` 起） | `01`、`02`、`03` |
| 连续合并 | 同日且与上一条主题连续 | 续写同一文件，追加 `## 问答二` |
| 主题 | 简短中文 | `react模式与循环`、`工具同步调用` |
| 日期 | 落盘日 `YYYYMMDD` | `20260530` |

---

## 二、与模块目录的关系

```text
docs/questions/01-xxx.md          ← 主归档（先写这里）
        │
        ├─ 可映射到 category → 同步复制到
        └─ docs/modules/{category}/questions/01-xxx.md
```

---

## 三、记录列表

| 文件 | 时间 | 分类 | 问题摘要 |
|------|------|------|----------|
| （暂无） | — | — | — |

新增记录后，在本表追加一行，按序号排序。

维护规则见 [conventions.md § 9.1](../meta/conventions.md#91-问题记录总库--模块副本) 与 `.hancli/skills/doc-maintenance/SKILL.md` § 七。
