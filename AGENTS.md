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

% 封面
\thispagestyle{empty}
\PageTag[secondary]{高中数学·必要性探路}
\setcounter{page}{1}

\noindent
\begin{minipage}[t][0.7\textheight][c]{\textwidth}
  \raggedleft
  \vspace*{\fill}
  {\sffamily\bfseries\Huge\color{accent}\notetitle\par}
  \vspace{0.6em}
  {\Large\color{inkgray}\notesubtitle\par}
  \vspace{0.5em}
  {\large\color{inkgray}\noteauthor\par}
  \vspace{0.4em}
  {\normalsize\color{inkgray}\noteversion\quad|\quad\notedate\par}
  \vspace*{\fill}
\end{minipage}

\begin{center}
\begin{minipage}{0.84\textwidth}
  \textcolor{inkgray}{\sffamily\small 学习导航}\\
  \begin{itemize}
    \item 围绕\keyword{必要性探路}这一核心思想，梳理命题逻辑、不等式、函数、数列等常见场景中的典型模型；
    \item 强调从\inlinehint{必要条件}出发缩小范围，再通过\inlinehint{充分性}验证完成证明与求解；
    \item 配套基础例题与练习，帮助形成可迁移的\keyword{通用解题步骤}与\keyword{参数分析框架}。
  \end{itemize}
\end{minipage}
\end{center}
\vspace{1em}

\clearpage
\thispagestyle{plain}
\phantomsection

% 目录
\tableofcontents
\newpage



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
- 新的小节不换新页。
- 避免滥用彩色box，导致重点不突出。
- 正文中文是思源宋/思源黑，显式斜体或楷体会切到霞鹜文楷。

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

# 数学笔记 XeLaTeX 模板使用说明

## 模板简介

这是一个专为数学笔记设计的 XeLaTeX 模板，基于 `mathnote-preamble.tex` 导言区构建。模板提供分层色彩、统一的笔记盒、TikZ 样式以及屏幕/印刷双通道支持。

## 编译方式

使用 XeLaTeX 编译：
```bash
xelatex 数学笔记模板.tex
```

建议编译两次以获得正确的目录和交叉引用：
```bash
xelatex 数学笔记模板.tex
xelatex 数学笔记模板.tex
```

## 模板特性

### 1. 文档类
- 使用 `ctexart` 文档类，自动支持中文

### 2. 颜色主题
模板内置现代低饱和度配色，并保持屏幕与 CMYK 输出的亮度一致：
- `accent`: 主色，用于章节标题、定理盒
- `secondary`: 次要强调色，用于定义、辅助说明
- `highlight`: 高光橙色，用于示例、提示
- `inkgray`: 中性文字色，用于页眉、注释
- `surface`: 背景底色，提升对比度

### 3. 专用环境

#### 定义环境
```latex
\begin{definitionbox}{定义标题}
内容...
\end{definitionbox}
```

#### 定理环境
```latex
\begin{theorembox}{定理标题}
内容...
\end{theorembox}
```

#### 例题/技巧环境
```latex
\begin{examplebox}{例题标题}
内容...
\end{examplebox}
```

#### 引理环境
```latex
\begin{lemmabox}{引理标题}
内容...
\end{lemmabox}
```

#### 注意环境
```latex
\begin{notebox}{注意标题}
内容...
\end{notebox}
```

所有彩色盒子默认支持自动分页（`breakable`），长内容不会越页被截断；左侧色条与细线框用于保持层次。

#### 总结环境
```latex
\begin{summarybox}{关键要点}
内容...
\end{summarybox}
```

#### 关键词高亮
```latex
在证明的关键步骤使用 \keyword{关键词} 可以快速定位重点。
```

### 4. 屏幕/印刷模式切换

- 默认以 sRGB 色域显示，亮度友好、适合屏幕阅读；
- 若需要 CMYK 输出，在 `\input{mathnote-preamble.tex}` 之后、`\begin{document}` 之前调用：
  ```latex
  \MathNoteEnablePrint
  ```
  该命令会重新映射配色，限制油墨总量，并同步更新 `hyperref` 链接颜色。

### 5. 信息层级辅助
- `\inlinehint{文字}`：以无衬线小字提示，兼容双模式；
- `\keyword{关键字}`：以主题色加粗；
- `focuspoints`：用于列出排查或推演节点；
- `\PageTag{文本}`：在当前页右上角添加浮动标签，常用于阶段标识。

### 6. 数学定理环境

模板还提供了标准的 amsthm 定理环境：
- `\begin{theorem}...\end{theorem}` - 定理
- `\begin{definition}...\end{definition}` - 定义
- `\begin{lemma}...\end{lemma}` - 引理
- `\begin{corollary}...\end{corollary}` - 推论
- `\begin{example}...\end{example}` - 例题

### 7. 绘图支持

模板支持 TikZ 绘图，可以绘制函数图像、几何图形等。

### 8. 页面设置

- A4 纸张
- 页边距：上下 2.5cm，左右 2cm
- 字体大小：11pt

### 9. 元信息与页眉

在导言区通过以下命令配置标题信息，所有页面会自动在页眉/页脚中显示：
```latex
\renewcommand{\notetitle}{数学笔记标题}
\renewcommand{\noteauthor}{作者姓名}
\renewcommand{\notedate}{\today}
```

页眉左侧为整本笔记标题，右侧为当前章节；页脚显示作者与“当前页/总页数”。

### 10. 常用命令

模板预置数学命令，保证符号一致：
```latex
\R,\C,\Q,\Z,\N,\dd x,\abs{z},\norm{\mathbf{v}}
```
可使用 `\DeclarePairedDelimiter` 提供的星号形式（如 `\abs*{...}`）自动调节括号大小。

## 使用示例

### 基本结构

```latex
\documentclass[a4paper, 11pt]{ctexart}
\input{mathnote-preamble.tex}
% \MathNoteEnablePrint  % 若需要 CMYK 输出

\begin{document}

\title{\Huge \bfseries \color{accent} 你的标题}
\author{你的名字}
\date{\today}
\maketitle

\tableofcontents
\newpage

\section{章节标题}
\subsection{小节标题}

\begin{definitionbox}{定义标题}
定义内容...
\end{definitionbox}

\begin{theorembox}{定理标题}
定理内容...
\end{theorembox}

\end{document}
```

### 数学公式示例

```latex
% 行内公式
设函数 $f(x) = x^2 + 1$。

% 独立公式
\begin{equation}
    \sin^2\alpha + \cos^2\alpha = 1
\end{equation}

% 多行公式
\begin{align}
    \sin(\alpha \pm \beta) &= \sin\alpha\cos\beta \pm \cos\alpha\sin\beta \\
    \cos(\alpha \pm \beta) &= \cos\alpha\cos\beta \mp \sin\alpha\sin\beta
\end{align}
```

### TikZ 绘图示例

```latex
\begin{center}
\begin{tikzpicture}[mathnote lines, scale=1]
    % 坐标轴
    \draw[->, thick] (-2,0) -- (3,0) node[right] {$x$};
    \draw[->, thick] (0,-1) -- (0,3) node[above] {$y$};
    
    % 函数图像
    \draw[accent, thick, domain=-1.5:2.5, samples=100] 
        plot (\x, {(\x-0.5)^2 + 0.5});
\end{tikzpicture}
\captionof{figure}{函数图像}
\end{center}
```

## 自定义修改

### 修改笔记元信息

在导言区重定义 `\notetitle`、`\noteauthor`、`\notedate`：
```latex
\renewcommand{\notetitle}{高等数学笔记}
\renewcommand{\noteauthor}{张三}
\renewcommand{\notedate}{2024.06}
```

### 修改颜色

在 `mathnote-preamble.tex` 中调整屏幕/印刷两套基色，例如：
```latex
\definecolor{screenAccent}{HTML}{0055A4}
\definecolor{printAccent}{cmyk}{1,0.45,0,0.05}
```
或在主文件中局部覆盖
```latex
\colorlet{accent}{teal!65!black}
\MathNoteRefreshColors
```
以同步更新盒子与链接配色。

### 修改页边距

在 `geometry` 设置中修改：
```latex
\geometry{left=2cm, right=2cm, top=2.5cm, bottom=2.5cm}
```

### 修改字体大小

在文档类选项中修改：
```latex
\documentclass[a4paper, 12pt]{ctexart}  % 改为 12pt
```

## 注意事项

1. 确保使用 XeLaTeX 编译，不要使用 pdfLaTeX
2. 如果中文字体显示不正确，可能需要安装中文字体或修改字体设置
3. 某些复杂的 TikZ 图形可能需要多次编译
4. 目录需要编译两次才能正确显示

## 常见问题

### Q: 编译时提示找不到字体？
A: 检查系统是否安装了中文字体。如果使用 Windows，通常使用默认字体即可。如果需要指定字体，可以修改 `\setCJKmainfont` 命令。

### Q: 如何添加自定义环境？
A: 参考模板中的 `\newtcolorbox` 命令创建新的环境，设置合适的颜色和样式。

### Q: 如何移除目录？
A: 删除或注释掉 `\tableofcontents` 这一行。

## 参考

本模板基于以下数学笔记文件的结构：
- `三角函数.tex`
- `以高等数学视角看待函数.tex`
- `常用初等函数衍生函数.tex`




