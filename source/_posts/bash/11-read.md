---
title: read 命令
categories: [Bash]
tags: [linux, bash]
date: 2021-03-02 18:40:04
---

可以使用 read 命令来读取用户输入的内容、密码以及文件内容，并存入变量以备使用。

<!-- more -->

## 用法

- read 命令的格式：

```shell
read [-options] [variable...]
```

例如：

```shell
read -p "input username:" user
read -sp "input password:" password
```

- read 后如果不指定接收的变量名称，则将值赋给 REPLY 变量：

```shell
read
> 123
echo $REPLY // 123
```

- read 可以同时指定多个变量，默认情况下，**用户可以使用空格分隔多个变量的值，read 指定的最后一个变量接收剩下的所有值：**

```shell
read a b c
> aa bb cc dd
echo $a // aa
echo $b // bb
echo $c // cc dd
```

- 可以使用 read 命令读取文件，每次读取一行：

```shell
while read oneline
do
	echo $oneline
done < filename
```

## read 的参数

- -t ： 读取输入时设置超时秒数，也可以使用环境变量 TMOUT 来指定超时秒数
- -p ：指定输入的提示信息
- -n ：指定只读取若干个字符：

```shell
read -n 1 a
> y
echo $a // y
```

- -e ：允许使用 readline 库提供的快捷键，例如自动补全，在输入路径时很有用：

```shell
read -e filename
```

- -s ：用户输入不显示出来，适合输入密码

**其他不太常用的参数：**

- -a ：将用户的输入保存为一个数组：

```shell
read -a names
> a b c
echo ${a[0]} // a
```

- -d (delimiter) ：指定输入结束符，默认时换行结束

- -r ：raw 模式，不会转义输入的反斜杠转义字符

- -u fd ：使用文件描述符 fd 作为输入

  Shell 启动时有三个标准的文件描述符：

  - stdin 0 号描述符，代表输入设备，进程从中读取数据
  - stdout 1 号描述符，进程往其中写入数据
  - stderr 2 号描述符，进程往其中写入错误信息

## IFS 变量

IFS，Internal Field Separator，内部字段分隔符，默认值为空格、Tab、换行符。

使用该变量可以为 read 指定读取分隔符：

```shell
IFS=":" read user pw uid gid name home shell <<< $(cat /etc/passwd | grep root)
# 将 IFS 和 read 写在一起，这样执行过后，IFS 回自动恢复默认值，其中 <<< 是 Here 文档
```



