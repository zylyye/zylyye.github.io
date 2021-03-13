---
title: 循环
categories: [Bash]
tags: [linux, bash]
date: 2021-03-12 19:16:03
---

---

Bash 中有三种循环 while、 until、 for。

<!-- more -->

## while 循环

while 循环的结构如下：

```shell
while condition ; do
  ...
done
```

**其中 condition 与 if 的语法一致。**

例如:

```shell
num=0
while ((num++ < 10)); do
  echo $num
done
```

## until 循环

until 与 while 循环刚好相反，until 等到条件满足时退出循环，语法结构与 while 一致：

```shell
until condition ; do
  ...
done
```

## for .. in 循环

for .. in 循环可以用于遍历列表：

```shell
for name in list ; do
  // todo with $name
done
# 或者
for name in $var1 $var2 $var3 ; do
  ...
done
```

list 可以是使用空格隔开的参数列表，例如：`a b c d e`，如果不指定 list，则默认使用 `$@`:

```shell
for name ; do
  ...
done
# 等价于
for name in "$@" ; do
  ...
done
```

## for 循环

Bash 支持和很多高级语言一样的 for 循环：

```shell
for (( exp1; exp2; exp3 )); do
  ...
done
```

exp1 初始化循环条件，exp2 作为循环判断条件，每次循环开始时执行，exp3 每次循环结束后执行。**注意括号内的变量不需要加 `$`。**

例如：

```shell
for (( i=0; i<10; i++ )); do
  echo $i
done
```

也可以省略括号中的语句：

```shell
for ((;;)); do
  ...
done
```

## break, continue

可以在循环体中使用 break 和 continue：

- break ：跳出循环
- continue ：终止本次循环，继续下次循环

## select 语句

select 语句可以生成菜单供用户选择，其语法与 select 基本一致：

```shell
select name in list ; do
  echo $name
done
```

用户输入的内容会保存在 `$REPLY` 变量中，select 语句会一直循环，需要使用 break 手动退出。

---

