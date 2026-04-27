---
name: NEU-undergraduate-Writing
description: 本科毕设论文写作 skill。用于用户明确要求写作或改写本科毕设/本科论文/毕业设计的指定章节、段落、摘要、引言、相关工作、方法、实验、结论、参考文献或致谢时触发；依赖 ./docs/paper/_meta/context.md 和项目大纲，强调主张-证据链、中文学术文风、引用核验与降 AI。绝不批量遍历所有章节。
---

# NEU-undergraduate-Writing

按用户点名章节，逐节生成中文本科毕设论文内容。只负责内容写作、论证组织、引用占位、文风修订和图表占位；学校排版、Word 模板和格式细则交给 `NEU-undergraduate-Typesetting`。

## 触发

用户说「写引言 / 写摘要 / 写第 X 章 / 写实验 / 写结论 / 写致谢 / 写论文的某段」等明确章节指令。

## 硬约束

0. **全局禁忌**：按需读取 `../common/forbidden_rules.md`，只加载“全局绝对禁止”“Writing 禁忌”“用户自定义补充区”；用户自定义补充区优先级最高。
1. **按需写作**：用户必须指明章节；若仅说"写论文/写一下"等含糊指令 → **先反问要哪一节**，不得批量生成。
2. **前置依赖**：缺少 `${PROJECT_ROOT}/docs/paper/_meta/context.md` → 提示先跑 `NEU-undergraduate-Context` 并停止。
3. **项目约束优先**：具体章节骨架、题目、术语、数据、实验和导师要求，以 `context.md`、项目论文大纲和用户指令为准；本 skill 不硬编码过细结构。
4. **先证据后结论**：写作前先建立本节主张-证据关系；证据不足时标 `【待补证】`、`【待补实验结果】`、`【待确认】`，不得编造数据、引用、实验或贡献。
5. **原创性**：拒绝整段照抄；任何观点必有出处；冲突时引用优先于流畅度。
6. **全程简体中文**。

## 工作流

1. `Read ${PROJECT_ROOT}/docs/paper/_meta/context.md`，提取相关字段。
2. 判断目标章节，**按需** Read `references/chapter_checklists.md` 中对应章节段；不要读取无关章节。
3. 对摘要、引言、相关工作、方法、实验、结论等正文型任务，Read：
   - `references/content_quality_standards.md`：主线、摘要、标题导语、跨章节闭合；
   - `references/argument_evidence_rules.md`：主张-证据表、AXES 段落、实验-主张对应；
   - 定稿前 Read `references/style_and_deai_rules.md`：文风与降 AI。
4. 起草前先形成本节 mini-plan（内部使用，不写入正文）：
   - 本节任务；
   - 对应的一句话论文主线；
   - 本节主张与可用证据；
   - 不可写或需待确认的内容；
   - 段落安排；
   - 需要引用、图表或实验结果的位置。
5. 起草章节内容；引用占位写作 `[CITE:关键词]`，数据不足处保留 `【待补...】` 标记。
6. 起草后做自检：
   - 每个核心段落是否有主张、证据、解释或意义；
   - 标题后是否有导语；
   - 实验是否对应具体主张；
   - 结论是否回应绪论贡献；
   - 是否出现无证据的绝对化、模板句或空话。
7. **按需** Read `references/delegation_rules.md`，按规则委派：
   - 文献综述类章节 → 调 `36-taoyunudt-literature-review-skill`；
   - 解析所有 `[CITE:...]` → `bib-search-citation` 拿候选 + **每条** `WebFetch` 命中 `https://doi.org/{id}` 或 `https://arxiv.org/abs/{id}` 核验标题/作者/年份，匹配才允许写入；不匹配丢弃改找替代；
   - 章节定稿前 → 调 `48-copaper-ai-chinese-de-aigc` 降 AI 味；
   - 涉及图表 → 委派 `NEU-undergraduate-Drawing`，本 skill 在正文中只留 `图 X.Y` 占位与 caption。
8. 写入 `${PROJECT_ROOT}/docs/paper/drafts/{chapter}-v{n}-{YYMMDD-HHMM}.md`；版本号 = 同章节最新 v + 1；首次为 v1。

## 章节英文文件名映射

abstract / introduction / related_work / method / experiment / conclusion / references / acknowledgment

## 输出末尾必含

- 本节主张-证据摘要（已支撑 / 待补证 / 待确认）；
- 引用核验结果摘要（通过/丢弃数量）；
- 降 AI 处理结论（哪些段落改写过）；
- 是否已生成/委派图表清单。

## 子文件

- `references/chapter_checklists.md` — 各章节功能要求
- `references/content_quality_standards.md` — 论文主线、标题导语、跨章节闭合
- `references/argument_evidence_rules.md` — 主张-证据链、AXES 段落、实验-主张对应
- `references/style_and_deai_rules.md` — 中文学术文风与降 AI 规则
- `references/delegation_rules.md` — 委派规则详表与失败回退
- `../common/forbidden_rules.md` — 全局禁忌与用户自定义补充
