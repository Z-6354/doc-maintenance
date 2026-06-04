# Agent 回合结束检查清单（强制）

> 配合 `every-turn-protocol.md` 与 `SKILL.md` **§ 0.0、§ 零**。Cursor 加载 Skill ≠ 已归档；**Write/StrReplace** 才算完成。

## 0. 答复前：读协议（v1.9.0 首步）

- [ ] 已读 [`every-turn-protocol.md`](every-turn-protocol.md)（≤60 行；**不必**读完全部 SKILL.md）

## 1. Skill 是否已生效（§ 0.0）

- [ ] `doc-maintenance` 在 available_skills，或已读 `every-turn-protocol.md` / SKILL 文首「首读」

**是** → 下节起默认**须归档**；用户**无需**说「归档」「写 docs」。  
**否** → 仅当用户明确触发 doc-maintenance 时再归档（本仓库通常始终为「是」）。

## 2. 本回合是否须归档？

- [ ] 用户问题与 **本仓库**（HANCLI 源码、docs、CLI、MCP、Skill、配置）相关
- [ ] 已给出**可复用的实质答复**（非纯寒暄/仅「好的」）
- [ ] 用户**未**说「不要归档」「仅回答」「不用记录」

以上全满足 → 继续；任一否 → 在回复末写 `📁 未归档：<原因>` 后结束。

**禁止**：因用户未提及「归档 / docs/questions / doc-maintenance」而跳过。

## 3. 落盘（同一回合内、**先于**用户可见最终答复）

- [ ] 读 `docs/questions/`：按 § 7.3 决定**续写**当日锚点或**新建** `NN-主题-YYYYMMDD.md`
- [ ] **先写总库** `docs/questions/`（模板见 `templates/question-record*.md`）
- [ ] 可映射 category → **同步同名**到 `docs/modules/{category}/questions/`
- [ ] 更新 `docs/questions/README.md`（+ 模块 `questions/README.md` 若适用）
- [ ] 更新 `docs/meta/question-bank-modules.md` 或 `question-bank-overview.md`（§ 六）

## 4. 回复用户（最后一步）

- [ ] 最终答复末尾包含：`📁 已归档：docs/questions/…`（续写时注明「问答 N」）

## 5. 禁止

- 只回答、不写文件
- 把「Skill 已加载」当作「已归档」
- 等用户说「归档一下」或问「要不要写入问题库」
- 认为只有用户主动问 doc-maintenance 才需要写盘
- 本回合只回复、下回合再归档
- 因 SKILL.md 太长未读 § 七而跳过（应读 `every-turn-protocol.md`）
