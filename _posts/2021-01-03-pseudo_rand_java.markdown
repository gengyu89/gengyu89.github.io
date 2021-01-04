---
layout:     post
title:      "为什么说Java中的随机数都是伪随机数？"
subtitle:   "为什么说Java中的随机数都是伪随机数？ - GreatAnt - 博客园"
date:       2021-01-03 12:00:00
author:     "heapify"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - 线性同余公式
    - 周期性序列
    - 种子
    - 随机性
    - 随机事件
---

#### 什么是伪随机数？
1. 伪随机数是看似随机实质是固定的周期性序列,也就是有规则的随机。
2. 只要这个随机数是由确定算法生成的,那就是伪随机,只能通过不断算法优化,使你的随机数更接近随机。(随机这个属性和算法本身就是矛盾的)
3. 通过真实随机事件取得的随机数才是真随机数。

#### Java随机数产生原理：
Java的随机数产生是通过线性同余公式产生的,也就是说通过一个复杂的算法生成的。

#### 伪随机数的不安全性：
Java自带的随机数函数是很容易被黑客破解的,因为黑客可以通过获取一定长度的随机数序列来推出你的seed,然后就可以预测下一个随机数。

#### 不用种子的不随机性会增大的原因：
`java.Math.Random()`实际是在内部调用`java.util.Random()`的,使用一个和当前系统时间有关的数字作为种子数。两个随机数就很可能相同。
```java
double a = Math.random()；
double b = Math.random();
Random r1 = new Random();
r1.nextInt(10);
Random r2 = new Random();
r2.nextInt(10);
```

#### Java中产生随机数的方法有两种：
* 第一种：`Math.random()`
* 第二种：`new Random()`

#### 一、`java.lang.Math.Random`：
调用这个`Math.Random()`函数能够返回带正号的`double`值,取值范围是[0.0,1.0),在该范围内（近似）均匀分布。因为返回值是`double`类型的,小数点后面可以保留15位小数，所以产生相同的可能性非常小,在这一定程度上是随机数。

#### 二、`java.util.Random`：
```java
Random r1 = new Random();
Random r2 = new Random();

Random r3 = new Random(10);
Random r4 = new Random(10);
```

<span style="color:red"> 下面是`Random()`的两种构造方法： </span>

* `Random()`:使用一个和当前系统时间对应的相对时间有关的数字作为种子数。
* `Random(long seed)`:直接传入一个种子数。

<span style="color:red"> 种子的作用是什么？ </span>

种子就是产生随机数的第一次使用值,机制是通过一个函数,将这个种子的值转化为随机数空间中的某一个点上,并且产生的随机数均匀的散布在空间中。以后产生的随机数都与前一个随机数有关。

<span style="color:red"> 举例： </span>
```java
Random r =new Random(100);
System.out.println(r.nextInt(20));
```
种子数只是随机算法的起源数字,和生成的随机数字的区间没有任何关系。

初始化时100并没有起直接作用（注意：不是没有起作用）,`r.nextInt(20)`中的20是随机数的上限,产生的随机数为0-20的整数,不包括20。

举例：
```java
Random r1 = new Random();
Random r2 = new Random();
//无参构造使用的是参数作为种子数
Random r3 = new Random(100);
Random r4 = new Random(100);
//产生随机数调用nextXXX()方法
System.out.println(r1.nextInt(10));
System.out.println(r1.nextInt(10));
System.out.println(r2.nextInt(10));
System.out.println(r2.nextInt(10));
System.out.println("-----------------");
System.out.println(r3.nextInt(10));        
System.out.println(r3.nextInt(10));
System.out.println(r4.nextInt(10));
System.out.println(r4.nextInt(10));
```
结果：
```
5
1
4
0
-----------------
5
0
5
0
```

<span style="color:red"> 总结： </span>

1. 同一个种子,生成N个随机数,当你设定种子的时候,这N个随机数是什么已经确定。相同次数生成的随机数字是完全相同的。
2. 如果用相同的种子创建两个 `Random` 实例,如上面的`r3`,`r4`,则对每个实例进行相同的方法调用序列,它们将生成并返回相同的数字序列。
3. Java的随机数都是通过算法实现的,`Math.random()`本质上属于`Random()`类。
4. 使用`java.util.Random()`会相对来说比较灵活一些。
