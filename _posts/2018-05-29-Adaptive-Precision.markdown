---
layout:     post
title:      "浮点数公式的累计误差边界计算"
date:       2018-05-29 20:00:00
author:     "ExreLin"
header-img: "img/post-bg-unix-linux.jpg"
catalog: true
tags:
    - 数学 
    - 算法
---

>浮点数公式的累计误差边界计算

<在计算机中很多判断是无法得到一个精确的结果的，这是因为计算底层二进制的形式去描述一个浮点数总是带有误差。在很多应用场景中就需要得到唯一精确的结果，举个简单的例子 
```cpp
double a = 0.2;
double b = 0.3-0.1;
if(a==b)
    std::cout << "a == b" <<std::endl;
else
    std::cout << "a != b" <<std::endl;
```
运行结果应该是 "a != b".<br>

在实际应用中线l1、l2和l3相交于一个点p，但是通过l1和l2求得的点p0不一定等于l2和l3求出的点p1，这样就导致后续算法的出现问题。<br>

当然我们可以用一些无限长的精确计算库，如gmp，但是进入精确计算后算法效率就会变得很低。<br>

论文《[Adaptive Precision Floating-Point Arithmetic and Fast Robust Geometric Predicates ](https://link.springer.com/article/10.1007/PL00009321)》提供了自适应精度的计算的算法。但是论文实在太难了，我只看懂了一部分，所以我只介绍里面最简单的浮点数误差边界的计算。

我们定义<br>
<center> a ⊕ b = a + b + err(a ⊕ b )</center><br>
<center> a ⊙ b = a x b + err(a ⊙ b )</center><br>

首先我们计算一个1的的误差边界，其实很简单，当在计算机中 1 + a == 1 则a就是1的误差边界,代码如下：

``` cpp
double get_epsilone()
{
    double epsilon = 1.0;
    double half = 0.5;
    double check = 1.0;
    double lastCheck;
    do{
        lastCheck = check;
        epsilon *=half;
        check = 1.0 + epsilon;
    } while((check != 1.0) &&(lastCheck != check));

    return epsilon;
}
``` 

那么上计算a+b的误差边界就是 (abs(a) + abs(b)) * epsilon; 怎么利用这个误差边界呢？

```cpp
double a,b;

double c = a + b;
double errBound  = (abs(a) + abs(b)) * get_epsilone();
//如果c满足条件则表示c是在误差范围内的
if((c > errBound||(-c > errBound))
    return c;
else
    gmp(a + b); //伪代码，如果不满足就使用gmp去精确计算。

```


