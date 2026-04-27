---
name: NEU-undergraduate-Review
description: 本科毕设论文只读审查 skill。四维评审：格式合规 / 文字行文 / 创新点+综合质量 / 查重AIGC。基于学校规范、形式审查表、质量评价表、评阅表给出报告。仅产出报告到 ./docs/paper/reviews/，禁止改稿。仅当用户为本科毕设/本科论文/毕业设计审查时触发。
---

# NEU-undergraduate-Review

## 触发

用户说「审一下论文 / 审查 / review / 看看格式 / 评估质量 / 查重 / 检测 AI」等。

## 只读硬约束

**禁止 Edit/Write/NotebookEdit** `${PROJECT_ROOT}/docs/paper/typeset/` 与 `${PROJECT_ROOT}/docs/paper/drafts/` 下任何文件。仅可：
- 读取上述路径；
- 写入 `${PROJECT_ROOT}/docs/paper/reviews/{YYMMDD-HHMM}-review.md`。

## 输入

- 默认审查 `${PROJECT_ROOT}/docs/paper/typeset/` 下最新 `正文-v{n}-*.docx`；
- 用户指定路径则用指定的；
- `${PROJECT_ROOT}/docs/paper/_meta/context.md`（用于第三维创新点对照）；
- 可选 `${PROJECT_ROOT}/docs/paper/_meta/review_config.json`（API 配置，缺失则走 Claude 内容评估路径）；
- 涉及 python（API 调用）时按需 Read `env.json`。

## 四维审查（细则在 `references/review_rubric.md`，按需 Read）

### 维度 1：格式合规
依据：`templates/规范.docx` + `templates/形式审查表.docx`（附件 18）+ rubric 中格式分项清单。
输出：每条规则 ☑/☒/⚠（不适用），附定位（页码/章节/行号），并给出修复建议。

### 维度 2：文字与行文
- 错别字 / 病句 / 学术腔失当 / 逻辑断裂 / 章节衔接生硬。
- 调用 `paper-audit` 做深度评审，整合其结论。

### 维度 3：创新点 + 综合质量
- 基于 `context.md` 的"创新点列表"对照正文，标记「真创新 / 增量改进 / 复述他人」三档；
- 按 `templates/质量评价表.doc`（附件 17 工学）+ `templates/评阅表.doc`（附件 13 评阅教师）的指标项给预评价分项；
- 提示「导师/评阅人可能在此项扣分的位置」（精确到段或图表）。

### 维度 4：查重 / AIGC（API 接口化）
1. 读 `${PROJECT_ROOT}/docs/paper/_meta/review_config.json`（样板 `config.example.json`）。
2. 字段含 `cnki / turnitin / wanfang / gptzero / zhuque`，每项 `{endpoint, key}`。
3. **若任一字段含可用 endpoint+key**：用 `env.json` 中 python 跑一段 `requests` 脚本调对应 API，附原始返回 + 命中段落定位。
4. **若全空**：用 Claude 自身知识做内容级评估：
   - ① 与已知公开文献的句法/术语相似段落定位（按段编号 + 风险等级）；
   - ② 高 AI 概率段落定位（句长方差小、连接词模板化、同义堆叠等启发式）；
   - ③ 给出**降重 / 降 AI 的具体改写建议**（指明替换词、句式变换、结构重排）。
5. **绝不推荐外部平台名**。

## 输出

`${PROJECT_ROOT}/docs/paper/reviews/{YYMMDD-HHMM}-review.md`，结构：

```
# 论文审查报告 {date}

## 1 元信息
- 被审文件 / 字数 / 字符数 / 章节数

## 2 维度一：格式合规
- 通过 X 项 / 不通过 Y 项 / 不适用 Z 项
- 详表（清单）

## 3 维度二：文字与行文
- paper-audit 摘要
- 自查发现

## 4 维度三：创新点 + 综合质量
- 创新点逐条评级
- 质量评价表预评分
- 评阅表预评分
- 高风险扣分项

## 5 维度四：查重 / AIGC
- 走 API 还是 Claude 内评估
- 可疑段落清单
- 改写建议

## 6 总体结论与优先修订清单
按"高/中/低"风险排序的修订条目
```

## 子文件

- `references/review_rubric.md` — 四维评分细则
- `templates/规范.docx` — 格式审查依据副本
- `templates/形式审查表.docx` — 附件 18 副本
- `templates/质量评价表.doc` — 附件 17 副本
- `templates/评阅表.doc` — 附件 13 副本
- `config.example.json` — 查重/AIGC API 配置样板
- `env.example.json` — python 解释器配置样板
