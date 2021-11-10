---
title: 基本语法
categories: [Bash]
tags: [linux, bash]
p: bash/basic-grammar
date: 2021-01-27 17:06:00
---

## 命令格式

---

`command [arg1 ... [argN]]`

- 命令的参数使用空格隔开
- 如果命令过长，可以使用饭斜杠 \ 进行换行
- 一行可以放多个命令，多个命令使用分号 (;) 隔开
- 命令可以使用逻辑组合 && 与 ||

<!-- more -->

## echo 命令

---

`echo [content]`

- -n 取消末尾换行
- -e  启用转义字符 (注意 zsh 默认启用，bash 默认不启用，即 bash 中 \n 原字符输出，而不是换行)



## type 命令

---

`type command_name`

Bash 中内置了很多命令，type 用来查询命令是内置命令还是外部命令

- -a 查看命令所有的定义
- -t 查看命令类型  alias/keyword/function/builtin/file



## 快捷键

---



- Ctrl + L   清除屏幕
- Ctrl + C   中止执行
- Shift + PageUp 向上滚动
- Shift + PageDown 向下滚动
- Ctrl + U 剪切到行首
- Ctrl + K 剪切到行尾
- Ctrl + D 关闭 Shell 会话
- Ctrl + A 移到行首
- Ctrl + E 移到行尾
- 方向上/下 浏览执行历史

