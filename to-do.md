# Todo（mathnote-preamble 改进）

- [x] 抽象 `\ctexset` 依赖：仅在加载 `ctex` 文档类/宏包后执行配色设置，给其他文档类提供安全回退。
- [x] 调整改写中文逗号的方案：停止把 `，` 重新定义为英文逗号，改用 `xeCJK` 自带的宽度与空隙控制，避免复制/搜索异常。
- [x] 让 `itemize`/`enumerate` 的装饰色条支持跨页，考虑为 `tcolorbox` 加 `breakable` 或换用 `tikz` 画线。
- [x] 让 `\noteversion` 在页眉或 PDF 元数据中真正显示，避免无效配置。
- [x] 为审稿章图标提供公开自定义接口（如 `\MathNoteReviewStampPath`），不要只依赖固定的 `assest/` 路径。
- [ ] 调整完成后自测一次示例文稿以验证模式切换、列表分页和页眉显示。

- [x] 将 `mathnote-preamble.tex` 模块化拆分为 `mathnote-core.tex` / `mathnote-colors.tex` / `mathnote-structure.tex` / `mathnote-content.tex`，并在各模块中补充中文注释以提升可读性与可维护性。
