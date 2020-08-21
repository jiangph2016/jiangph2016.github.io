---
layout: post
title: 【Mac】在Mac下使用vscode来写LaTeX文件
category: 笔记
tags: Mac
keywords: 
---
* content
{:toc}

# 在Mac下使用vscode来写LaTeX文件

## 安装LaTeX


在[MacTeX官网](http://www.tug.org/mactex/)安装TeXShop

![](https://humanlee1011.github.io//img/post_img/2019-02-25/Tex-folder.png)
![](https://humanlee1011.github.io//img/post_img/2019-02-25/tex-folder-contents.png)

## 在VScode中配置LaTeX

### 安装扩展LaTeX Workshop
搜索扩展`LaTeX WorkShop`
![](/assets/img/notes/latex-workshop.png)

### 打开配置文件

使用快捷键`cmd` + `，`打开VScode的设置，点击`拓展`->`LaTeX`，或者直接在搜索栏中输入`recipes`或`tools`

然后点击`在setting.json中设置`

![](/assets/img/notes/latex-setting.png)


### 修改配置文件

修改两项：`latex-workshop.latex.tools`和`latex-workshop.latex.recipes`


```
"latex-workshop.latex.tools": [
    
        {
            "name": "xelatex",
            "command": "xelatex",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-pdf",
                "%DOC%"
            ]
        },
        {
            "name": "latexmk",
            "command": "latexmk",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-pdf",
                "%DOC%"
            ]
        },
        {
            "name": "pdflatex",
            "command": "pdflatex",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "%DOC%"
            ]
        },
        {
            "name": "bibtex",
            "command": "bibtex",
            "args": [
                "%DOCFILE%"
            ]
        }
    ],
    "latex-workshop.latex.recipes": [
        
        {
            "name": "xelatex -> bibtex -> xelatex*2", //如果带有BibTeX，要编译三次
            "tools": [
                "xelatex",
                "bibtex",
                "xelatex"
            ]
        },
        {
          "name": "pdflatex -> bibtex -> pdflatex*2",
          "tools": [
              "pdflatex",
              "bibtex",
              "pdflatex",
              "pdflatex"
          ]
        },
    ]
```

参考：[在Mac上用VSCode敲LaTeX](https://www.dazhuanlan.com/2020/02/02/5e3686276fa28/?__cf_chl_jschl_tk__=8b47364750fd03f0575d8b953414d0474d5bce36-1590337374-0-AbFv1LvDEP7zXxnn30FXwVIuuDV4fv1aJZVahre033KmnBCtncGL4x4VxUaW82GzfdJnmtDaGlOwwe5XVfd2rwDacTVnSzwRjtgL187lwW5eJCT2OtLA1bJx98Brirr_0t6X4xLXIG4ARRQ9EFVCbzb0STVBbodKwK3yH5G8T4iIT8zcUpfp0SLFwMNqtcfY588xHKe7HehHjJ3DHy0cJPj2h_Ow5ts-FbuhA9G7iOv3lIbxoML-Dt1V1Wh0Sj9MT16ZGEUMlEB2qTbQ_u5lxmVHHSHRdtK0dcRVejP9fgGAE1Qa9rGfsNl3E8uqeuhB6g)


