---
title: 变量
categories: [bash]
tags: [linux, bash]
p: bash/variable
date: 2021-02-02 15:27:09
---

## 简介

---

Bash 中有两类变量：

- **环境变量**
- **自定义变量**

<!-- more -->

### 环境变量

环境变量可以通过 `env` 或者 `printenv` 命令查看所有的环境变量。

**常见的环境变量：**

- `BASHPID`： Bash 进程ID

- `BASHOPTS`:  当前 Shell 的参数，可由 `shopt`命令修改

- `DISPLAY`:  图形环境显示器的名字，`:0` 表示第一个显示器

- `EDITOR`: 默认文本编辑器

- `HOME` ：用户主目录

- `IFS`：词之间分隔符，默认空格

- `LANG`：字符集以及语言编码，例如 `zh_CN.UTF-8`

- `PATH`：可执行程序搜索目录列表

- `PS1`: Shell 提示符

- `PS2`: 输入多行命令，Shell 次要提示符

- `PWD`： 当前工作目录

- `RANDOM`： 返回 0 ~ 32767 之间的随机数

- `SHELL`：Shell 的名字

- `SHELLOPTS`:  启动当前 Shell 的 `set`  命令的参数

- `TERM`:   当前终端类型名

- `UID`： 当前用户的 ID 编号

- `USER`：当前用户的用户名

  

查看单个变量名可使用 `printenv` 或 `echo`

```shell
printenv PATH
echo $PATH
```

​                                                                                                                    

### 自定义变量

**自定义变量只在当前 Shell 环境下有效**

可以使用 `set` 查看当前 Shell 中所有的**环境变量**和**自定义变量**。



## 创建变量

---

变量的命名规则：

- 字母，数字和下划线字符组成
- 第一个字符必须是字母或下划线
- 不能有空格或标点符号

变量声明时，等号两边**不能空格**：

```shell
var_name=var_value
```

- Bash 变量只有字符串类型
- 变量可以重复赋值



## 变量读取

---

使用 `$` 符号读取变量：

```shell
echo $var_name
# 或
echo ${var_name}
```

- 如果变量的值是变量，可以使用 `${!var_name}` 读取



## 删除变量

---

使用 `unset`可以删除一个变量。

---

## 输出变量

---

自定义变量仅在当前 Shell 下有效，子 Shell 读取不到，可以使用 `export` 来使改变量能被子 Shell 访问。

```shell
export a=1
# 或者分两步执行
a=1
export a
```

- 注意在子 Shell 中修改 export 的变量，不会影响到父 Shell 的值



## 特殊变量

---

- `$?` ： 上个命令的退出状态码，一般成功返回 0
- `$$`： 当前 Shell 的进程ID，例如可以用来命名临时文件`touch file.$$`
- `$_`： 上个命令的最后一个参数
- `$!`： 最近一个后台执行的命令的进程 ID
- `$0`： 当前 Shell 名称或脚本名
- `$-`： 当前 Shell 的启动参数
- `$@` 和 `$#`：表示脚本的参数数量



## 变量的默认值

---

- `${varname:-word}`:  （**返回默认值**）如果变量不存在或为空，则返回 word，否则返回变量的值
- `${varname:=word}`：（**设置默认值**）如果变量不存在或为空，则被设置并返回 word，否则返回变量的值
- `${varname:+word}`:  （**存在时返回设置值**）用来测试变量是否存在，如果存在且不会空则返回word，否则返回空值
- `${varname:?message}`： （**不存在则退出并提示指定信息**）如果变量不存在或为空，则打印 `varname: message`，且中断脚本执行，用来防止变量未定义。
- `${1:?'变量不存在'}`： 变量名可以使用 1~9，表示脚本的参数



## declare 命令

---

declare 命令用来声明变量，可以对变量增加一些特性限制。

```shell
declare OPTIONS VARIABLE=value
```

`declare` 在函数内部声明的变量只在函数内部有效，等同于 `local`命令，`declare` 命令默认合宿出当前环境的所有变量，包括函数在内，等同于不带任何参数的 `set`

OPTIONS 可选值：

- `-a`:  声明数组变量
- `-f`：输出所有函数定义
- `-F`：输出所有函数名
- `-i`：声明整数变量

声明的整数变量，可直接进行数学运算

```shell
declare -i num1=1 num2=10
# 只要将 sum 声明为整数变量，就会进行整数运算
declare -i sum
sum=num1+num2
echo $sum // 输出 11
```

- `-x`：声明为环境变量，等同于 export

```shell
declare -x a=1122
# 等价与
export a=1122
```

- `-r`：声明为只读变量，不能更改和 `unset`

- `-u`：声明变量为大写字母，会自动将变量的值转化为大写字母
- `-l`：声明变量为小写字母，会自动将变量的值转化为小写字母
- `-p`： 输出变量信息



## `readonly` 命令

---

`readonly`命令等价于 `declare -r`，用于声明只读变量。

`readonly` 有三个参数：

- `-f`： 声明的变量为函数名
- `-p`：打印出所有的只读变量
- `-a`：声明的变量为数组



## let 命令

---

使用 `let` 命令声明变量可以直接进行算术计算：

```shell
let a=1+2
let b=a++
```

如果有空格，需要使用引号包含整个表达式

```shell
let "a = 1 + 2"
```

多个使用空格隔开

```shell
let c=1 d=2
let "c = 1" "d = 2"
```

---

