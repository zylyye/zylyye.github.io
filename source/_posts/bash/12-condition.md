---
title: 条件判断
categories: [Bash]
tags: [linux, bash]
date: 2021-03-10 21:01:38
---

---

Bash 脚本中可以使用 if - else，case 语句 来进行条件判断，对于字符串还支持正则。

<!-- more -->

## if 的结构

```shell
if condition; then
  some statements
elif condition; then
  some statements
fi
```

如果 if 和 then 不写在同一行，则不需要加分号：

```shell
if condition
then
  some statements
fi
# 也可以全部写在同一行
if condition; then some statements; fi
```

if 后的判断可以有多个，多个使用分号隔开，判断的结果根据**最后一个**条件决定:

```shell
if condition1; condition2; condition3; then
  some statements # 只有 condition3 的结果为 true 时才会执行
fi
```

## if 后条件的写法

if 的条件有三种写法：

```shell
# 写法一
command // 例如 test -f /etc/redis/redis.conf
# 写法二
[ expression ]
# 写法三
[[ expression ]]  # 只有该方式支持正则比较
```

方括号 `[` 是 test 的简写形式，因此，方括号中的内容与 `[` 之间需要有一个空格。

例如判断一个文件是否存在且是一个普通文件：

```shell
if test -f /path/to/file ; then
  ...
fi

if [ -f /path/to/file ] ; then
  ...
fi

if [[ -f /path/to/file ]] ; then
  ...
fi
```

if 后面可以使用任意命令作为条件，如果命令返回 0 ，则条件成立，否则不成立。

## 字符串比较

字符常见的比较方式：

- `[ str ]` ：如果字符不为空，则为 true
- `[ -n str ]` ：字符串不为空，则为 true
- `[ -z str ]` ：字符串为空，则为 true
- `[ str1 = str2 ]` 等价于 `[ str1 == str2 ]` ：字符串相等
- `[ str1 != str2 ]` ：字符串不相等
- `[ str1 '>' str2]`，`[ str1 '<' str2 ]` ：字典顺序比较字符串，注意 `>`、`<` 要加引号，否则会被 shell 解释为重定向符

例如：

```shell
str='abc'
if [ -n "$str" ] ; then
  echo "str is not empty"
fi
```

**注意进行字符串判断时，最好将变量放入引号 `""` 中，防止变量为空。**例如当 str 为空时，判断就会变为 `[ -n ]`，此时结果为 true，不符合预期。

## 整数判断

判断的方式：

- `[ num1 -eq num2 ]` ：等于 **eq**ual
- `[ num1 -ne num2 ]` ：不等于 **n**ot **e**qual
- `[ num1 -lt num2 ]` ：小于 **l**ittle **t**han
- `[ num1 -le num2 ]` ：小于等于 **l**ittle or **e**qual
- `[ num1 -gt num2 ]` ：大于 **g**reat **t**han
- `[ num1 -ge num2 ]` ：大于等于 **g**reat or **e**qual

## 正则判断

正则需要在 `[[ ... ]]` 中使用 `=~` 符号进行判断：

```shell
[[ str =~ regex ]]
```

例如：

```shell
num=5
# 判断 num 是否是数字
if [[ "$num" =~ ^-?[0-9]+$ ]] ; then
  echo "num is number"
else
  echo "num is not a valid number"
fi
```

## 逻辑运算

可以使用逻辑运算 `&&`、`||`、`!` 将 test 表达式联结起来：

- `&&` 或者 `-a` ： 逻辑 与
- `||` 或者 `-o` ：逻辑 或
- `!` ：逻辑 非

例如：

```shell
if [[ $a -eq $b && $a -gt 0 ]] ; then
  echo "a = b > 0"
fi
# 注意 [[]] 与 [] 写法的区别
if [ $a -eq $b ] && [ $a -gt 0 ] ; then
  echo "a = b > 0"
fi
# 注意使用括号 () 界定范围时，需要使用斜杠 \ 进行转义
if [[ !\( $a -eq $b || $a > 0 \)]] ; then 
  ...
fi
```

与命令一起使用时，也可以使用算术运算符：

```shell
if [ $a -lt $b ] && cd /home/$username ; then
  ...
fi
```

## 算术判断

可以使用 `((...))` 进行算术判断：

```shell
if ((3 > 2)); then
  ...
fi
```

**注意，`((...))` 运算的结果如果非零，则视为 true，与命令返回值判断相反。**

```shell
((0)) false
((1)) true
((a = 3)) true
```

## case 语句结构

case 语句的结构：

```shell
case expression in
  pattern1 )
    commands ;;
  pattern2 )
    commands ;;
  ...
esac
```

expression 可以是一个表达式或者变量， pattern 则是匹配的模式，可以使用通配符：

```shell
a) # 匹配 a
a|b) # 匹配 a 或 b
???) # 匹配三个字符
*.txt) # 匹配 .txt 结尾的名称
*) # 匹配任意字符串，可以作为默认匹配条件
```

Bash 4.0 之后，可以匹配多个，区别是，结尾使用 `;;&`：

```shell
case $a in
  b) ... ;;&
  ?) ... ;;&
  *) ... ;;
esac
```

---

