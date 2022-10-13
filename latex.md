---
title: latex
category: 2022-10-13
renderNumberedHeading: true
grammar_cjkRuby: true
---

{
	"*": "*", // 这里指代其他已有的字典键值，保留，在其下增加新键值
	"latex-workshop.latex.recipe.default": "xelatex -> bibtex -> xelatex*2",
	// 设置默认编译指令，根据name在下方recipes取具体的先后执行命令
	"latex-workshop.latex.recipes": [
		{"name": "pdflatex -> bibtex -> pdflatex*2",
		 "tools": ["pdflatex", "bibtex", "pdflatex", "pdflatex",],},
		// 对应具体的执行命令集合，这些命令pdflatex/bibtex从下方tools取
		{"name": "xelatex -> bibtex -> xelatex*2",
		 "tools": ["xelatex", "bibtex", "xelatex", "xelatex",],},
	],
	"latex-workshop.latex.tools": [
		{"name": "pdflatex", "command": "pdflatex",
		 "args": ["-synctex=1", "-interaction=nonstopmode", "-file-line-error", "%DOCFILE%",],},
		{"name": "bibtex", "command": "bibtex",
		 "args": ["%DOCFILE%",],},
		{"name": "xelatex", "command": "xelatex",
		 "args": ["-synctex=1", "-interaction=nonstopmode", "-file-line-error", "%DOCFILE%",],},
	},
	],
	"latex-workshop.view.pdf.viewer": "tab",  // 标签模式打开pdf
	"latex-workshop.synctex.afterBuild.enabled": true, // 启用反向搜索
	"latex-workshop.view.pdf.internal.synctex.keybinding": "double-click",  // 双击反向搜索（反向搜索见下文解释）

	// 以下是其他配置，非必须
  "latex-workshop.bibtex-fields.sort.enabled": true,  // 对.bib文件按键排序
  "latex-workshop.bibtex-format.sort.enabled": true,  // 对.bib文件格式化
  "latex-workshop.intellisense.package.enabled": true,
  "latex-workshop.latex.autoClean.run": "onBuilt",  // 编译完成后清理多余文件
  "latex-workshop.latex.clean.fileTypes": [  // 设定清理的文件类型
    "*.aux", "*.bbl", "*.blg", "*.idx", "*.ind", "*.lof", "*.lot", "*.out", "*.toc",
    "*.acn", "*.acr", "*.alg", "*.glg", "*.glo", "*.gls", "*.ist", "*.fls", "*.log",
    "*.fdb_latexmk"
  ],
}

