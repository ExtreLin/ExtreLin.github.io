---
layout:     post
title:      "不寻常的坐标系 ---- Plücker coordinates"
date:       2018-05-10 12:00:00
author:     "ExreLin"
header-img: "img/post-plucker-bg.jpg"
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

首先来理解下点在齐次坐标系下的直观意义。将三维退化为二维更好理解。<br>
![点的齐次理解](/img/in-post/plucker/post-plucker-point.jpg)<br>
如上图所示，图中的点是线和平面的交点，换种思想就是说线上的所有点沿着线的方向进行投影，得到的永远是图中所示的点，则这条线就是这个点的齐次方程，齐次的w是投影长度。

同样我们表示一个三维点在普吕克坐标系下为表示为<center>(x<sup>0</sup>:x<sup>1</sup>:x<sup>2</sup>:x<sup>3</sup>)</center>
x<sup>0</sup>为齐次坐标，x<sup>1</sup>，x<sup>2</sup>，x<sup>3</sup>分别代表三维中的x，y，z。

普吕克坐标系最常见的两个操作是 join 和 meet。join表示两个元素组成新的元素，meet表示两个元素相交出的元素。比如point join point 得到 line ，point join line 得到 plane ，line meet line 得到 point ，line  meet plane 得到point。为了简单的工程应用，后续就围绕着这几个操作进行分析。推导的详细过程可以查看wiki的[普吕克坐标系](https://en.wikipedia.org/wiki/Plücker_coordinates)

>point join point

我们定义<b>线</b>为：<br>
<center>(p<sup>01</sup>:p<sup>02</sup>:p<sup>03</sup>:p<sup>23</sup>:p<sup>31</sup>:p<sup>13</sup>)</center>

p<sup>01</sup>,p<sup>02</sup>...之类的怎么来的呢？假设这条线段上面有两个点(x<sup>0</sup>:x<sup>1</sup>:x<sup>2</sup>:x<sup>3</sup>)和(y<sup>0</sup>:y<sup>1</sup>:y<sup>2</sup>:y<sup>3</sup>)，我们将以下面的规则定义p<sup>ij</sup>:<br>
<div align=center><img src="http://latex.codecogs.com/gif.latex?\\p_{ij}=\begin{vmatrix}x_{i}&y_{i}\\\x_{j}&y_{j}\end{vmatrix}=x_{i}y_{j}-x_{j}y_{i}"\></div><br>
为什么只取这6个数呢？因为p<sup>ii</sup> = 0 并且 p<sup>ij</sup> = -p<sup>ji</sup>，所以取6个数就能代表整个矩阵。
以两个点得出线的操作称为<b>point join point</b>。<br>
-----这么理解是不是很复杂，那么我介绍一种简单的理解，上面的线的表达式可以退化为(<b>d</b>:<b>m</b>)，假设直线上有两个点p<sub>1</sub>和p<sub>2</sub>则<b>d</b> = p<sub>2</sub> - p<sub>1</sub>, <b>m</b> =  p<sub>1</sub> x p<sub>2</sub>。将 p<sub>1</sub>和p<sub>2</sub> 看作从(0,0,0)出发的两条线段。这样理解<b>d</b>可以筛选出系列的线段，可以看作绕着原点，方向为朝向屏幕的直线集合，如下图所示。<b>m</b>的作用就是唯一能确定一条线段垂线方向与其相同，且离原点距离为|<b>m</b>|。这样空间中的直线就能被唯一确定下来。<br>

<div align=center><img src="/img/in-post/plucker/line.jpg"></div>

>point join line 

在空间中的一个点和一条线我们可以唯一确定一个平面。<b>point join line</b>就是用来确定空间中一个平面的操作。<br>
我们定义<b>平面</b>为：<br>
<center>(a<sup>0</sup>,a<sup>1</sup>:a<sup>2</sup>:a<sup>3</sup>)</center>

假设空间中的点坐标为(x<sup>0</sup>:x<sup>1</sup>:x<sup>2</sup>:x<sup>3</sup>)，空间中的线段的定义为如<b>point join point</b>中p<sup>ij</sup>中的定义。
这里为了简便直接给出a<sup>i</sup>的定义：<br>
<div align=center><img src="http://latex.codecogs.com/gif.latex?\\\sum\limits_{i=0}^{3}p^{ij}x_{i},\hspace{1cm}j=0,1,2,3"\></div><br>

其实有平面方程的就很好理解，平面方程的系数就是上述的<b>a</b>。

>line meet line

有了上面的点、线、面的定义以后我们可以通过利用普吕克坐标系进行相应的相交操作。我们首先来讲线线相交。<br>
在普吕克坐标系中如果两条直线有相交，则其交点为(<b>d · m' : m x m' </b>)，通过齐次位是否为0可以判断两条线段是否相交，这个很好理解<b>d'</b> 和<b>m'</b>是正交的，如果<b>d · m' </b> 为0，表示<b>d</b> 和<b>m'</b>也是是正交，则 <b>d</b> 和<b>d'</b>是平行的所以没有交点。<br>
<b>m x m' </b>也很好理解，两条直线的交点实则是求一个从原点出发的向量，<b>m x m' </b>表示这个向量的方向，如下图中0和C组成的向量方向。<br>

<div align=center><img src="/img/in-post/plucker/line-line-meet.jpg"></div><br>

>line meet plane




