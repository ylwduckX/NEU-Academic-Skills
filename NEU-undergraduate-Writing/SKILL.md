---
name: NEU-undergraduate-Writing
description: 本科毕设论文写作 skill。基于 ./docs/paper/_meta/context.md，按用户**指定**章节生成中文论文内容（摘要/引言/相关工作/方法/实验/结论/参考文献/致谢），自动委派文献核验、降 AI、绘图。仅在用户明确处理本科毕设/本科论文/毕业设计写作时触发。绝不批量遍历所有章节。
---

# NEU-undergraduate-Writing

按用户点名章节，逐节生成中文本科毕设论文内容。

## 触发

用户说「写引言 / 写摘要 / 写第 X 章 / 写实验 / 写结论 / 写致谢 / 写论文的某段」等明确章节指令。

## 硬约束

1. **按需写作**：用户必须指明章节；若仅说"写论文/写一下"等含糊指令 → **先反问要哪一节**，不得批量生成。
2. **前置依赖**：缺少 `${PROJECT_ROOT}/docs/paper/_meta/context.md` → 提示先跑 `NEU-undergraduate-Context` 并停止。
3. **原创性**：拒绝整段照抄；任何观点必有出处；冲突时引用优先于流畅度。
4. **全程简体中文**。

## 工作流

1. `Read ${PROJECT_ROOT}/docs/paper/_meta/context.md`，提取相关字段。
2. **按需** Read `references/chapter_checklists.md` **中本次目标章节那一节**（不读其他节，省 token）。
3. 起草章节内容；引用占位写作 `[CITE:关键词]`，不立即落实。
4. **按需** Read `references/delegation_rules.md`，按规则委派：
   - 文献综述类章节 → 调 `36-taoyunudt-literature-review-skill`；
   - 解析所有 `[CITE:...]` → `bib-search-citation` 拿候选 + **每条** `WebFetch` 命中 `https://doi.org/{id}` 或 `https://arxiv.org/abs/{id}` 核验标题/作者/年份，匹配才允许写入；不匹配丢弃改找替代；
   - 章节定稿前 → 调 `48-copaper-ai-chinese-de-aigc` 降 AI 味；
   - 涉及图表 → 委派 `NEU-undergraduate-Drawing`，本 skill 在正文中只留 `图 X.Y` 占位与 caption。
5. 写入 `${PROJECT_ROOT}/docs/paper/drafts/{chapter}-v{n}-{YYMMDD-HHMM}.md`；版本号 = 同章节最新 v + 1；首次为 v1。

## 章节英文文件名映射

abstract / introduction / related_work / method / experiment / conclusion / references / acknowledgment

## 输出末尾必含

- 引用核验结果摘要（通过/丢弃数量）；
- 降 AI 处理结论（哪些段落改写过）；
- 是否已生成/委派图表清单。

## 子文件

- `references/chapter_checklists.md` — 各章节必含要素清单
- `references/delegation_rules.md` — 委派规则详表与失败回退
