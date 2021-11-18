---
layout:     post
title:      "什么是尾递归？"
subtitle:   "在开始学习 Lisp 的同时，我遇到了术语“尾递归”。到底是什么意思？"
date:       2021-03-17 12:00:00
author:     "heapify"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - 递归
    - JavaScript
    - 尾递归
    - Lisp
    - 堆栈溢出
---

考虑一个简单的函数，该函数将前 N 个自然数相加。（例如`sum(5) = 1 + 2 + 3 + 4 + 5 = 15`）。

这是一个使用递归的简单 JavaScript 实现：
```javascript
function recsum(x) {
    if (x === 1) {
        return x;
    } else {
        return x + recsum(x - 1);
    }
}
```
如果您调用`recsum(5)`，这是 JavaScript 解释器将评估的内容：
```
recsum(5)
5 + recsum(4)
5 + (4 + recsum(3))
5 + (4 + (3 + recsum(2)))
5 + (4 + (3 + (2 + recsum(1))))
5 + (4 + (3 + (2 + 1)))
15
```
请注意，在 JavaScript 解释器开始实际执行计算总和之前，必须完成每个递归调用。

这是该函数的尾递归版本：
```javascript
function tailrecsum(x, running_total = 0) {
    if (x === 0) {
        return running_total;
    } else {
        return tailrecsum(x - 1, running_total + x);
    }
}
```
这是如果您调用会发生的事件序列`tailrecsum(5)`（`tailrecsum(5, 0)`由于默认的第二个参数，实际上是）。
```
tailrecsum(5, 0)
tailrecsum(4, 5)
tailrecsum(3, 9)
tailrecsum(2, 12)
tailrecsum(1, 14)
tailrecsum(0, 15)
15
```
在尾部递归的情况下，每次对递归调用进行评估时，`running_total`都会更新。

在**传统的递归中**，典型的模型是先执行递归调用，然后取递归调用的返回值并计算结果。以这种方式，直到您从每个递归调用中返回后，您才能获得计算结果。

在**tail 递归中**，首先执行计算，然后执行递归调用，将当前步骤的结果传递到下一个递归步骤。这导致最后一个语句的形式为`(return (recursive-function params))`。**基本上，任何给定递归步骤的返回值都与下一个递归调用的返回值相同**。

这样的结果是，一旦您准备好执行下一个递归步骤，就不再需要当前的堆栈框架。这样可以进行一些优化。实际上，使用适当编写的编译器，您永远都不应通过尾部递归调用来实现堆栈溢出*窃笑*。只需将当前堆栈框架重用于下一步递归步骤​​。我很确定 Lisp 会这样做。
