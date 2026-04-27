# 数据图表工具候选（按需给用户 2~4 个，让用户决定）

按图类型给候选；每条含**适用场景**、**优点**、**缺点**、**最小可用代码片段**。  
不指定唯一工具，由用户根据熟悉度与最终发表场景选择。

## 折线图 / 柱状图 / 散点图

### 候选 1：matplotlib + scienceplots
- 适用：期刊插图、需要严格控制每个像素的场景。
- 优点：可控性最强、矢量输出 PDF/SVG、scienceplots 自带 IEEE / nature 风格。
- 缺点：默认中文需手工配 SimSun/SimHei；调样式语法略繁琐。
- 片段：
  ```python
  import matplotlib.pyplot as plt
  import scienceplots
  plt.rcParams['font.sans-serif'] = ['SimHei']
  plt.rcParams['axes.unicode_minus'] = False
  with plt.style.context(['science', 'no-latex']):
      fig, ax = plt.subplots(figsize=(6, 4))
      ax.plot(x, y, label='方法A')
      ax.set_xlabel('训练轮次'); ax.set_ylabel('准确率')
      ax.legend(); fig.savefig('xxx.png', dpi=300, bbox_inches='tight')
  ```

### 候选 2：seaborn
- 适用：分组柱、箱线、统计图。
- 优点：API 简洁、配色现成、与 pandas 集成顺畅。
- 缺点：底层仍是 matplotlib，复杂自定义需回落到 mpl 接口。

### 候选 3：Origin（GUI）
- 适用：本科论文常见、导师熟悉、不想写代码。
- 优点：一键得到出版级图、模板丰富。
- 缺点：商业软件、可复现差。

## 热力图 / 相关矩阵 / 混淆矩阵

### 候选 1：seaborn.heatmap
- 适用：数值矩阵可视化。
- 优点：annot 一键标数字、配色多。
- 片段：
  ```python
  import seaborn as sns
  sns.heatmap(matrix, annot=True, fmt='.2f', cmap='YlGnBu', cbar_kws={'label': '相似度'})
  ```

### 候选 2：matplotlib.imshow
- 适用：需要更细控制坐标轴/colorbar 时。
- 优点：和 mpl 子图体系兼容好。

## 雷达图 / 多指标对比

### 候选 1：matplotlib polar 子图
- 适用：常见的多指标对比。

### 候选 2：plotly
- 适用：需要交互 HTML 输出（不建议用于纸质论文，但可用于答辩 PPT）。

## 模型架构 / 流程图（如果还想代码画）

### 候选 1：draw.io / diagrams.net（GUI 推荐）
### 候选 2：tikz（LaTeX 用户；本套 skill 不接 LaTeX，仅作提示）
### 候选 3：Mermaid（适合简单流程，导出 SVG/PNG）

## 工具选择决策提示给用户

询问句式建议：
> 这张图你倾向：(a) python 脚本可复现（matplotlib/seaborn）/ (b) GUI 一键出图（Origin/Excel）/ (c) 在线交互（plotly）？我会按你选的生成对应代码或步骤。
