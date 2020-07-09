## 基本配置

　　英文等宽字体用Operator Light，中文用方正龙爪，开启连字(需字体支持)，不显示缩略图，在视区宽度和wordWrapColumn的较小值处换行：

```json
{
	"editor.fontFamily": "'Operator Mono Light', 'FZLongZhao-R-GB'",
    "editor.fontSize": 17,
    "editor.fontLigatures": true,
    "editor.minimap.enabled": false,
    "editor.wordWrapColumn": 200,
    "editor.wordWrap": "bounded",
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

```json
{
    "tabnine.experimentalAutoImports": true,
}
```

- [SynthWave '84](https://marketplace.visualstudio.com/items?itemName=RobbOwen.synthwave-vscode)：霓虹灯主题 发光字体

```json
{
    "workbench.colorTheme": "SynthWave '84",
    "synthwave84.brightness": 1.0,
    "synthwave84.disableGlow": true,
}
```

- [A-super-translate](https://marketplace.visualstudio.com/items?itemName=xuedao.super-translate)：划词翻译

- [Bracket Pair Colorizer 2](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)：括号配对

- [Icon Fonts](https://marketplace.visualstudio.com/items?itemName=idleberg.icon-fonts)：可以输入awesome font

- [vscode-icons](https://marketplace.visualstudio.com/items?itemName=vscode-icons-team.vscode-icons)：文件图标

- [C/C++](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)：C和C++用clang-format格式化<br>

- [Clang-Format](https://marketplace.visualstudio.com/items?itemName=xaver.clang-format)：格式化工具

```json
    "clang-format.fallbackStyle": "Google",
    "C_Cpp.clang_format_style": "{ BasedOnStyle: LLVM, UseTab: Never, IndentWidth: 4, TabWidth: 4, AllowShortIfStatementsOnASingleLine: true, AllowShortFunctionsOnASingleLine: true, IndentCaseLabels: true, ColumnLimit: 0, AccessModifierOffset: -4 }",
    "C_Cpp.clang_format_fallbackStyle": "{ BasedOnStyle: LLVM, UseTab: Never, IndentWidth: 4, TabWidth: 4, AllowShortIfStatementsOnASingleLine: true, AllowShortFunctionsOnASingleLine: true, IndentCaseLabels: true, ColumnLimit: 0, AccessModifierOffset: -4 }",
```

- [Prettier-Code](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

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
                "--shell-escape",
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
    "latex-workshop.view.pdf.viewer": "tab", # 若用外置pdf浏览器 则external
    "latex-workshop.view.pdf.external.viewer.command": "evince",
    "latex-workshop.view.pdf.external.synctex": {
        "command": "evince",
        "args": [
            "%LINE%",
            "%PDF%",
            "%TEX%"
        ]
    },
    "[latex]": {
        "editor.defaultFormatter": "James-Yu.latex-workshop"
    },
}
```
