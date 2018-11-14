---
layout: post
title: 虚拟视点绘制
data: 2018-11-09
tags: research
---

### 什么是虚拟视点

虚拟视点，Virtual View是多视点视频Multi-Viewpoint Video和自由视点视频Free Viewpoint Video中的核心概念，这两个名词的商用价值就是生成全景视频或图片，当然你也可以做成立体的，效果如下：

[全景图片](https://www.skypixel.com/photos/3f832df4-d747-4b06-a8e4-a9f5c20a4525)

![](/images/posts/virtualview/001.png)

[全景视频](https://v.youku.com/v_show/id_XMTcxOTc1ODIzNg==.html?spm=a2h1n.8261147.reload_1.1~3!2~5~A&s=12d1b22f75ab11e6bdbb)

![](/images/posts/virtualview/002.png)

上述的图片/视频是通过这种设备拍摄的：

![](/images/posts/virtualview/003.png)

![](/images/posts/virtualview/004.png)

可以显而易见的无论是几个摄像头，所拍摄到的信息都是有限的，而全景图片/视频需要连续的视点切换，这就相当于需要通过离散的采样点得到连续的信息，现有的主流方法有视点融合、光流法、基于视差/深度的绘制方法。关于前两者，视点融合最为常用，核心技术是特征点匹配和插值算法，算法复杂程度较高，容易产生鱼眼效应，但是信息采集渠道最常见，因而应用程度最广；光流法，也称为基于光线空间的绘制，由日本名古屋大学，该算法的主要思想是利用四个维度上的变量构造出函数$f(x,y,\theta,\phi)$，用以描述空间中的光线信息。$(x,y)$表示参考平面与光线的交点坐标，$\theta$表示光线与参考平面之间的夹角，$\phi$表示光线与垂直方向的夹角。如下图所示，为光线空间中的横截面切片示意图，与传统的图像存储方式不同，它是以图像横向切片的方式存储场景中图像信息。

![](/images/posts/virtualview/005.png)

光流法需要特别光流采集设备，与普通商用的设备相差较大，因而应用面很窄。最后是本文和我研究生阶段主要研究的方向，基于深度信息的图像绘制方法(Depth Image Based Rendring, DIBR)

### 什么是DIBR

上一段介绍了虚拟视点的概念，DIBR就是如何绘制虚拟视点的技术。这里引入一个新的名词，深度图(Depth Image)。

![](/images/posts/virtualview/006.png)

> *白话：* 深度图是抛弃的图像一切纹理的，只反映场景中目标与相机距离信息的图像

如上图所示，值越大（越接近白色）表示对应像素点与相机距离越近。同样我们也看到了很多的边界上的毛刺与残缺，这就要说到深度图的获得。现有深度图的获得主要由两种渠道：深度相机拍摄如*kinect*(下图)和基于视差的估计。

![](/images/posts/virtualview/007.png)

基于视差的估计，是通过估计视差信息来得到的，即输入左视点、右视点和中间视点的彩色纹理图，通过特征点匹配的方法得到中间视点每一个像素点的视差信息。当然理论上只有两个视点也可以估计，具体可以参考深度估计参考软件(Depth Estimate Reference Software, DERS)。

![](/images/posts/virtualview/008.png)

可以看到*DERS*的设置是很麻烦的，除了红框中的相机参数是由阵列相机拍摄时提供，还有很多预测深度范围的设定，这个就很玄学了，具体来说看经验，可能是因为笔者没有做过采集端的工作吧。

**补充知识：**深度图是8bit编码的，即量化区间[0,255]，越亮，越白，越近。

介绍完深度图，就可以正式进入DIBR了。学术上的实验设备大概长这个样子：

![](/images/posts/virtualview/009.png)

下图是简化的一维虚拟视点示意图，图中虚线表示的View2就是虚拟视点，实现表示的View1和View3是实际拍摄的参考视点。

![](/images/posts/virtualview/010.png)

DIBR的核心思路是通过深度图将参考视点彩色图的每一个像素点映射到三维空间，然后再将每一像素点重映射到对应的虚拟视点上，如下图所示

![](/images/posts/virtualview/011.png)

其流程包含三个部分：前向映射(ForwardWarp)，融合(Merge)和空洞填补(FillHoles)

![](/images/posts/virtualview/012.png)

### 前向映射(ForwardWarp)

这是DIBR中的最关键的一个步骤，后续的融合和空洞填补都是在改善该步骤的效果，本文以*VSRS*参考平台来展开描述。我们先来看一下*VSRS*的配置文件。

![](/images/posts/virtualview/013.png)

红框部分都是相对重要的参数，基本上看字面意思也能理解了，值得注意的是左/右视点最近/最远深度值，这四个参数是由深度估计软件*DERS*提供的，也就是说单有深度图是无法反映像素点的深度信息的，必须配以相机参数和实际深度量化参数才可以。

![](/images/posts/virtualview/014.png)

![](/images/posts/virtualview/015.png)

![](/images/posts/virtualview/016.png)

![](/images/posts/virtualview/017.png)

![](/images/posts/virtualview/018.png)

![](/images/posts/virtualview/019.png)

### 融合(Merge)

其实虚拟视点绘制最早是单视点绘制，也就是一个视点绘制另一个视点，但是这样就会产生很多的不可避免的结构性空洞，如下图绿色区域，而左右视点映射图的空洞区域不同，通过融合后就能很大程度减少空洞区域。

![](/images/posts/virtualview/025.png)

![](/images/posts/virtualview/020.png)

![](/images/posts/virtualview/021.png)

![](/images/posts/virtualview/022.png)

![](/images/posts/virtualview/023.png)

### 空洞填补(FillHoles)

经过上述融合步骤后的剩余空洞已经比较少了，所以*VSRS*只是通过一个简单的基于深度信息的行遍历来实现，即用空洞周围的距离成像面最远的像素点来填补。

![](/images/posts/virtualview/024.png)

但是实际上很多的研究是基于单视点绘制来做的，由于丢失信息量大，需要填补的空洞区域也很大，所以就有图像修复、时域和空域填补等种种方法应用在这里，这也是虚拟视点绘制领域的最重头的研究点。

**补充知识：**

空洞的成因

我分析空洞主要有三大原因产生：遮挡暴露(occlusion exposure)，深度图质量不高(a poor depth-map quality)，映射时的舍入误差(rounding errors during warping)，具体如下图

![](/images/posts/virtualview/026.png)

where the red regions represent the disocclusion regions, the blue regions represent the structural holes, and the green regions represent the cracks. 

遮挡暴露问题：

一句话：当前视点不可见的信息在另一视点将会变的可见

![](/images/posts/virtualview/027.png)

深度图质量不高问题：

上文已经阐述，直接导致前向映射步骤出现问题

舍入误差问题：

![](/images/posts/virtualview/028.png)

### 相关下载
  
转载请注明：[汤锐彬的博客](https://nbsmalltree.github.io) » [虚拟视点绘制](https://nbsmalltree.github.io/2018/04/Online_Commit_Guide/)

