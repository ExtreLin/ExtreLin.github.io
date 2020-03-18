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
<center> a ⊕ b = a + b ± err(a ⊕ b )</center><br>
<center> a ⊙ b = a x b ± err(a ⊙ b )</center><br>

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

![误差边界](/img/in-post/errbound/errbound.jpg)

结合代码和图，就能明白误差边界的定义，所以我们认为我们计算的数值在两侧误差边界中就表示我们计算的结果可靠。<br>

>上面式子是不是太简单了？

我们尝试求解一个简单的几何问题，判断点和直线的位置关系。<br>
判断点和直线的位置关系实际就是求点和直线上的两个点组成的三角形的面积。面积为0表示点在直线上，大于0表示在直线上方，小于0表示在直线的下方。那么判断的式子如下式所示：<br>
![](http://latex.codecogs.com/gif.latex?\\det=\left(a_{x}-c_{x}\right)\left(b_{y}-c_{y}\right)-\left(a_{y}-c_{y}\right)\left(b_{x}-c_{x}\right))<br>

![](/img/in-post/errbound/1.jpg)<br>

t对应计算值，x对应真值。<br>

t<sub>1</sub>、t<sub>2</sub>、t<sub>3</sub>、t<sub>4</sub>误差边界计算很简单，就是加标准误差。我们计算t<sub>5<sub><br>

![](/img/in-post/errbound/2.png)

同样的 t<sub>6</sub> = x<sub>6</sub> ± (3ε + 3ε<sup>2</sup>+ε<sup>3</sup>)|x<sub>6</sub>|<br>

![](/img/in-post/errbound/3.jpg)

![](/img/in-post/errbound/4.jpg)
![](/img/in-post/errbound/5.jpg)

上式的转换我提供了自己两种证明方式：
![](/img/in-post/errbound/6.jpg)

因为还有最后的加和乘法所以再加两个(1+ε),即(1+ε)<sup>2</sup>

![](/img/in-post/errbound/7.jpg)

所以很艰难的求出了误差边界。


>有没有发现没有除法

因为计算机对除法的特殊意义，所以式子中一定不能带除法！