---
title: LSB 与 MSB
categories: [阅读笔记]
tags: [LSB, MSB]
p: read-note/lsb-and-msb
date: 2020-04-13 16:51:41
---

## LSB

---

 LSB 即 **least significant bit**， 代表一串二进制数的最低位 (low-order bit)，例如：

1 0 0 1 0 1 0 **1**

加粗的二进制数即为 LSB。

<!-- more -->

## LSB 与数字密文

---

在一串用于表示颜色的二进制数中，低位改变不会带来肉眼可见的区别，因此可将信息存放在 LSB 及其相邻的二进制中进行传输，达到信息隐藏的效果。

## MSB

---

MSB 即 **most significant bit**，代表一串二进制数的最高位（high-order bit)，例如：

**1** 0 0 1 0 1 0 1

加粗的二进制数即为 MSB。

## MSB 与有符号数

---

对于有符号数，MSB 可作为该数的符号位，MSB 为 `1` 时表示该数为正数，为 `0` 时表示该数为负数。

## 参考资料

---

- [维基百科：Bit bumbering](https://en.wikipedia.org/wiki/Bit_numbering)