---
title: Bash 行操作
categories: [Bash]
tags: [linux, bash]
date: 2021-02-22 11:49:26
---

---

Bash 内置了 Readline 库，提供了许多命令行操作的功能。

<!-- more -->

## 设置编辑模式

---

Bash 默认使用的是 Emacs 快捷键，可以临时更改成 vi 快捷键:

```shell
set -o vi
```

如果想要永久改动，需要修改 `~/.inputrc`文件：

```shell
set editing-mode vi
```

关闭 Readline 库功能：

```shell
bash --noediting
```

## 光标移动

---

- ctrl + a ： 移动到行首
- ctrl + e ：移动到行尾
- ctrl + b ：向前移动一个字符
- ctrl + f  ：向后移动一个字符
- alt + b  ：移动到当前单词词首
- alt + f   ：移动到当前单词词尾

## 清除屏幕

---

ctrl + l

## 编辑操作

---

有关编辑命令行内容的操作：

- ctrl  + d :  删除光标位置的字符，**如果没有字符则退出终端**
- ctrl + w ：删除光标位置前面的单词
- ctrl + t  ：交换光标位置和前面位置的字符
- alt + t   ：光标位置的词与前面的词交换位置
- alt + l   ：光标位置至词尾转化为小写
- alt + u  ：光标位置至词尾转化为大写

剪切粘贴：

- ctrl + k ：剪切光标位置到行尾的文本
- ctrl + u ：剪切光标位置到行首的文本
- alt + d  ：剪切光标位置到词尾
- alt + backspace ：剪切光标位置到词首
- ctrl + y ： 在光标位置粘贴文本

Alt 键可用 Esc 键代替。

## 操作历史

---

- Bash 的操作历史默认保存在 `~/.bash_history` 中（zsh 保存在 .zsh_history 中），环境变量`$HISTFILE`保存了这个路径。

- 语法 `!string` 可以从历史记录中匹配最近开头是 string 的命令。

```shell
echo hahaha
!e // 匹配 echo hahaha
```

注意，类似于模式扩展， `!str`会扩展历史命令，放在字符串中要小心，可以在 `!`前加反斜杠转义。

- 使用 ctrl + r 可以搜索历史命令

### history

使用 history 命令可以查看历史执行过的命令，越近的命令行号越大。

- 如果不希望保存本次的操作历史，可以设置环境变量 `HISTSIZE` 为 0

```shell
export HISTSIZE=0
```

- 可以使用行号执行对应的命令：

```shell
!100
```

- `history -c` 可以清除操作历史



## 其它相关命令

---

- `!!`  ：执行上一条命令
- `!n` ： 执行历史中的第 n 号命令
- `!-n` ： 执行之前第 n 条命令
- `!string` ： 执行最近一个以 string 开头的命令
- `!?String`  ： 执行最近一条包含 string 的命令
- `^str1^str2` ：执行最近一条包含 str1 的命令，将其替换为 str2

---

