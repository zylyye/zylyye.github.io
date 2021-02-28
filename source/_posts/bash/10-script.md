---
title: Bash 脚本基础
categories: [Bash]
tags: [Linux, bash]
date: 2021-02-26 10:57:27
---

---

Bash 脚本是多个 Bash 命令的集合，使用脚本的好处是可以方便处理复杂任务并且复用。

<!-- more -->

## 指定脚本解释器

---

脚本开头的 `#!` 称为 Shebang 字符，用来指定脚本的解释器，该行称为 Shebang 行。

通常使用 Bash 作为脚本解释器：

```shell
#!/bin/bash
```

为了更好的兼容其他系统，防止 bash 命令不在 /bin 目录下，推荐使用 env 命令来启动解释器：

```shell
#!/usr/bin/env bash
```

env 命令会从 PATH 中寻找第一个匹配的 bash 命令。同理，也可以根据需要指定其他解释器：

```shell
#!/usr/bin/env python
```

env 命令的一些参数：

- -i, --ignore-environment ： 不带环境变量启动
- -u, --unset=NAME ： 从环境变量中删除一个变量

如果没有指定解释器，则需要使用命令执行：

```shell
bash script.sh
```

## 注释

---

Bash 只支持单行注释，注释内容使用 # 开头：

```shell
# This is comment
some statements here
```

## 向脚本传递参数

---

脚本内部可以是用特殊变量来引用传递给脚本的参数:

- $0 ：脚本文件名
- $1 ~ 9 ：脚本的第一个参数到第九个参数，多于 9 个的参数使用 ${num} 的方式引用
- $# ：脚本参数的数量
- $@ ：所有参数，使用空格隔开
- $*  ：所有参数，参数使用 $IFS 值的第一个字符分隔

可以使用 for 循环遍历接收的参数：

```shell
for i in "$@"; do
	echo $i
done
```

如果传递参数时，有空格的参数可以使用逗号包裹：

```shell
./script.sh "a b" c d 
```

## shift 命令

---

shift 命令会将参数列表向前移动一位，例如

```shell
echo $@ // 1 2 3 4
echo $1 // 1
shift
echo $1 // 2
```

shift 命令可以接受一个数字参数，指定移动几位，默认移动一位：

```shell
shift 2
```

## 使用 getopts 解析配置项参数

---

一般命令通常都会接收配置项参数，例如：

```shell
-v  显示版本号
-f  指定文件名称
-i  操作时询问
```

在脚本中，可以使用 getopts 便捷的解析这种参数：

```shell
# getopts optstring name 
# - optstring 配置项，例如有 -i -v -f 这样的配置项，则为 ivf，带有值需要加逗号，即 ivf:
# - name 解析到的配置项会初始化到 name 变量中
while getopts 'ivf:' opt; do
	case "$opt" in
		v)
			echo "show version"
			;;
		i)
			echo "ask for confirm"
			;;
		f)
			# 配置项的值使用 $OPTARG 访问
			file_name="$OPTARG"
			echo "get filename $file_name"
			;;
		# 没有指定的配置项，解析的结果为 ?, 例如传了不在 ivf: 中的参数 -a
		?)
			echo "syntax error!"
			echo "usage: script [-v] [-i] [-f filename]"
			exit 1
			;;
		esac
	done
```

- 另外脚本中的 $OPTIND 变量表示 getopts 解析的配置项索引，从 1 开始，解析结束可以使用 shift 移动参数，来保证后续程序能够使用 $1, $2 来访问非配置参数

```shell
shift $((OPTIND - 1))
```

## 配置项终止符 `--`

---

有时候调用命令不想要指定配置项，为了防止把传递给命令的参数误当作了配置参数，可以使用 `--` 来表示没有配置项：

```shell
ls -- --filename.txt // 文件名叫做 --filename.txt
```

## exit 退出值

---

执行 exit 时，脚本会立即终止并返回退出值，该命令会默认返回 0，可以指定返回值，例如： exit 123

## 命令执行结果

---

脚本中的变量 $? 保存了上个命令退出时的返回值，通常 0 表示执行成功，非 0 表示失败，可以使用 if 进行判断，然后决定后续动作：

```shell
cd /home/abc
if [ "$?" = "0" ]; then
	some statements
else
	other statements
fi
```

由于 if 可以直接将 0 当作 true，因此可以简写为：

```shell
if cd /home/abc; then
...
```

甚至可以使用逻辑运算符 &&, || 进一步简化:

```shell
cd /home/abc && ...
```

## source 命令

---

脚本每次执行时，会新建一个子进程，所有的变量都在子进程中的环境设置，**如果希望对当前环境生效，或者共享当前环境中的环境变量**，可以使用 source 命令执行：

```shell
source script.sh
# source 可以简写为 .
. script.sh
```

## alias 命令

---

alias 可以为命令指定别名，例如：

```shell
alias ll='ls -alG'
```

---

