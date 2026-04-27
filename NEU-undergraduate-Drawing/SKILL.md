---
name: NEU-undergraduate-Drawing
description: 本科毕设论文绘图 skill。生成两类图：(A) 概念示意图通过已安装的 codex 插件让 codex 使用 gpt-image2 直出 PNG；(B) 数据图表先出 gpt-image2 预览草图再给科学绘图工具候选。仅在用户为本科毕设/本科论文/毕业设计需要插图或图表时触发。
---

# NEU-undergraduate-Drawing

## 触发

用户说「画图 / 画一张图 / 出架构图 / 画柱状图 / 画热力图 / 画消融对比图 / 画曲线」等。

## 前置条件

先按需读取 `../common/forbidden_rules.md`，只加载“全局绝对禁止”“Drawing 禁忌”“用户自定义补充区”；用户自定义补充区优先级最高。

1. 用户已安装 codex 插件（`/plugin install codex@openai-codex`），本 skill **直接调用**，不写安装步骤；
2. 涉及 python 工具（matplotlib/seaborn/plotly/科学绘图脚本）时按需 Read `env.json`（默认 `conda run -n dfv python`）。

## 两类工件分流

询问用户图属于哪一类（若用户已说明则跳过）：

- **(A) 概念示意图**：架构图、流程图、模块示意、数据流。 → gpt-image2 直出
- **(B) 数据图表**：条形/柱状/折线/箱线/热力图/雷达图等数值图。 → gpt-image2 预览 + 推荐工具

## (A) 概念示意图工作流

1. 写 spec 文件 `${PROJECT_ROOT}/docs/paper/figures/{name}.spec.md`，含字段：
   - 图主题 / 风格（学术-扁平-矢量）/ 主色与辅色（hex）/ 关键模块清单 / 模块间连接关系 / 必须出现的简体中文标注；
2. 按需 Read `references/codex_prompt_template.md` 拼装 prompt；
3. `Bash` 调 codex 插件命令（具体命令见模板）；
4. 输出 PNG 到 `${PROJECT_ROOT}/docs/paper/figures/{name}.png`，分辨率 ≥ 1600×1200；
5. 报告路径并提示用户预览；caption 与编号由 Writing skill 维护。

## (B) 数据图表工作流

1. 写 data 文件 `${PROJECT_ROOT}/docs/paper/figures/{name}.data.md`，含数据表（Markdown）+ 图类型 + 配色 + 中文坐标轴/图例/标题；
2. 按需 Read `references/codex_prompt_template.md` 中 (B) 部分拼 prompt；调 codex 让 gpt-image2 出**预览草图** `{name}.preview.png`，并在草图旁注「⚠ 预览，仅供审稿」；
3. 按需 Read `references/chart_tool_candidates.md`，给出 **2~4 个工具候选**及适用场景与短代码片段，**让用户决定**；
4. 用户选定 python 类工具 → 用 `env.json` 中的 python 命令运行生成脚本，输出最终 `{name}.png`；
5. 用户选 GUI 类工具（Origin/Excel）→ 给出操作步骤清单。

## 标题与排版规范

- 图标题：图下方居中，五号宋体，编号「图 X.Y」按章；
- 表标题：表上方居中；
- 图内全部文字必须简体中文（含坐标轴、图例、单位）。

## 失败回退

- codex 命令报错或超时 → 输出完整 prompt 文本，提示用户在终端手动跑 `/codex` 或粘贴到外部模型；
- python 脚本执行失败 → 打印错误 + 检查 `env.json` 路径正确性，再重试一次。

## 子文件

- `references/codex_prompt_template.md` — gpt-image2 prompt 模板（A/B 两类）
- `references/chart_tool_candidates.md` — 数据图工具候选清单
- `env.example.json` — python 解释器配置样板（复制为 env.json 后修改）
- `../common/forbidden_rules.md` — 全局禁忌与用户自定义补充
