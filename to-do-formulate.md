# TODO: Use mathnote-preamble.sty for all .tex files

Goal: migrate every note in this repo to the unified `mathnote-preamble.sty` preamble and stop using per-file custom preambles.

## 0. Global tasks

- [x] Ensure `mathnote-preamble.sty` is the single source of truth for the preamble; keep `mathnote-preamble.tex` only as a thin compatibility wrapper.
- [x] Use a ctex-based document class (for example `ctexart` / `ctexrep`) consistently in all notes; do not switch to English-only classes or change paper size here.
- [x] In every `.tex` document, load the preamble only via:  
  - `\usepackage{mathnote-preamble}` (preferred), or  
  - `\input{mathnote-preamble.tex}` (for legacy files),  
  and make sure they are not both used at the same time.
- [x] Remove per-file settings that duplicate or conflict with the preamble (geometry, fonts, colors, list styles, code styles, etc.).
- [ ] After loading the preamble, always set note meta information using  
  `\notetitle`, `\noteauthor`, `\notedate`, `\notesubtitle`, `\noteversion` (optionally `\noteversionmark`).
- [ ] For a few representative notes, compile with XeLaTeX (twice) and check screen/print modes, colors, boxes, TOC and headers/footers.

## 1. Top-level .tex files

- [x] Main template `.tex` in this directory: verify it uses `mathnote-preamble.sty` (directly or via `mathnote-preamble.tex`) and that the cover, TOC and sample boxes use the current APIs.
- [ ] `mathnote-colors.tex`: keep only color demo and documentation here; move any real color definitions into the `.sty` if still needed.
- [ ] `mathnote-content.tex`: ensure all examples use `definitionbox` / `theorembox` / `examplebox` / `summarybox` etc. and the provided math macros instead of older ad-hoc commands.
- [ ] `mathnote-core.tex`: move long-term core macros into the `.sty` and remove duplicated or experimental definitions from the example docs.
- [ ] `mathnote-structure.tex`: update sectioning, headers/footers and roadmap/PageTag/ModeBadge usage to match the current design in the `.sty`.
- [ ] `mathnote-preamble.tex`: keep it as a minimal wrapper with `\RequirePackage{mathnote-preamble}` and clear comments that `.sty` is the preferred entry point.

## 2. Subject folders (math / physics / etc.)

For each subject folder, apply the same steps to all `.tex` files:

1. Replace any old custom preamble with the standard skeleton: `ctexart` (or similar) + `mathnote-preamble.sty` (or `mathnote-preamble.tex`).
2. Remove local `geometry` / fonts / colors / list-style settings that duplicate or override the preamble defaults.
3. Add or check the 5 meta commands (`\notetitle`, `\noteauthor`, `\notedate`, `\notesubtitle`, `\noteversion`) and any cover / TOC / PageTag / ModeBadge usage.
4. Ensure all note content uses the standard boxes (`definitionbox`, `theorembox`, `examplebox`, `summarybox`, etc.) and helpers (`roadmap`, `focuspoints`, `ModeBadge`) where appropriate.
5. Pick at least one representative file per folder, compile with XeLaTeX and visually check both screen and print modes.

### 2.1 Math notes

- [ ] All math-related `.tex` files under the math folder(s).

### 2.2 Physics notes

- [ ] All physics-related `.tex` files under the physics folder(s).

### 2.3 Advanced math / special topics

- [ ] All `.tex` files in advanced-math or other topic-specific folders.

### 2.4 English notes

- [x] All English-related `.tex` files, using the same preamble and layout even if the content is not math.

## 3. Wrap-up

- [ ] Update any README or template documentation to recommend `\usepackage{mathnote-preamble}` first, with `\input{mathnote-preamble.tex}` kept for legacy files only.
- [ ] (Optional) Add a short “before vs after” migration example to show how a typical note’s preamble changes when moving to `mathnote-preamble.sty`.
- [ ] Record the standard build command (XeLaTeX twice) and common troubleshooting tips after the migration.
