## 基本配置

　　英文等宽字体用Fira，中文用苏新诗柳楷，开启连字，不显示缩略图，在视区宽度和wordWrapColumn的较小值处换行：

```json
{
	"editor.fontFamily": "'Fira Code Retina', '方正苏新诗柳楷简体-yolan'",
	"editor.fontSize": 16,
	"editor.fontLigatures": true,
	"editor.minimap.enabled": false,
	"editor.wordWrapColumn": 200,
	"editor.wordWrap": "bounded"
}
```

　　底部状态栏可见，隐藏上方菜单栏，删除文件时是否提示：

```json
{
	"workbench.statusBar.visible": true,
	"window.menuBarVisibility": "toggle",
	"explorer.confirmDelete": false
}
```

## 常用插件

- [简体中文语言包](https://marketplace.visualstudio.com/items?itemName=MS-CEINTL.vscode-language-pack-zh-hans)

- [TabNine](https://marketplace.visualstudio.com/items?itemName=TabNine.tabnine-vscode)：自动补全工具

- [A-super-themes](https://marketplace.visualstudio.com/items?itemName=xuedao.super-themes)：霓虹灯主题 发光字体

- [A-super-translate](https://marketplace.visualstudio.com/items?itemName=xuedao.super-translate)：划词翻译

- [Bracket Pair Colorizer 2](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)：括号配对

- [Icon Fonts](https://marketplace.visualstudio.com/items?itemName=idleberg.icon-fonts)：可以输入awesome font

- [vscode-icons](https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons)：文件图标

- [Clang-Format](https://marketplace.visualstudio.com/items?itemName=xaver.clang-format)：格式化工具

- [C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)：C和C++用clang-format格式化<br>

```json
{
	"[cpp]": {"editor.defaultFormatter": "xaver.clang-format"},
	"[c]": {"editor.defaultFormatter": "xaver.clang-format"}
}
```

- [Prettier-Code](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

```json
{
	"[json]": {"editor.defaultFormatter": "esbenp.prettier-vscode"},
	"[jsonc]": {"editor.defaultFormatter": "esbenp.prettier-vscode"}
}
```

- [shell-format](https://marketplace.visualstudio.com/items?itemName=foxundermoon.shell-format)

```json
{
	"shellformat.path": "/usr/bin/shfmt"
}
```

- [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

- [vscode-lua](https://marketplace.visualstudio.com/items?itemName=trixnz.vscode-lua)

- [Julia](https://marketplace.visualstudio.com/items?itemName=julialang.language-julia)

- [Code Runner](https://marketplace.visualstudio.com/items?itemName=formulahendry.code-runner)：主流的编程语言几乎都支持，右键直接运行

- [Markdown Preview Enhance](https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced)

```json
{
	"markdown-preview-enhanced.enableExtendedTableSyntax": true,
	"markdown-preview-enhanced.enableCriticMarkupSyntax": true,
	"markdown-preview-enhanced.enableScriptExecution": true,
	"markdown-preview-enhanced.liveUpdate": false,
	"markdown-preview-enhanced.mathRenderingOption": "MathJax", # 配置文件是.mume/mathjax_config.js
	"markdown-preview-enhanced.printBackground": true
}
```

- [LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop)：用evince做pdf浏览器 默认xelatex编译一次

```json
{
	"latex-workshop.latex.recipes": [
        {
            "name": "xelatex",
            "tools": [
                "xelatex"
            ]
        },
        {
            "name": "pdflatex ➞ bibtex ➞ pdflatex × 2",
            "tools": [
                "pdflatex",
                "bibtex",
                "pdflatex",
                "pdflatex"
            ]
        },
        {
            "name": "xelatex ➞ bibtex ➞ xelatex × 2",
            "tools": [
                "xelatex",
                "bibtex",
                "xelatex",
                "xelatex"
            ]
        },
    ],
    "latex-workshop.latex.tools": [
        {
            "name": "xelatex",
            "command": "xelatex",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-pdf",
                "-outdir=%OUTDIR%",
                "%DOC%"
            ],
            "env": {}
        },
        {
            "name": "pdflatex",
            "command": "pdflatex",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "%DOC%"
            ],
            "env": {}
        },
        {
            "name": "bibtex",
            "command": "bibtex",
            "args": [
                "%DOCFILE%"
            ],
            "env": {}
        }
    ],
    "latex-workshop.view.pdf.viewer": "external", # tab
    "latex-workshop.view.pdf.external.viewer.command": "evince",
    "latex-workshop.view.pdf.external.synctex": {
        "command": "evince",
        "args": [
            "%LINE%",
            "%PDF%",
            "%TEX%"
        ]
    },
}
```
