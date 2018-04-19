---
layout: post
title: 网站在线评论实现
data: 2018-04-19 16:00:00 +0000
tags: wits
---

### 前言

之前初步实现了网站架构，老铁们建议要有一个评论系统来分享(自)交流(嗨)，那么就果断实现一下，经过接近2天的工作就有了本文。重申本文实现基础是`Github Page` + `Jekyll` + *Disqus* or *Gitment* 。 

### 评论模块简介

在现有的架构基础上添加评论模块是比较可行的方法，调研了几个主流的评论模块：

  - **多说** \- 最多用户使用的评论，但遗憾2017年6月将暂定服务；不建议新用户使用，但为旧用户保留，也感谢多说一路的陪伴
  - **网易云跟帖** \- 网易提供的评论组件，功能比较简单，性能优秀；管理后台在查询上还不算特别智能，但足够普通用户使用
  - **畅言** \- 搜狐提供的评论组件，功能丰富，体验优异；但必须进行域名备案。只要域名备过案就可以通过审核，简单问题复杂化
  - **Disqus** \- 国外使用较多的评论组件。万里长城永不倒，一枝红杏出墙来，你懂的
  
上述总结转载自参考文献[1]，多说和网易云跟帖没有了，畅言要备案，对于只是挂靠在GitHub的博客非常的不友好，放弃！Disqus，我们都希望自己的博客可以不分国界！所以Disqus也只能放弃！
  
另外，本网站的模板网站利用了Disqus，所以在设计实现方案时，就考虑先从Disqus入手。

### Disqus

其实只要根据官网的步骤来，非常方便，不得不说他们的导入界面做得还是非常棒的。

#### 1. 官网注册

注册的原因是Disqus是把评论的内容存放在了它自己的官网上，在注册完成后点击 `GET STARTED` 按钮进入如图界面

![](/images/posts/onlinecommit/001.png)

#### 2. 申请评论空间

选择 *I want to install Disqus on my site*，进入下图，对最后三项进行填入及选择

![](/images/posts/onlinecommit/002.png)

然后选择免费，手动微笑.png，进入下图，选择 *Jekyll*

![](/images/posts/onlinecommit/003.png)

#### 3. 本地配置

![](/images/posts/onlinecommit/004.png)

如上图，进入配置环节，按照教程在本地的模板文件中修改，考虑到评论只需要在日志部分的末尾出现，因此针对性修改了 *_layouts* 文件夹中的 `post.html`(参考不同模板命名可能不同)，在头文件 `layout: default` 后增加一行 `comments: true`

在文件末尾增加:

```html

<div id="disqus_thread"></div>
<script>

/**
*  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
*  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables*/
/*
var disqus_config = function () {
this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
};
*/
(function() { // DON'T EDIT BELOW THIS LINE
var d = document, s = d.createElement('script');
s.src = 'https://tomrubin.disqus.com/embed.js';
s.setAttribute('data-timestamp', +new Date());
(d.head || d.body).appendChild(s);
})();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
                          
```

以上是我的配置，你们的代码肯定与我不同，只需按照官网教程即可

#### 4. 上线结果

测试成功，中文界面友好，但不支持Markdown。

![](/images/posts/onlinecommit/005.png)

#### 5. 分析

本文的测试环境还包括了对现有网络作了和谐化处理，如果正在阅读本文的你按照上述步骤一步步实现发现自己的博客没有显示评论功能，请尝试绕过GFW，说人话就是 Disqus 不支持国内访问，但让老铁们在自嗨前都去FQ一下显然不是那么乐意的，所以本文下半部分考虑国内能正常访问评论的解决方案。

### Gitment

> Gitment is a comment system based on GitHub Issues, which can be used in the frontend without any server-side implementation.
>
>Gitment 基于 GitHub Issues 的评论系统。支持在前端直接引入，不需要任何后端代码。可以在页面进行登录、查看、评论、点赞等操作，同时有完整的 Markdown / GFM 和代码高亮支持。尤为适合各种基于 GitHub Pages 的静态博客或项目页面。

简而言之，就是利用了 GitHub Issues 功能来做了一个静态网页的评论系统。

开源代码库及官网 [Gitment](https://github.com/imsun/gitment)  [Demo Page](https://imsun.github.io/gitment/)   [中文简介](https://imsun.net/posts/gitment-introduction/)

安装部署步骤详见中文简介，接下来说具体说一下我所碰到的问题，希望可以给以参考，减少新手往GitHub *push*的次数。

### Gitment 错误及解决方案

具体的可以参考文献[1]中的`Gitment坑点小结`，我在这里补充说明我自己的问题。

官网提供的调用核心代码是

```html
<div id="container"></div>
<link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
<script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
<script>
var gitment = new Gitment({
  id: '页面 ID', // 可选。默认为 location.href
  owner: '你的 GitHub ID',
  repo: '存储评论的 repo',
  oauth: {
    client_id: '你的 client ID',
    client_secret: '你的 client secret',
  },
})
gitment.render('container')
</script>
```

#### 1、 变量的值不可删除\'\'

由于对 *html* 语言不熟悉，最开始删除了变量 `id`、 `owner`、 `repo`、 `client_id`、 `client_secret`后面值的\'\'，导致了评论模块的无法显示

#### 2、 对变量的理解

**id**

这个ID作为 GitHub Issues 两个 *label* 之一(另一个是 gitment) id记录网页的相对根目录的路径，是区别博客不同文章的标签，不可删除或注释

但 GitHub 对于 *label* 的长度有50字符的限制，而我们的路径中包含题目，如果一个汉字算一个字符的话不会超出限制，但是题目会转换成utf-8编码——就是一堆%的东西，这样每个汉字就是6个字符，超出长度限制是肯定的了。

所以网上有大牛给出解决方案，把 id 换成 page.subtitle(这个是自定义的，也就是说要在每篇博客的头文件中自定义一个不同的用英文写的 subtitle 变量) 或者是 page.date(这个是现阶段我博客文章固定有的)

考虑到 page.date 变量是定长的，而且是我博客文章根目录构建的核心变量，所以采用这种方式

这就又遇到坑的问题，参考了文献[1]的方法，修改 id 为

```html
id: '<%= page.date %>'
```

发现所有的文章都共享同一个评论了，这当然不是我所期望的，打开 GitHub Issues 就发现了问题

![](/images/posts/onlinecommit/006.png)

可以看到 *label*的名字不是期望的时间，而是一串固定写死的字符串，所以才会出现评论共享的情况

经过探索后发现参考文献[1]是基于 *hexo* 做的， 所以代码与我们的 *Jekyll* 有些许不同，经过\“大胆\”试错后，终于得到了正确的代码，修改 id 为

```html
id: '{{ page.date }}'
```

这样终于可以了，修改后，每个博文页面都需要初始化，最后在 GitHub Issues 的结果如下

![](/images/posts/onlinecommit/007.png)

> 这是最坑的变量，没有之一，对这一变量的修改和探索经历了整一天，主要还是因为对html的不熟悉


**owner**

也就是自己 GitHub 账号的名字

**repo**

存放评论信息的仓库，可以是当前网页的仓库(比如说我的就是NBSmalltree.github.io)，也可以是新建一个空的仓库(比如说NBSmalltree.commit)

**client_id** 、**client_secret**

这是按照官网教程在 `OAuth Application` 步骤生成的用户名和秘钥，只可惜此处没有做隐藏，只要记得保留\'\'即可

#### 3、 对中文的支持 

将原始平台的

```html
<link rel="stylesheet" href="https://imsun.github.io/gitment/style/default.css">
<script src="https://imsun.github.io/gitment/dist/gitment.browser.js"></script>
```

修改为

```html
<link rel="stylesheet" href="https://billts.site/extra_css/gitment.css">
<script src="https://billts.site/js/gitment.js"></script>
```

来源: [https://github.com/imsun/gitment/issues/104](https://github.com/imsun/gitment/issues/104)

#### 4、 上线结果

测试成功，支持Markdown

![](/images/posts/onlinecommit/008.png)

### 参考文献
  - [Gitment评论功能接入踩坑教程](http://ihtc.cc/2018/02/25/2018-02-25%20_Gitment%E8%AF%84%E8%AE%BA%E5%8A%9F%E8%83%BD%E6%8E%A5%E5%85%A5%E8%B8%A9%E5%9D%91%E6%95%99%E7%A8%8B/)
  - [知乎：国内有没有类似 Disqus 的社会化实时评论平台？](https://www.zhihu.com/question/19736680?sort=created)
  - [官网](https://github.com/imsun/gitment)
  
转载请注明：[汤锐彬的博客](https://nbsmalltree.github.io) » [网站在线评论实现](https://nbsmalltree.github.io/2018/04/Online_Commit_Guide/)

