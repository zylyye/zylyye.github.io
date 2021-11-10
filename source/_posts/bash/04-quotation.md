---
title: 引号和转义
categories: [Bash]
tags: [linux, bash]
p: bash/quotation
date: 2021-01-29 18:04:25
---

## 转义

---

Bash 接收的数据都视为字符串类型，遇到特殊含义的字符需要使用反斜杠转义：\

```shell
echo \$abc
echo \\
```

<!-- more -->

## 单引号与双引号

---

字符串可以放到单引号或双引号中，单引号中的**特殊字符不需要转义**，双引号中的部分特殊字符**需要转义**(``` \ ` $```)。

- **双引号中可以收入多行文本**：

```shell
echo "one
 two three
 four"
```

- **双引号中支持模式扩展**

```shell
echo "$(cal)"
```



## Here 文档

---

Here 是一种输入多行字符串的方法，格式如下

```shell
<< end_tag_name
<一些文本>
end_tag_name
```

例如：

```shell
cat << _EOF_
This is a
test text for
here document.
_EOF_
```

- Here 文档中支持模式扩展，单/双引号原样输出
- 如果将开始标记放到单引号中则会禁用模式扩展，类似单引号字符串
- Here 文档的语法**本质是重定向**，类似 `echo test >> demo.txt`，因此只适合接收标准输入的命令



## Here 字符串

---

与 Here 文档类似，Here 字符串也用于重定向字符串到标准输入，通常用于简单的字符串文本：

`cat <<< 'good job!'`



