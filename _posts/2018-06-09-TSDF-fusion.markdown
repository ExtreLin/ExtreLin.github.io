---
layout:     post
title:      "TSDF融合算法"
date:       2018-06-09 17:00:00
author:     "ExreLin"
header-img: "img/post-bg-tsdf.jpg"
catalog: true
tags:
    - 三维重建
    - 数学 
    - 算法
---

>简单介绍

TSDF其实应该叫T-SDF，SDF是有向距离场，T是截断的意思，TSDF相比原来的SDF减少了很多计算量。TSDF融合就是将所有点云数据加入一个体素中，计算相关体素中的SDF值，然后构建等值面，最终得到网格模型。这种方法表达等值面的方法可以归类为隐式表达。

---

TSDF重建网上有很多博客介绍，比如这篇博客[3D Reconstruction——TSDF volume reconstruction](https://www.dazhuanlan.com/2019/12/26/5e04175d1e692/)。细节部分不详细展开，然后讲讲和博客中不一样的部分吧。

---

当我们进行无需点云的重建的时候，将每个点的影响范围变为以该点所在Voxel为中心的3X3X3或者5X5X5的范围的Voxel，这样能通过少量点云融合出等值面。计算SDF值的公式为<br>

![](http://latex.codecogs.com/gif.latex?\\sdf=(p-v)\cdot(n))<br>

上式子中p是输入的点坐标，v是voxel的中心点，n为p点的法向（需要单位化）。<br>

然后更新每个体块的中的SDF值可以为 sum_sdf += sdf*w,然后记录每个体块中的权重 sum_w += w。当融合完所有的点后，每个voxel的ave_sdf = sum_sdf/sum_w。<br>

后续用marching cube的方式抽取点云。即在每个有ave_sdf值的voxel中判断其x+1、y+1和z+1方向上的ave_sdf值，两者相乘小于0表示在这条边上有等值面穿过，就可以通过sdf值线性插值出相应的点。<br>

最后将这些点连接成三角网格用的是标准的marching cube的方式。

标准的TSDF融合算法就是这样非常简单的算法，只要有一颗能并行的octree，其余关键代码加起来估计没有多少行。

>缺陷

那么有没发现个问题，在x、y、z方向上插值点都是用同样的sdf值，这样做能使得重建出三角网格的表面很光顺(就是一个SDF值方向上的球)，但是sdf表示的有向距离场，那么其实表示的是当前点在某个方向上离等值面的一个距离，如果取平均了那么我们想要的特征区域是不是消失了? 这里我们都没有考虑marching cube方式连接网格带来的特征退化（这个方向也有很多研究比较著名的就是居涛老师的DC算法《[Dual contouring of hermite data](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.418.9220&rep=rep1&type=pdf)》），单纯sdf值上就是一个特征退化。<br>

还有用中心点云的法向去计算SDF值的方法其实还引入一个如何逼近等值面的问题，一般形式下逼近等值面有两种方式。<br>

![](/img/in-post/fusion/sdf.jpg)<br>

上图中红色是刚才介绍的SDF值求取的公式带来的影响，是一种外凸的逼近方式，还有一种是蓝色的求取的公式是内凹的，关于正确的SDF值求取还有很多可以研究，比如《[Feature Sensitive Surface Extraction from Volume Data](http://mesh.brown.edu/dgp/pdfs/kobbelt-sg2001.pdf)》就用一种粗暴的方式分别获得x、y、z分量上的不同sdf值，我将专门开一篇博客写关于SDF值计算的问题。当然在大多数情况下蓝色情况优于红色，红色的情况会使特征区域鼓起，像被圆润了一样，蓝色情况特征更加明显。

>我本来不想写TSDF

水一篇TSDF融合，这个主要是人写的太多了，没必要重复，由于后续的RBF和HRBF重建都用到了，我就打个基础。
