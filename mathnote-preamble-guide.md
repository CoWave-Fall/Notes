# `mathnote-preamble.tex` 使用指南

该导言区文件为整个数学笔记模板提供字体、配色、盒子环境和屏幕/印刷双模式能力。以下指南帮助你独立在任意 `ctexart`/`ctexrep`/`article` 项目中复用、定制并排错。

## 1. 准备工作

- **编译引擎**：强制使用 XeLaTeX 或 LuaLaTeX。若使用 PDFLaTeX，会直接触发 `\PackageError` 并提示切换引擎。
- **引入方式**：在主文件导言区末尾加 `\input{mathnote-preamble.tex}`，随后写正文。需要先加载 `ctex` 文档类或其它兼容中文的类。
- **字体目录**：导言会在 `fonts/`、`./fonts/`、`../fonts/`…等路径中查找可变字体（Noto、Source Han、思源黑体等）。若你在自定义目录储存字体，可在 `\input` 之前手动设置
  ```latex
  \def\MathNoteFontDir{/path/to/fonts/}
  ```
  末尾务必保留 `/`，以便 `fontspec` 的 `Path` 选项拼接。
- **字体缺失时的回退**：若找不到本地字体，导言会退回到系统字体（如 `TeX Gyre`、`Fandol` 系列），因此不用额外修改即可获得合法输出，只是视觉会与设计稿略有差异。

## 2. 元信息与页眉页脚

- 通过以下命令覆盖默认值（应放在 `\input` 之后、`\begin{document}` 之前）：
  ```latex
  \renewcommand{\notetitle}{代数笔记}
  \renewcommand{\noteauthor}{Alice}
  \renewcommand{\notedate}{2024-11-13}
  \renewcommand{\notesubtitle}{Galois 理论速写}
  \renewcommand{\noteversion}{v0.3}
  ```
- 这些命令会同时作用于 PDF 书签元数据、页眉的“标题 > 当前节”链路以及 `\pageref{LastPage}` 页脚显示。

## 3. 屏幕/印刷切换与颜色

- 默认处于 **屏幕模式**，采用 sRGB 配色，链接高亮、盒子底色等都较鲜明。
- 调用 `\MathNoteEnablePrint` 后将切换至 CMYK 友好色，超链接改为 `hidelinks`，适合送印。需要随时切回屏幕模式时，再次调用 `\MathNoteEnablePrint` 之前先手动 `\mathnoteprintmodefalse` 再 `\MathNoteRefreshColors` 即可。
- `\ScreenOnly{...}`、`\PrintOnly{...}` 与 `\DualMode{屏幕内容}{印刷内容}` 可针对两套输出显示不同文本或图形。
- 如需在正文中展示色板或模式标签，使用
  ```latex
  \MathNotePaletteSwatch{accent}{主色} \quad \ModeBadge[secondary]{草稿}
  ```
  `accent`/`secondary`/`highlight`/`inkgray`/`surface` 是主要配色命名，完全可以用 `\colorlet` 或 `\definecolor` 手动覆盖，然后调用 `\MathNoteRefreshColors` 让列表、盒子、超链接同步新颜色。

## 4. 审稿章与页标签

- `\MathNoteEnableReviewStamp` 会在导出的 PDF 首次页面叠加 `assest/lzlxV-reviewed.(svg|pdf)` 图章；若文件不存在会给出 `PackageWarning`（可按需替换成你自己的文件路径）。
- `\PageTag[highlight]{No.05}` 可于任意页面角落绘制一个彩色圆角页签；颜色参数可省略（默认 `accent`）。
- `\SectionTag{学习目标}` 在段落中插入大号无衬线标签，可用于手写式章节标题或点缀说明。

## 5. 版式、目录与段落

- 几何设置：A4 纸面、上下边距约 2.4 cm；`\setstretch{1.15}` 稍微拉开行距。
- `ctexset` 中给章节/小节/小小节分别赋予渐变色字体与 `\Large`、`\large` 等字号，确保层级清晰。若你想启用 `paragraph`/`subparagraph` 序号，可在导言后自行 `\setcounter{secnumdepth}{4}`。
- 将 `\pagestyle{fancy}` 的页眉文案替换为其他文字，可以重新定义 `\fancyhead[LE/RO]{...}`。

## 6. 数学与代码辅助

- 常用符号：`\abs{}`、`\norm{}`、`\ceil{}`、`\floor{}`、`\R`、`\C`、`\Q`、`\Z`、`\N`、`\dd`、`\ee`、`\dv{f}{x}`、`\pdv{f}{x}` 等均已就绪，无需重复 `\DeclareMathOperator`。
- 代码块：`listings` 已设定柔和背景、主题色关键字。若需不同语言风格，可追加 `\lstset{language=Python}` 或单独 `\begin{lstlisting}[language=...] ... \end{lstlisting}`。

## 7. 彩色盒子与流程

每个盒子有两个参数：`[可选 tcolorbox 键值]` 和 `{标题文本}`；若标题为空则按默认中文名称填充。

```latex
\begin{definitionbox}{定义标题}
  ...
\end{definitionbox}
```

可用盒子列表：`definitionbox`、`theorembox`、`examplebox`、`lemmabox`、`notebox`、`summarybox`、`conceptbox`、`proofbox`、`warningbox`。它们共享 `mathnote box` 样式（左侧色条、自动断页、与正文对齐的缩进），因此自定义新盒子时可直接：

```latex
\newtcolorbox{exercisebox}[2][]{%
  mathnote box,
  title=\mathnote@ifblank{#2}{练习}{#2},
  colframe=accent!50!black,
  ...
}
```

此外，还包含两个流程化环境：

- `focuspoints`：内部自动生成圆点项目符号，适合集锦关键点。
- `roadmap` + `\RoadmapStep{...}`：用于展示多步骤任务，模板会自动编号并在步骤之间插入箭头。

## 8. 列表与表格

- `itemize`、`enumerate`、`description` 已预设左侧色条与无衬线标题；若想恢复默认样式，可在局部环境中添加 `\begin{itemize}[before=\relax, after=\relax]` 并覆盖 `leftmargin` 等。
- `\mathnote@listbarbegin`/`\mathnote@listbarend` 是内部钩子，通常不需直接调用，但你可以参考其写法为其他列表（如 `tasks` 环境）套用相同装饰。
- `\renewcommand{\arraystretch}{1.2}` 提高表格行距，配合 `booktabs`/`tabularx`/`multicol` 即可排版信息密集的对照表。

## 9. TikZ 与绘图

- 已加载 `calc`、`arrows.meta`、`decorations.pathmorphing`、`positioning` 等常用库，且提供 `mathnote lines` 与 `mathnote grid` 两种预设样式。
- 若在屏幕/印刷模式下保持颜色一致，可直接使用 `accent`、`secondary` 等命名色彩。TikZ 节点标签默认继承 `inkgray`，如需改变，局部加 `text=black` 即可。

## 10. 常见排版技巧

- `\keyword{紧凑支撑}`：将关键字染为主色并加粗。
- `\inlinehint{复习提示}`：生成无衬线、小号辅助说明。
- `\ModeBadge{示例}`：彩色徽章，常用在表格或段落的模式标记；可用可选参数指定颜色（如 `[highlight]`）。
- 段首逗号加半字空：导言将中文逗号 `，` 定义为主动换算为英文逗号加 `0.5em`，因此不建议在正文中故意输入英文逗号来调整间距。
- 通过 `\ScreenOnly{图像 A}` 与 `\PrintOnly{图像 B}`，可为不同输出插入不同版本的图形或链接描述。

## 11. 排错清单

1. **编译时报找不到字体**：确认字体文件位于 `fonts/` 子目录，或设置 `\MathNoteFontDir` 指向绝对路径；必要时改用系统字体名称（如 `\setmainfont{Noto Serif}`）。
2. **审稿章不显示**：检查 `assest/lzlxV-reviewed.pdf/svg` 是否存在；SVG 需要 `\includesvg` 支持（模板已加载 `svg` 包）。若路径不同，修改 `\mathnote@reviewstamppathbase` 为自定义文件即可。
3. **盒子颜色无变化**：切换模式或手动 `\colorlet` 之后务必执行 `\MathNoteRefreshColors`，否则 `tcolorbox` 缓存仍使用旧颜色。
4. **页眉未更新节标题**：章节名称由 `\section` 命令写入 `\rightmark`，若使用的是自定义命令，可手动 `\markright{自定义文本}`。

通过上述步骤即可将 `mathnote-preamble.tex` 独立于模板使用、根据输出介质调整配色，并利用现成的盒子与辅助命令快速排版高质量数学学习笔记。
