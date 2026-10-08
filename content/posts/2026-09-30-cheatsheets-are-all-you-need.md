---
title: Cheatsheets are all you need
date: 2026-09-30
tags:
  - Tools
  - Software
author: Square Zhong
description: 还不够
---

计算机上有很多工具都可以通过命令行的方式调用。想要快速入门一个工具，我们第一反应可能是去找一个教程，但互联网上教程的质量层次不齐，有的废话连篇，有的鞭辟入里但是从盘古开天地开始讲；也可能是直接去找官方文档，然后发现有的文档一上来列个几十个命令行参数，人直接傻了。

更高效的方式其实是直接看 Cheatsheets 里简单的使用范例，拿来就能用，cheatsheets are ~~all~~ you need。

这里得夸一嘴 Pytorch 的文档，有解释（有的还有论文或者好的可视化说明链接）有参数列表有示例，顶级文档。

## Cheatsheets

推荐开源项目 reference，收录了大部分开发工具的 Cheatsheets

- [中文版](https://wangchujiang.com/reference/)：内容比英文版更多一些
- [English Version](https://quickref.me/)

通常命令行工具通常会遵循类似下面的范式：

```text
command [subcommand] [options] [arguments]
```

比如 `git commit -m "I do not want to write commit message"`

- `git` 就是 command
- `commit` 是 subcommand
- `-m` 是 option，后面的内容是 arguments

## man

当你需要较为深入地使用一个命令行工具时，就到了 `man` 出场的时候了。(`man` 是 manual，不是牢大)

抛开源代码和标准（比如 POSIX、RFC 等）不谈，man 可以被视作准第一手资料。按照质量和可靠程度排名，`man` > 发行版 wiki > Stack Overflow / Reddit > 稀土掘金、博客园 / 知乎 >> CSDN。

`man` 还有一个容易被忽视的优势，运行 `man` 命令得到的手册通常与安装版本匹配。

使用方法：终端中运行 `man <command>`，比如直接 `man man` 查看 `man` 的 manual。

你也可以访问 [man7.org](https://man7.org/linux/man-pages/index.html) 在线查看 Linux 命令的手册。

### man 的快捷操作

 `man` 默认会使用 `less` 作为翻页器，常用快捷键如下：
 
- 向下翻页：**`SPACE`**
- 向上翻页：**`b`**
- 向下逐行滚动： **`j`** 或 **`ENTER`** 或 **方向键**
- 向上逐行滚动：**`k`** 或 **方向键**
- 向后搜索：**`/`**，输入你要查找的内容然后回车。`n` 跳转下一个匹配项，`N` 跳转上一个
- 向前搜索：**`?`**，输入你要查找的内容然后回车。跳转方式如上
- 退出：**`q`**

## Why

如果你在使用一些命令行工具的时候被其强大深深吸引，你可能会想知道一个命令是如何设计以及为什么要那么设计。这时候就是翻源码或者阅读优质博客的时候了。

祝你用得开心。
