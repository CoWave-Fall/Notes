  - 导言主包重构（mathnote-preamble.sty）
      - \ProvidesPackage{mathnote-preamble}[2025/11/18 Math note XeLaTeX preamble]，正式将 mathnote-preamble.sty 作为唯一推荐入口（不再出现 mathnote-xetex-preamble 的包名）。
      - 引擎检查改为基于 \ifXeTeX 的显式限制：只允许 XeLaTeX，其他引擎会抛出 \PackageError{mathnote-preamble}{本导言仅支持 XeLaTeX 编译}{...}，并在错误信息中说明当前模板依赖 xeCJK。
      - 保留 root 探测逻辑，但报错信息改为统一使用 mathnote-preamble 包名，并用清晰中文提示：mathnote-core.tex 未找到，请确认 mathnote-* 导言文件与当前文档在同一工程目录树内。
      - 模块加载顺序固定为：
          - mathnote-core.tex
          - mathnote-colors.tex
          - mathnote-structure.tex
          - mathnote-content.tex
      - 版本信息块 \MathNoteVersionBlock 语义与文档对齐：
          - 当 \noteversionmark 为空时：显示一行 \noteversion | \notedate；
          - 当 \noteversionmark 非空时：仅显示 \noteversionmark 自定义内容，不再强行附带一行 \noteversion | \notedate。
  - 屏幕/印刷双模式逻辑修正（mathnote-core.tex + mathnote-colors.tex）
      - \MathNoteEnablePrint 现在调用高层接口：

        \newcommand{\MathNoteEnablePrint}{%
          \mathnoteprintmodetrue
          \MathNoteApplyPalette
        }
        从而在切换到印刷模式时，同时刷新：
          - 语义主色（accent / secondary / highlight / inkgray / surface）；
          - 16 色调色板 tone01–tone16；
          - hyperref 链接/边框颜色。
      - 保留 \MathNoteEnableReviewStamp 为正常可用命令：

        \newcommand{\MathNoteEnableReviewStamp}{%
          \mathnotereviewstamptrue
          \mathnote@enablereviewstamp
        }
        修复了之前在源码中出现的“带 % 的错误命令名”问题。
  - 代码高亮与彩色盒子样式内聚（mathnote-content.tex）
      - listings 默认配色统一改走 tone 语义层，而不是屏幕专用色：
          - 从 screenTone04/06/12 改为 tone04/06/12：

            keywordstyle=\color{tone04}\bfseries,
            commentstyle=\color{tone06},
            stringstyle=\color{tone12},
          - 配合 \MathNoteApplyPalette，在屏幕/印刷模式下会自动映射到 screenToneXX 或 printToneXX。
      - 将盒子标题颜色/字体的“细节 Tweaks”从导言层移回内容模块本身，统一直接写在各 \newtcolorbox 定义中：
          - definitionbox：coltitle=secondarytitle, fonttitle=\sffamily\bfseries
          - theorembox：coltitle=accenttitle, fonttitle=\sffamily\bfseries
          - examplebox / notebox / warningbox：coltitle=highlighttitle, fonttitle=\sffamily\bfseries
          - lemmabox / proofbox：coltitle=inktitle, fonttitle=\sffamily\bfseries
          - summarybox：coltitle=accenttitle, fonttitle=\sffamily\bfseries
          - conceptbox：coltitle=secondarytitle, fonttitle=\sffamily\bfseries
      - 这样彩色盒子所有视觉属性都集中在 mathnote-content.tex 内，不再依赖导言层的 tcolorboxenvironment{...} 额外覆写，维护更一致。
  - 兼容性与潜在影响
      - 引擎限制更明确：之前技术上允许 LuaLaTeX 进入导言阶段但会在 xeCJK 处失败；现在在导言层直接报错提示“仅支持 XeLaTeX”，对文档行为无实质退化，但对习惯尝试 LuaLaTeX 的用户会更早暴露问题。
      - 代码颜色在印刷模式下变化：由于改用 tone 语义色，切换 \MathNoteEnablePrint 后，代码高亮会明显适配 CMYK 调色板，而不是沿用原先屏幕优化的 RGB 色。
      - 版本块展示更符合文档说明：若已有文档手动把 \noteversionmark 设为多行版本记录，现在封面只展示这几行自定义内容；不会再在下面自动追加一行重复的 version | date。