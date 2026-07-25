---
title: All-platform Local LaTeX Solution
date: 2025-04-24
tags: ["LaTeX", "Academic"]
author: Square Zhong
description: Maybe Prism is better.
---

<span style="color:blue">[Updated on 2026-07-25]: Recommend Prism and Overleaf</span>

If you don't want to make things complicated, or you need to collaborate with others, just use [Prism](https://prism.openai.com/) by OpenAI or [Overleaf](https://www.overleaf.com/).

---

**(MacTex/MiKTeX + VSCode + LaTeX Workshop)**

All you need are MacTeX/MiKTeX and LaTex Workshop Extension.

## Installation

### 1. Install local LaTeX environment

- macOS

  Install MacTeX, you can refer to this [link](http://www.tug.org/mactex/mactex-download.html), or if you are a homebrew (highly recommended) user, you can simply use `brew install mactex` to finish this.


- Windows / Linux

  Refer to [miktex](https://miktex.org/download).

### 2. Install **LaTeX Workshop** VS Code extension.

- Configure the LaTeX Workshop. In fact this extension is **out-of-box**, but if you want better experience, you can add following code in `settings.json` ( press *command / ctrl  + shift + P* then type “open” to search for “Preference: Open User Settings (JSON)”

## Configuration

- JSON Configuration for LaTeX Workshop

  ```json
  "latex-workshop.latex.tools": [
      {
          "name": "latexmk",
          "command": "latexmk",
          "args": [
              "-synctex=1",
              "-interaction=nonstopmode",
              "-file-line-error",
              "-pdf",
              "-shell-escape",
              "-outdir=%OUTDIR%",
              "%DOC%"
          ],
          "env": {}
      },
      {
          "name": "xelatex",
          "command": "xelatex",
          "args": [
              "-synctex=1",
              "-interaction=nonstopmode",
              "-file-line-error",
              "-shell-escape",
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
              "-shell-escape",
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
  "latex-workshop.latex.recipes": [
      {
          "name": "pdfLaTeX",
          "tools": [
              "pdflatex"
          ]
      },
      {
          "name": "latexmk",
          "tools": [
              "latexmk"
          ]
      },
      {
          "name": "xelatex",
          "tools": [
              "xelatex"
          ]
      },
      {
          "name": "pdflatex ➞ bibtex ➞ pdflatex`x2",
          "tools": [
              "pdflatex",
              "bibtex",
              "pdflatex",
              "pdflatex"
          ]
      },
      {
          "name": "xelatex ➞ bibtex ➞ xelatex`x2",
          "tools": [
              "xelatex",
              "bibtex",
              "xelatex",
              "xelatex"
          ]
      }
  ],
  "latex-workshop.latex.recipe.default": "xelatex",
  ```

  **Remark:**

  1. Remember to add “,” if you want to add other contents in the json file.
  2. `"-shell-escape"` flag is prepared for the use of minted package.
  3. To modify the default recipe, please use [`latex-workshop.latex.recipe.default`](https://github.com/James-Yu/LaTeX-Workshop/wiki/Compile#latex-workshoplatexrecipedefault) .

## Usage

- Now you can open a “.tex” file then build and preview it.

  If you want to use external pdf viewer, search for “viewer” in extension settings, then choose “external”.

- **Some useful shortcut:**

  *option + command + B*: Build the tex file

  *option + command + J*: Jump from the code to corresponding PDF part

  *command + mouse press*: Jump from the PDF to corresponding code part


## Reference Link

1. https://mathjiajia.github.io/vscode-and-latex/
2. [Visual Studio Code (*vscode*) 配置 LaTeX](https://zhuanlan.zhihu.com/p/166523064)

## Troubleshooting

1. Manually add texlive path if needed

    ```bash
    echo 'export PATH="/usr/local/texlive/2024/bin/universal-darwin:$PATH"' >> ~/.zshrc
    source ~/.zshrc
    ```

    You don’t need to manually add path in config file.

    **Reboot** your mac if your LaTex Workshop still not work properly.
