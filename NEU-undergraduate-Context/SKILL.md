---
name: NEU-undergraduate-Context
description: 本科毕设论文上下文调研 skill。自动读取项目 CLAUDE.md、git log、docs/、README，抽取研究方向、论文主题、创新点、数据集、评估指标等结构化背景，写入 ./docs/paper/_meta/context.md，供其他 NEU-undergraduate-* 写作/绘图/排版/审查 skill 复用。仅当用户准备写本科毕设/本科论文/毕业设计相关材料时触发。
---

# NEU-undergraduate-Context

为 NEU 本科毕业设计论文工作建立**通用**研究背景档案。其他 4 个 skill 启动时强制读取该档案。

## 触发

用户开始本科毕设论文工作的第一个动作；或其他 NEU-undergraduate-* skill 提示「context.md 缺失」时。

## 行为

纯调研，**不主动询问用户**。按以下顺序收集，能拿到就拿，拿不到留空。

1. 项目根目录确定：`pwd` 给出当前工作目录作为 `${PROJECT_ROOT}`。
2. 读取（按存在性）：
   - `${PROJECT_ROOT}/CLAUDE.md`
   - `${PROJECT_ROOT}/README*`
   - `${PROJECT_ROOT}/docs/**/*.md`（如有 `docs/superpowers/specs/` 或类似设计文档优先）
   - `git log -n 30 --oneline`
   - 主代码目录关键文件名（如 `models/`、`src/`、`app/`）只列名，不读内容
3. 输出 `${PROJECT_ROOT}/docs/paper/_meta/context.md`，使用下方模板。

## 输出模板

```markdown
# 本科毕设论文上下文档案

> 自动生成。带 `<!-- user-edit-keep -->` 标记的段落不会被后续 Context 调研覆盖。

## 研究方向
（一句话）

## 论文主题候选
- 候选 1
- 候选 2

## 现有思路与方法链
（按时间或迭代顺序写关键节点）

## 创新点列表
| 编号 | 创新点 | 关键技术 | 出处文件/提交 |
|---|---|---|---|

## 主要数据集
| 名称 | 用途 | 规模/划分 |
|---|---|---|

## 评估指标
- 指标 1：含义
- 指标 2：含义

## 已有图表清单
（路径 + 一句话描述）

## 用户备注
<!-- user-edit-keep -->
（用户在此手填的内容不会被 skill 覆盖）
```

## 可重入

若 `context.md` 已存在：
- 解析所有 `<!-- user-edit-keep -->` 块原样保留；
- 其他段落基于最新调研结果增量更新；
- 输出前用 diff 给用户简要回报变更条目数。

## 与其他 skill 的契约

- 其他 skill 仅需 `Read ${PROJECT_ROOT}/docs/paper/_meta/context.md`，不需调用本 skill。
- 字段名稳定，新增字段请追加到末尾。
