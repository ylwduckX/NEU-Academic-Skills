---
name: NEU-undergraduate-Typesetting
description: 本科毕设论文 Word 排版 skill。按东北大学《本科生毕业设计(论文)书写印制规范V5》，用 docx skill 操作 .docx 完成排版；禁用 LaTeX；输出副本到 ./docs/paper/typeset/，绝不覆盖原文件。仅当用户为本科毕设/本科论文/毕业设计排版时触发。
---

# NEU-undergraduate-Typesetting

## 触发

用户说「排版 / 套模板 / 套规范 / 转 Word / 套学校格式 / 出最终稿」等。

## 前置检查

1. `templates/规范.docx` 与 `templates/模板.docx` 必须存在；缺失则提示用户重放（说明是 skill 自带副本）。
2. 待排版源：默认取 `${PROJECT_ROOT}/docs/paper/drafts/` 下各章节最新版（按 `{chapter}-v{n}-{date}.md` 中 n 最大者）；用户可指定具体路径。
3. 涉及 python（python-docx 兜底）时按需 Read `env.json`（默认 `conda run -n dfv python`）。

## 工作流

1. 委派 `docx` skill 加载 `templates/模板.docx` 副本（**注意：是副本不是原模板，先 cp**）。
2. 按需 Read `references/format_spec_summary.md` 取详细规则。
3. 章节内容 → Word 样式映射：
   - `# 一级` → Heading 1（章标题，黑体小二，段前 0.8 行段后 0.5 行）；
   - `## 二级` → Heading 2（黑体四号）；
   - `### 三级` → Heading 3（黑体小四）；
   - 正文段落 → 正文样式（宋体小四，1.5 倍行距，首行缩进 2 字符）；
   - 公式 → 居中 + 右侧编号「(章号.序号)」；
   - 图 → 居中插入 + 下方「图 X.Y 标题」（五号宋体居中）；
   - 表 → 上方「表 X.Y 标题」+ 三线表；
   - 代码块 → 等宽字体五号或小五。
4. 自动生成目录、页眉「东北大学本科生毕业设计（论文）」、奇偶页脚（摘要起大写罗马、正文起阿拉伯）、各章奇数页起。
5. 检查美观约束：禁大段空白、图表不跨页、标题后必接非空段落。
6. 写入副本（**禁止覆盖**）：扫描 `${PROJECT_ROOT}/docs/paper/typeset/` 下 `正文-v{n}-*.docx` 取最大 n，输出到 `正文-v{n+1}-{YYMMDD-HHMM}.docx`；首次为 v1。

## 规范摘要（详表见 `references/format_spec_summary.md`）

| 项 | 规则 |
|---|---|
| 纸张 | A4 |
| 页边距 | 上 2.5cm 下 2.5cm 左 3.0cm 右 2.5cm |
| 正文 | 宋体小四（英文/数字 Times New Roman）1.5 倍行距 |
| 章标题 | 黑体小二，段前 0.8 行段后 0.5 行 |
| 二级标题 | 黑体四号 |
| 三级标题 | 黑体小四 |
| 页眉 | 「东北大学本科生毕业设计（论文）」 |
| 页码 | 摘要起大写罗马 / 正文起阿拉伯，居中 |
| 参考文献 | GB/T 7714-2005 顺序编码 |
| 图表 | 三线表；图标题在下、表标题在上；按章编号 |
| 公式 | 按章编号，编号居右 |
| 章起 | 各章奇数页起 |

## 能力缺口处理

`docx` skill 无法直接设置的项（分节符、奇偶页起始、目录自动域、页眉每章不同）：
1. 优先用 `python-docx` 写小脚本兜底（python 命令读 `env.json`）；
2. 仍失败 → 输出"需手工调整步骤"清单（用 Word GUI 步骤描述）；
3. **绝不调用 LaTeX**。

## 子文件

- `references/format_spec_summary.md` — 规范详细条目（按需读）
- `templates/规范.docx` — 学校规范副本（核验依据）
- `templates/模板.docx` — 学校模板副本（执行底稿）
- `env.example.json` — python 解释器配置样板
