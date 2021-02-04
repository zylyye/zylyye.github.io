---
title: 字符串操作
categories: [Bash]
tags: [linux, bash]
p: bash/06-string
date: 2021-02-04 17:12:44
---

## 字符串长度

---

获取字符串长度：

```shell
echo ${#varname}
```

<!-- more -->

## 子字符串

---

截取子字符串：

```shell
${varname:offset:length}
```

- offset: 其实索引，从 0 开始，如果为负值则表示从结尾开始，**注意负值必须在负号前增加空格，否则和`${name:-value}` 冲突**，负值正确写法：`${name: -6}`
- length: 截取的长度，不指定则截取到末尾，length 可以为负，如果为负，则表示排除末尾的字符，如 -2 表示排除结尾 2 个字符

例如:

```shell
name=zhuyilong
echo ${name:3:6} // yilong
echo ${name:3}	// yilong
```

**该语法不能直接操作字符串**，错误示例：`${"zhuyilong":3:6}`



## 搜索和替换

---

Bash 提供的字符串搜索和替换方法：

### 字符串头部模式匹配

从头部开始匹配，返回匹配之外的部分，分为**贪婪匹配**和**非贪婪匹配**两种：

```shell
# 非贪婪匹配，匹配最短部分，返回剩余部分
${var#pattern}
# 贪婪匹配，匹配最长部分，返回剩余部分
${var##pattern}
# 匹配后替换
${var/#pattern/string}
```

其中的 pattern 可取 `*`, `?`, `[]` 等通配符。

### 字符串尾部模式匹配

从尾部开始匹配，返回剩余的部分：

```shell
# 非贪婪匹配，返回剩余部分
${var%pattern}
# 贪婪匹配，返回剩余部分
${var%%pattern}
# 替换尾部的内容
${var/%pattern/string}
```

### 任意位置匹配

```shell
# 贪婪匹配替换，仅替换第一个匹配
${var/pattern/string}
# 贪婪匹配替换，替换所有的匹配
${var//pattern/string}
```

### 改变大小写

语法：

```shell
# 转为大写
${var^^}
#转为小写
${var,,}
```

---

