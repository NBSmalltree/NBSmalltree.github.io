---
layout: post
title: 批量重命名
date: 2018-07-02 
tag: wits
---

### 初衷

在实验过程中有对比文献的参考程序有固定的输入文件名格式，而且需要更改的文件名称量比较大，所以构思一个批量更改名字的方法。[本文参考](https://jingyan.baidu.com/article/656db918a7e848e381249c0e.html)

### 实操步骤

#### 1、cmd中cd到需要重命名的目标文件夹

![](/images/posts/batchrename/001.png)
![](/images/posts/batchrename/002.png)

#### 2、输入`dir /b>rename.xls`，生成rename.xls的xls文件

![](/images/posts/batchrename/003.png)
![](/images/posts/batchrename/004.png)

#### 3、打开rename.xls，发现A列为当前文件夹下的文件名，删去最后一个无关文件名

![](/images/posts/batchrename/005.png)

#### 4、在B1单元格输入目标文件名，并下拉自动填充所有文件名

![](/images/posts/batchrename/006.png)

#### 5、C1单元格输入公式`="ren "&A1&" "&B1`，并下拉填充

![](/images/posts/batchrename/007.png)

#### 6、复制C列，粘贴到当前目录下的新建ren.bat中

![](/images/posts/batchrename/008.png)
![](/images/posts/batchrename/009.png)

#### 7、运行ren.bat，重命名完成

![](/images/posts/batchrename/010.png)

### 总结

主要是通过Excel的下拉自动填充来完成批量重命名命令，调用批处理来执行命令
