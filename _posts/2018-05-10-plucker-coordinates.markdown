---
layout:     post
title:      "不寻常的坐标系 ---- Plücker coordinates"
subtitle:   " \"Hello World, Hello Blog\""
date:       2018-05-10 12:00:00
author:     "ExreLin"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - 数学 
    - 几何
---

> “抛开常见的三维坐标系. ”

# 前言

最近在整理代码，偶然看到以前用了[普吕克坐标系](https://en.wikipedia.org/wiki/Plücker_coordinates)来处理元素相交问题。回头看，已经全部看不懂了，所以打算写个博客来记录普吕克坐标系的用法。

普吕克坐标系是[直线几何](https://baike.baidu.com/item/直线几何/3832387?fr=aladdin)中的一种坐标系和普通我们用的三维笛卡尔坐标系不太相同，它是一个齐次坐标系，既然是齐次，我们就用用投影的思想来看待它。

# 正文

首先来理解下点在齐次坐标系下的直观意义。将三维退化为二维更好理解。
![点的齐次理解](/img/in-post/post-plucker-point.jpg)
如上图所示，图中的点是线和平面的交点，换种思想就是说线上的所有点沿着线的方向进行投影，得到的永远是图中所示的点，则这条线就是这个点的齐次方程。