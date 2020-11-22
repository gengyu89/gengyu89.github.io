---
layout:     post
title:      "你见过最美的程序是什么？"
subtitle:   "你见过最美的程序是什么？ - 知乎"
date:       2020-11-22 12:00:00
author:     "heapify"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - 反平方倒数
    - 卡马克
    - 浮点运算
    - 十六进制
    - 计算机图形学
---

**匿名用户** <br>
<span style="color:gray"> 148 人赞同了该回答 </span>

[平方根倒数速算法](https://zh.wikipedia.org/wiki/%E5%B9%B3%E6%96%B9%E6%A0%B9%E5%80%92%E6%95%B0%E9%80%9F%E7%AE%97%E6%B3%95)
```cpp
float Q_rsqrt( float number )
{
    long i;
    float x2, y;
    const float threehalfs = 1.5F;
 
    x2 = number * 0.5F;
    y  = number;
    i  = * ( long * ) &y;// evil floating point bit level hacking（对浮点数的邪恶位级hack）
    i  = 0x5f3759df - ( i >> 1 );// what the fuck?（这他妈的是怎么回事？）
    y  = * ( float * ) &i;
    y  = y * ( threehalfs - ( x2 * y * y ) );   // 1st iteration （第一次牛顿迭代）
//      y  = y * ( threehalfs - ( x2 * y * y ) );   // 2nd iteration, this can be removed（第二次迭代，可以删除）
    return y;
}
```

以及下面是 xkcd 的提到它的漫画以及[科学松鼠会对它的解释](http://songshuhui.net/archives/52767)

![comic](/img/in-post/post-best-algorithm/xkcd_r.png)

`0x5f375a86` 来自一个传奇算法，出自 John Carmack 开发的《雷神之锤3》的 3D 引擎。这个引擎的源代码里包括一个反平方倒数的算法，其速度要比标准的牛顿迭代法快上几十倍，而其中的关键是一行神秘的代码和一个莫名其妙的数字：
```cpp
    i  = 0x5f3759df - ( i >> 1 );// what the fuck?（这他妈的是怎么回事？）
```

没有人知道 Carmack 是怎么发现这个数字的。普度大学的数学家 Lomont 觉得很好玩，于是自己从理论推导出了一个可能的最佳值 `0x5f375a86`，和 Carmack 的接近但略微好一丁点，他很怀疑 Carmack 其实是用试错法找到原来的数的……（答者注，其实这家伙先用理论推导出来一个 `0x5f37642f`，结果试了一下发现还不如原本的 `0x5f3759df` 精确，怒，暴力穷举出来的 `0x5f375a86`……）于是 Lomont 写了一篇文章发表了这个算法，称之为 "Fast inverse square root"。天知道还有多少这样的神奇算法藏在各种商业软件里，不为人知。

作者：匿名用户 <br>
链接：https://www.zhihu.com/question/25568830/answer/32542217 <br>
来源：知乎 <br>
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

<span style="color:gray"> 编辑于 2014-10-27 </span>
