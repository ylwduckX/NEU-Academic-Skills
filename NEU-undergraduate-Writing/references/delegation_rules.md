# 委派规则详表（Writing skill 按需 Read）

## 1. 文献综述章节

触发：用户写「相关工作 / related work / 文献综述」时。
动作：
- 调用 `Skill` 工具 `36-taoyunudt-literature-review-skill`，传入 context.md 中的研究方向 + 主题候选；
- 接收其大纲与候选文献；
- 回到本 skill 完成行文 + 引用核验。

## 2. 引用条目核验（强校验）

输入：草稿中所有 `[CITE:关键词]` 占位。
步骤：
1. 调 `bib-search-citation` 用关键词搜本地 .bib（若无库则直接走步骤 2）；
2. 对每条候选：
   - 有 DOI → `WebFetch https://doi.org/{doi}`，从返回页面比对标题/第一作者姓/年份；
   - 无 DOI 有 arXiv ID → `WebFetch https://arxiv.org/abs/{id}`，同上比对；
   - 两者都无 → 用 `WebSearch` 搜索 `"标题" 作者 年份` 至少 1 个权威站点（dl.acm.org / ieeexplore / openreview / springer / nature / sciencedirect）确认存在；
3. 三项有任一不匹配 → 丢弃，记录到核验日志，重新检索替代品；
4. 通过的写入正文 `[N]` 编号 + 末尾参考文献条目（GB/T 7714 格式）。
5. 全章节核验完成后输出汇总：通过 X 条 / 丢弃 Y 条 / 替代 Z 条。

禁忌：
- 不允许"凭印象"写引用；
- 不允许猜测 DOI；
- 不允许编造作者或年份。

## 3. 降 AI 味（章节定稿前）

触发：本 skill 完成一节内容、引用核验完毕、即将写入 drafts/ 之前。
动作：调用 `Skill` 工具 `48-copaper-ai-chinese-de-aigc`，传入待落盘文本，按其五步闭环工作流处理；返回结果作为最终草稿写入。

## 4. 图表委派

触发：草稿中需要插图/图表的位置。
动作：
- 在正文留 `图 X.Y {简短中文标题}`（位置在引用语之后），并写一行 `<!-- TODO: 调 NEU-undergraduate-Drawing 生成 ./docs/paper/figures/{name}.png -->`；
- 章节末尾汇总所有 TODO 图表清单提示用户跑 Drawing skill；
- **本 skill 不直接画图**。

## 5. 失败回退

| 子环节失败 | 回退动作 |
|---|---|
| 36-taoyunudt 不可用 | 自行按章节模板写综述大纲；标注「未走综述 skill，请人工增强」 |
| bib-search-citation 不可用 | 直接走 WebSearch + WebFetch 核验路径 |
| WebFetch 全部超时 | 引用先标 `[CITE:?未核验]` 留在草稿，**禁止写入 references 章节**，提醒用户联网后重跑 |
| 48-copaper 不可用 | 文本不入库，提示用户手动跑 `/48-copaper-ai-chinese-de-aigc` 后重存 |
