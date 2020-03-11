---
layout:     post
title:      "OpenGL和CUDA的交互"
date:       2019-01-21 13:00:00
author:     "ExreLin"
header-img: "img/post-bg-opengl.jpg"
catalog: true
tags:
    - 应用
    - OpenGL
---

>“OpenGL和cuda的交互. ”


最近在写OpenGL模拟扫描的算法需要用到OpenGL去渲染很多张图片，然后需要分析这些图片。最初的方案是用OpenGL的glReadPixels去读取渲染的buffer然后再保存在内存中再分析。这个方案主要是glReadPixels这条函数特别的慢，需要cpu等待。后来查了资料又将图片read到PBO(pixels buffer objects)中，因为是GPU到GPU，所以可以减少cpu等待周期，但是最后计算还是要通过map的形式映射到内存，所以觉得很慢。所以在想直接在GPU中完成所有计算只输出一个结果，那么就可以大大提高效率。<br>
直接在GPU有很多方式，比如OpenGL的computer shader 亦或CUDA、OpenCL之类的并行库。由于当时想学习CUDA所以就选择了CUDA去做这件事。<br>


cuda中可以注册两种形式的buffer, 一种是image类型的buffer，如renderbuffer或者texture,另一种就是vbo，vertexBufferObject。

\\\cpp
GLuint rbo,vbo;


\\\