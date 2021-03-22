---
title: Bash 技巧与提示
categories: [Bash]
tags: [linux, bash]
date: 2021-03-21 15:01:59
---

---

编写脚本时常常会出现各种错误，导致意料之外的情况发生，下面说明如何尽可能的降低这些错误带来的风险，以及如何排查错误位置。

<!-- more -->

## 指定运行行为

使用 set 可以设置脚本中发生错误时，脚本该如何进行。

### set 指定行为

- `set -u`  如果遇到不存在的变量，脚本报错，终止执行，另一种写法：`set -o nounset`
- `set -x`  打印脚本执行的内容，与 `bash -x` 等价，可以使用 `set +x` 关闭
- `set -e`  脚本发生错误时，终止执行，可以使用 `set +e` 关闭
- `set -o pipefail`  管道命令中的某个环节命令出错，管道命令就返回错误，默认情况下，**管道命令执行的结果由最后一个管道命令决定**
- `set -E`  设置 set -e 时，函数内部的错误不会触发 trap，`set -E` 可以纠正该行为

其他的 set 参数

- `set -n`  不运行命令，只检查语法
- `set -f`  不对通配符进行文件名扩展
- `set -v` 打印 shell 接收的每一行输入
- `set -o noclobber`  防止重定向覆盖已存在的文件

建议脚本前都可以加入这些配置，提高安全性

```shell
set -Eeuo pipefail
```

### shopt 命令调整 Shell 参数

shopt 和 set 功能类似，shopt 命令语法：

```shell
shopt OPTION optionName
```

一般选项：

- `-s`  打开某个特性
- `-u`  关闭某个特性
- `-q`  查询某个特性

例如：

```shell
shopt -q globstar
echo $? // 0-开启 1-关闭
```

## 脚本调试

Bash 中提供了一些可用于调试脚本的参数和变量：

- `bash -x`   打印执行的命令，类似与 `set -x`

### 一些用于调试的环境变量

- LINENO  返回脚本的当前行号
- FUNCNAME 返回函数的调用堆栈，是个数组，FUNCNAME[0] 是当前调用函数名
- BASH_SOURCE 返回脚本的调用堆栈，BASH_SOURCE[0] 是当前调用脚本名
- BASH_LINENO  返回每一轮调用的行号，BASH_LINENO[0] 是当前脚本被调用的行号

---

