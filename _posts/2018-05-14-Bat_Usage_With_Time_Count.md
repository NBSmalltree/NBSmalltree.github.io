---
layout: post
title: 批处理文件的调用及计时实现
date: 2018-05-14 
tag: wits
---

### 初心

一直在用批处理文件，可以更有效率地调用程序，实现不同配置参数下的功能，也能使程序功能通过注释更具可读性。

可以理解为是一个在程序层面解释和调用的工具(定好基调)。

最近第二个工作已经接近尾声，我的程序都是分模块写的，有好多的exe和cfg，好在我把它们都放在了独立文件夹下(一个讲究的码农)，按不同功能不同调用。

但是现在到了联调的时候了，虽然它们之间的参数文件的输入输出配置是继承的，但是exe和调用exe的bat并不继承，每次都要点不同的bat很麻烦，因此如果有一个总调用的bat就显得十分方便了。这就是本文探索的初心。

### 基本操作

以下是我常使用的基本的.bat操作命令

  - 调用程序及其配置文件
`BackgroundEstimation.exe BackgroundBuild_PoznanStreet_View5.cfg`
  - 调用程序及命令行
```
TAppRenderer.exe -c rendering_UndoDancer.cfg ^
--s_0=./output/Virutal_UndoDancer_View_5.yuv ^
-f 5
```
  - 提示
`echo PoznanStreet`
  - 暂停，按任意键继续
`pause`
  - 不显示指令，在指令前添加即可在cmd中直接显示运行结果(适合最终版bat，如果不加就可以看到哪一句出错了<适用于调试>)
`@`
	
### bat调用bat

这边参考了文献[1](https://blog.csdn.net/lewky_liu/article/details/78536439)，它分了三类：

  - 两个bat文件在同一个目录下
  - 两个bat文件不在同一个目录下
  - 开启一个新的cmd窗口来运行另一个bat文件
  
这边我尝试了前两个，第三个我觉得于我没什么必要，总结如下：

只要掌握了同一目录下的bat调用，在不同目录下只需要用cd切换到对应文件夹下就好了

这边有两种实现：

  - 直接调用
    `run.bat`，这样执行完run.bat后直接退出，不会再执行主bat的后续命令
  - call调用
    `call run.bat`，这样执行完run.bat后会继续执行主bat的后续命令

一般情况都是要继续执行，所以就选择*call调用*

### bat的时间统计

这边非常想要吐槽一下，百度引流的网站上关于这么常用的一个功能竟然都是只有几个版本的信息，而且都没有说明自己的测试环境，让很多新手菜鸟一头雾水，没有太大的参考价值，最后在google引流的网站上看到一篇不错的文献[2](http://blog.51cto.com/conducer/1377650)，一步步看下来实现，修改一下就能完成本实验的要求，统计2位小数精度的时间。

具体不再啰嗦，直接上代码

```
@set a=%time%
@set /a h1=%a:~0,2%
@set /a m1=1%a:~3,2%-100
@set /a s11=1%a:~6,2%-100
@set /a s12=1%a:~9,2%-100

@echo Time a : %h1% hour %m1% minute %s11% second %s12% msecond
@pause

@set b=%time%
@set /a h2=%b:~0,2%
@set /a m2=1%b:~3,2%-100
@set /a s21=1%b:~6,2%-100
@set /a s22=1%b:~9,2%-100

@echo Time b : %h2% hour %m2% minute %s21% second %s22% msecond

@if %h2% LSS %h1% set /a h2=%h2%+24
@set /a ts1=%h1%*360000+%m1%*6000+%s11%*100+%s12%
@set /a ts2=%h2%*360000+%m2%*6000+%s21%*100+%s22%
@set /a diff=%ts2%-%ts1%
@set /a s=%diff%/100
@set /a ss=%diff%%%100

@echo Final Time : %s%.%ss% second
@pause
```

实现的功能是记录第一次时间，然后暂停等待按键，按键后记录第二次时间，统计两次时间的间隔，精度两位小数，可以跨天，非常方便统计算法运算时间

我的具体主bat文件调用如下，给各位参考

```
@echo PoznanStreet

@set a=%time%

@echo Sequence Warp
@cd ..\ForwardWarp\PoznanStreet
@call run.bat

@set b=%time%

@echo Background Build
@cd ..\..\BackgroundBuild\PoznanStreet
@call run.bat

@echo Background Warp
@cd ..\..\ForwardWarp\PoznanStreetBG
@call run.bat

@echo Background Merge
@cd ..\..\BGMerge\PoznanStreet
@call run.bat

@echo Filling with Background
@cd ..\..\HoleFillingWithBG\PoznanStreet
@call run.bat

@set c=%time%

@set /a h1=%a:~0,2%
@set /a m1=1%a:~3,2%-100
@set /a s11=1%a:~6,2%-100
@set /a s12=1%a:~9,2%-100

@set /a h2=%b:~0,2%
@set /a m2=1%b:~3,2%-100
@set /a s21=1%b:~6,2%-100
@set /a s22=1%b:~9,2%-100

@set /a h3=%c:~0,2%
@set /a m3=1%c:~3,2%-100
@set /a s31=1%c:~6,2%-100
@set /a s32=1%c:~9,2%-100

@if %h2% LSS %h1% set /a h2=%h2%+24
@set /a ts1=%h1%*360000+%m1%*6000+%s11%*100+%s12%
@set /a ts2=%h2%*360000+%m2%*6000+%s21%*100+%s22%
@set /a diff=%ts2%-%ts1%
@set /a s=%diff%/100
@set /a ss=%diff%%%100
@echo Sequence Forward Warp Time : %s%.%ss% second

@if %h3% LSS %h2% set /a h3=%h3%+24
@set /a ts1=%h2%*360000+%m2%*6000+%s21%*100+%s22%
@set /a ts2=%h3%*360000+%m3%*6000+%s31%*100+%s32%
@set /a diff=%ts2%-%ts1%
@set /a s=%diff%/100
@set /a ss=%diff%%%100
@echo Background Hole Filling Time : %s%.%ss% second

@pause
```

### 尾记

前后用了大约3H，愿后人少走些弯路。

### 参考文献
  - [dos笔记-如何在bat文件中调用另一个bat文件](https://blog.csdn.net/lewky_liu/article/details/78536439)
  - [批处理计算时间差的方法总结](http://blog.51cto.com/conducer/1377650)
  
转载请注明：[汤锐彬的博客](https://nbsmalltree.github.io) » [批处理文件的调用及计时实现](https://nbsmalltree.github.io/2018/05/Bat_Usage_With_Time_Count/)