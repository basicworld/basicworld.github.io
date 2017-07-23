---
layout: single
title: Sublime3 配置
category: tool
tags: tech
description: Sublime3 配置教程
---
sublime3相对于sublime2有很多改进，目前发现的：颜色好看了、自定义设置与系统设置分离、插件包配置简单了。这里介绍一些python开发必备的插件。

## lincense

sublime3的lincense：还没买，70刀，略贵啊！


## sublimelinter + sublimelinter-pep8

python pep8 代码校验

网址：[sublimelinter](https://gaohaoyang.github.io/2015/03/26/sublimeLinter/)

配置参考：<http://sourabhbajaj.com/mac-setup/SublimeText/SublimeLinter.html>

1. 安装并配置sublimelinter：在sublimelinter的user setting里配置

```
{
    "user": {
        "debug": false,
        "delay": 0.25,
        "error_color": "D02000",
        "gutter_theme": "none",
        "gutter_theme_excludes": [],
        "lint_mode": "background",
        "linters": {
            "pep8": {
                "@disable": false,
                "args": [],
                "disable": "",
                "enable": "",
                "excludes": [],
                "ignore": "",
                "max-line-length": null,
                "rcfile": "",
                "select": ""
            }
        },
        "mark_style": "outline",
        "no_column_highlights_line": false,
        "paths": {
            "linux": [],
            "osx": [
                "/usr/local/bin/"
            ],
            "windows": []
        },
        "python_paths": {
            "linux": [],
            "osx": [
                "/usr/local/bin/"
            ],
            "windows": []
        },
        "rc_search_limit": 3,
        "shell_timeout": 10,
        "show_errors_on_save": false,
        "show_marks_in_minimap": true,
        "syntax_map": {
            "html (django)": "html",
            "html (rails)": "html",
            "html 5": "html",
            "php": "html",
            "python django": "python"
        },
        "warning_color": "DDB700",
        "wrap_find": true
    }
}
```

2. 安装sublimelinter-pep8 重启就可以了

## sublimerepl

运行在sublime运行py代码

配置步骤

1. 安装sublimerepl
2. 在sublime的key binding中配置如下：

```
[
    {"keys":["f5"],
    "caption": "SublimeREPL: Python",
    "command": "run_existing_window_command",
    "args":
        {
            "id": "repl_python_run",
            "file": "config/Python/Main.sublime-menu"
        }
    }

]

```

## sublimecodeintel

代码提示工具

配置:

1. 安装
2. 重启
3. 等待一会儿--反应慢

快捷键使用：
```
  + Shortcuts for jump to definition have changed:

    For Mac OS X:
      * Jump to definition = ``Control+Click``
      * Jump to definition = ``Control+Command+Alt+Up``
      * Go back = ``Control+Command+Alt+Left``
      * Manual CodeIntel = ``Control+Shift+space``

```
