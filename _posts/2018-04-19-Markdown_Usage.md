---
layout: post
title: Markdown用法
data: 2018-04-19
tags: wits
---


### 初识 Markdown

　　Markdown是一种轻量化的文本编写语言，说人话就是用一种特殊的`txt`格式写文档。

　　生活中有很多时候需要记录零散的想法或临时的计划，但打开`word`等待的那一刹那就会让很多想法烟消云散了，而且几句简短的话也要占用10多K的空间让强迫症有点难以接受。但是`txt`除了换行和空格以外没有其他特殊的标记来 **突出重点** 或是  ~~做一些明显的标记~~。这个时候就到了 Markdown 大显身手的地方了。
　　
### 基本语法

标题

H1 : 

# 标题一

H2 : 

## 标题二

H3 : 

### 标题三

H4 : 

#### 标题四

H5 : 

##### 标题五

H6 : 

###### 标题六

链接 : [我的主页](https://nbsmalltree.github.io)

加粗 : **Bold**

斜体 : *Italics*

删除 : ~~text~~

段内内嵌代码 : `std::cout << "Welcome to My Blog!" << std::endl;`

段落 : 两个回车

列表(代办事项) :

  - [ ] 个人博客网站完善
    - 总结新的*Markdown*语法
	- 在线评论添加方法总结
	- 增加自己的简历部分
  - [x] 完成测试序列的下载
    - 运行测试序列（看参数文件是否匹配）
	- 跑一下对比文献程序
	- 选定自己的测试序列

表格 : 

|Lable| 文件名 | 标签 |
|:-- |:-----:| ---:|
|  0 | 0.png |  4  |
|  1 | 1.png |  9  |
|  2 | 2.png |  1  |
|  3 | 3.png |  7  |
|  4 | 4.png |  3  |

插入图片 :
 
![](/images/posts/markdown/image1.png)

作业部落在线 Markdown 编辑器推出桌面版客户端啦，全平台支援。

插入代码 : 

*python*
```python
img_name = rng.choice(train.filename)
filepath = os.path.join(data_dir, 'Train', 'Images', 'train', img_name)

img = imread(filepath, flatten=True)

pylab.imshow(img, cmap='gray')
pylab.axis('off')
pylab.show()
```

*C++*
```c++
void CPixelHist::calcHist(unsigned char * pBuffer)
{
	int i;

	//>Initialize
	for (i = 0; i < 255; i++)
		m_iBarValue[i] = 0;

	for (i = 0; i < m_iTotalFrame; i++)
		m_iBarValue[(int)pBuffer[i]]++;

	//>Test to output the BarValue
	//showFrequency();
}
```

### 其他应用场景

　　Markdown 应用其实很多，因为其简单高效的特性被用在很多的地方，Github 上的`README.md`，甚至是我做个人主页的 *post* 都可以用 Markdown 来写，再也没有 *office word* 因为版本不兼容而带来的无法打开的问题，精力更多地投入到作品内容本身的意义上。
　　
### 个人心得

　　简单的文档可以直接用 `Notepad++` ，复杂的需要可视化程度高的建议使用 [Cmd Markdown](https://www.zybuluo.com/cmd/?utm_source=mindstore.io)
