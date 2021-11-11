---
title: Bash 函数
categories: [Bash]
tags: [linux, bash]
date: 2021-03-14 16:17:15
---

---

Bash 脚本中可以将需要复用的代码定义成函数，方便脚本内重复调用。

<!-- more -->

## 函数使用基础

Bash 中的函数定义方式有两种：

```shell
# 第一种
fn() {
  ...
}
# 第二种
function fn() {
  ...
}
```

**注意函数在执行时不会创建子 shell 环境**，且函数执行的优先级高于同名脚本，低于别名。

函数调用方式：

```shell
# 像执行命令一样
fn
# 也可以传递参数
fn arg1 arg2
```



### 查看和删除函数

- 可以使用 `unset -f function_name` 删除一个函数
- `declare -f` ：查看所有已定义的函数，包含函数具体定义内容，也可以指定函数名查看具体的函数 `declare -f function_name`
- `declare -F` ：查看所有已定义的函数的函数名，不包含函数体

## 函数参数

函数调用时可以传递参数，函数内部可以像 Bash 脚本一样读取参数：

```shell
fn() {
  echo $0  // 函数所在脚本名
  echo $1  // 传递给函数的第一个参数
  echo $@  // 函数的全部参数
}
```

## 函数的返回值 return

函数中可以使用 return 返回一个值，调用者可以使用 `$?` 来读取：

```shell
fn() {
  ...
  return 10
}
# 调用
fn
echo $?
```

**return 与 exit 的区别：**

- return 终止当前函数，返回整定值
- exit 终止当前 shell，返回退出状态码(exit 后指定的值)

## 函数中的局部变量

函数中可以访问脚本中的变量，函数中定义的变量也可以被脚本访问，可以使用 local 来定义只有在函数中才可以访问的局部变量：

```shell
fn() {
  local a=1
}
```

---

