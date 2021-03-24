---
title: mktemp 与 trap
categories: [Bash]
tags: [linux, bash]
date: 2021-03-24 22:29:47
---

---

mktemp 命令可以创建临时文件或文件夹，trap 命令可用于指定当 shell 触发某个信号时执行指令（例如 shell 退出时清理临时文件）。

<!-- more -->

## 创建临时文件

使用 mktemp 可以创建临时文件：

```shell
temp_file=$(mktemp) || exit 1
```

mktemp 支持的一些参数：

-  `-d`  创建临时文件夹：

```shell
temp_dir=$(mktemp -d) || exit 1
```

- `-p` 指定临时文件所在目录
- `-t` 指定临时文件名的模板，如 `mytemp.XXXXXX` 其中 X 会使用随机字符替换

## 捕捉 shell 信号

使用 trap 命令可以捕捉shell 执行过程中触发的信号，指定执行某个命令。

语法：

```shell
trap [动作] [信号1] [信号2]...
```

使用 `trap -l` 可以查看系统信号列表，常见的信号如下：

- 1) HUP    挂起，一般是与终端连接丢失时触发
- 2) INT      使用 Ctrl + C 时触发
- 3) QUIT   按下 Ctrl + / 意图退出脚本时触发
- 9) KILL    进程被杀死时
- 15) TERM 终止信号，kill 默认信号
- 0) EXIT 脚本终止信号，**只要脚本退出就会触发**

例如退出时删除临时文件：

```shell
trap 'rm -f "$temp_file"' EXIT
temp_file=$(mktemp) || exit 1
```

注意 trap 命令应放在脚本开头，防止还没有执行到 trap 命令就触发了信号。

---

