# Notes 数学笔记项目 · AGENT 指南

本文件面向在 `Notes` 目录下修改文件的 AI Agent / 合作者，归纳与 **mathnote 系列导言** 及彩色盒子设计相关的约定和用法。请优先遵守本说明，再结合具体笔记中的需求行事。

## 1. 总体约定

- **编译引擎**：只考虑 XeLaTeX / LuaLaTeX；不要为 pdfLaTeX 做兼容，也不要删除现有引擎检查。
- **文档类**：默认使用 `ctexart`（或 `ctexrep` 等 ctex 系列），不要改成英文类或改变纸张大小，除非用户明确提出需求。
- **导言文件 / 包**：
  - 全局导言由 `mathnote-core.tex`、`mathnote-colors.tex`、`mathnote-structure.tex`、`mathnote-content.tex` 组合完成。
  - 推荐通过 `\usepackage{mathnote-preamble}` 引入；为兼容旧文档，仍保留 `\input{mathnote-preamble.tex}`（后者只是一个包装壳，会自动加载同名 `.sty` 包）。
  - 新增命令或环境时，优先在这些导言模块中集中管理，避免在单个笔记里重复定义。
- **字体目录**：默认在 `fonts/` 及其相对路径中查找；变更字体方案时，保留 `\MathNoteFontDir` 入口。
- **屏幕/印刷双模式**：保持 `\MathNoteEnablePrint`、`\mathnoteprintmodefalse`、`\MathNoteRefreshColors` 的语义不变；修改颜色时必须确保两种模式都可用。
- **现有宏与样式**：已预置大量数学命令、列表样式、彩色盒子、TikZ 样式和代码块设置；添加新功能时避免重复加载同一宏包或重新定义同名命令。在适当的位置加入图表。

## 2. 导言模块与可配置入口

- `mathnote-core.tex`：引擎检查、字体探测与设定、`geometry` 版式、XeCJK 配置、核心宏包。
- `mathnote-colors.tex`：屏幕/印刷配色、语义主色、16 色预置调色板、与 `hyperref` 的联动。
- `mathnote-structure.tex`：段落、节标题、页眉页脚、页码等结构性设置。
- `mathnote-content.tex`：数学辅助命令、彩色盒子、列表样式、TikZ 样式等。
- 优先使用已有“软接口”而不是直接改底层：
  - 版式：通过 `\MathNoteGeometry` 调整 `geometry{...}` 参数；
  - 行距：通过 `\MathNoteLineSpread` 调整 `\setstretch`；
  - 字体：通过 `\MathNoteSetupFonts` 重新应用字体方案；
  - 配色：修改语义色后调用 `\MathNoteRefreshColors` / `\MathNoteApplyPalette`；
  - 页眉页脚：重定义 `\MathNoteHeaderFormat` / `\MathNoteFooterFormat`；
  - 列表样式：使用 `\MathNoteUseColorLists` / `\MathNoteUsePlainLists` 切换。

## 3. 配色与 16 色调色板

- 语义主色：`accent`、`secondary`、`highlight`、`inkgray`、`surface`，在屏幕模式下映射为 sRGB，在印刷模式下映射为 CMYK。
- 16 色调色板：统一使用 `tone01`–`tone16`，内部根据 `\ifmathnoteprintmode` 自动映射到 `screenTone*` / `printTone*`。
  - 示例：
    ```latex
    \textcolor{tone03}{关键结论} \quad \colorlet{myAxisColor}{tone07}
    ```
- 盒子标题颜色使用 `accenttitle` / `secondarytitle` / `highlighttitle` / `inktitle`，相对主色略降饱和度，更克制。避免滥用盒子。

## 4. 章节结构 vs 语义盒子（防止滥用）

这一节是对“结构层级”和“语义标签”的约束，Agent 修改内容时必须遵守。

- **4.1 章节结构只交给章节命令和列表**
  - “一、二、三……”：`\section`（或更高层级） + ctex 自动编号；
  - “1. 2. 3. …”：`\subsection` 或 `enumerate` 第一级；
  - “(1) (2) (3)…”、①②③：使用 `enumerate` 的嵌套层级实现；
  - 禁止用 `definitionbox` / `examplebox` 等彩色盒子来充当 “(1)” 这类编号。

- **4.2 盒子只表达“内容类型”，不表达“层级”**
  - 核心语义盒子：
    - `definitionbox`：正式的定义 / 名词解释；
    - `theorembox` / `lemmabox`：定理、引理、命题；
    - `summarybox` / `conceptbox`：小节总结、关键概念回顾；
    - `proofbox`：特别重要、需要视觉突出的长证明（一般证明可用普通段落）。
  - 提示类盒子：
    - `examplebox`：少量“代表性例题/例子”；
    - `notebox`：提示、注意事项；
    - `warningbox`：真正“踩坑会出大问题”的警告；
    - `sideinfobox`：旁注式小贴士、历史/背景说明。
  - 建议频率：
    - 一页中强彩色盒子不超过 2–3 个；
    - 每个小节里，`definitionbox` / `theorembox` / `summarybox` 数量保持在“必要且精简”的范围内。

- **4.3 颜色覆盖率：line / soft / solid**
  - 通过 `mathnote coverage` 选项控制单个盒子的背景覆盖率：
    - `mathnote coverage=line`（默认）：白底 + 左色条，适合大多数定义/定理/总结盒子；
    - `mathnote coverage=soft`：轻微背景染色，用于“本节最核心结论”“代表性例题”；
    - `mathnote coverage=solid`：色块感较强，只用于极少数重要警告或综述。
  - 示例：
    ```latex
    \begin{examplebox}[mathnote coverage=soft]{例题·二次函数顶点}
      ...
    \end{examplebox}

    \begin{warningbox}[mathnote coverage=solid]{易错警告}
      ...
    \end{warningbox}
    ```

## 5. 简要使用说明（给人类作者和 Agent）

- 推荐新文档导言：
  ```latex
  \documentclass[a4paper,11pt]{ctexart}
  \usepackage{mathnote-preamble}
  % \MathNoteEnablePrint % 如需印刷版
  ```
- 兼容旧文档的方式：
  ```latex
  \input{mathnote-preamble.tex}
  ```
- 元信息：
  ```latex
  \renewcommand{\notetitle}{代数笔记}
  \renewcommand{\noteauthor}{Alice}
  \renewcommand{\notedate}{2024-11-13}
  \renewcommand{\notesubtitle}{Galois 理论速写}
  \renewcommand{\noteversion}{v0.3}
  ```

## 6. 工具与编码提示

- 仓库统一使用 UTF-8 编码；在 PowerShell 中操作文件时，推荐显式指定编码，例如：
  ```powershell
  Get-Content AGENTS.md -Encoding utf8
  Set-Content AGENTS.md -Encoding utf8
  ```
- 或在当前 PowerShell 会话中设置：
  ```powershell
  $PSDefaultParameterValues['*:Encoding'] = 'utf8'
  ```
  以避免中文注释在命令行中出现乱码。

---

如需为本仓库新增环境、命令或模板文件，请尽量遵循以上约定，并保持与现有笔记的风格一致；涉及 mathnote 导言和彩色盒子的改动，必须优先考虑利用既有配置入口，并遵守“章节结构 vs 语义盒子”的区分，避免滥用彩色盒子。

## 7. 封面与目录设计指导

- **封面推荐做法**：
  - 使用 `\thispagestyle{empty}`，避免页眉页脚出现在封面页。
  - 标题区域建议使用 `\title{\Huge \bfseries \color{accent} ...}`，保持单一主色 + 简短副标题，尽量不要在封面堆叠多个彩色盒子。
  - 如需展示版本信息，优先使用 `\MathNoteVersionBlock` 或在封面底部用小号灰色文字标注，而不是再造一套特殊盒子样式。
  - 封面可以配合 `\PageTag` 做简单页签标记，但不应用 `definitionbox`/`examplebox` 等语义盒子。
- **目录（TOC）推荐做法**：
  - 使用标准 `\tableofcontents` 生成目录，不将整个 TOC 包在彩色盒子或多栏环境中。
  - 目录层级深度通过 `\setcounter{tocdepth}{...}` 控制，而不是手动删改目录条目；保持与正文章节结构一致。
  - 目录页不额外使用彩色盒子；如需说明“草稿版”“待补章节”，可以在封面或首页的 `summarybox` 中说明，而非直接改 TOC 样式。
  - Agent 在调整封面/目录时，应优先通过上述命令与计数器配置实现，不自行重写复杂 TOC 格式。
