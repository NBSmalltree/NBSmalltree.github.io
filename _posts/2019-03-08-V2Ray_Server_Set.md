---
layout: post
title: V2Ray Server配置
data: 2019-03-08
tags: Linux
---


### 1. 引言

昨天尝试了ShadowSockes Server，今天尝试一下[V2Ray](https://www.v2ray.com) Server

V2Ray(Project V) 相对于 Shadowsocks，V2Ray 更像全能选手，拥有更多可选择的协议 / 传输载体 (Socks、HTTP、TLS、TCP、mKCP、WebSocket )，还有强大的路由功能，不仅仅于此，它亦包含 Shadowsocks 组件，你只需要安装 V2Ray，你就可以使用所有的 V2Ray 相关的特性包括使用 Shadowsocks，由于 V2Ray 是使用 GO 语言所撰写的，天生的平台部署优势，下载即可使用，当然啦，由于 V2Ray 的配置相对来说是很繁琐的，毫无夸张的说，但是有了本人所写的 最好用的 V2Ray 一键安装脚本 & 管理脚本 加持下，使用 V2Ray 便会显得轻松多了[1](https://ccat.xyz/post/2/)。

### 2. Ubuntu设置

#### 1) 登陆ssh

使用putty登陆云服务器

![](/images/posts/v2rayserver/001.png)

#### 2) 下载脚本

```
wget –no-check-certificate -O v2ray.sh https://git.io/v2ray.sh
```

结束之后如下图所示

![](/images/posts/v2rayserver/002.png)

#### 3) 运行脚本

```
chmod +x shadowsocks-all.sh
```

![](/images/posts/v2rayserver/003.png)

```
sudo ./v2ray.sh
```

出现此界面，选择1.安装

![](/images/posts/v2rayserver/004.png)

提示选择传输协议，选择默认TCP，也可以选择加速的mKCP类的选项，此处默认TCP

![](/images/posts/v2rayserver/005.png)

提示输入服务端口，随机默认

![](/images/posts/v2rayserver/006.png)

广告拦截选项无所谓，此处选择Y

提示配置Shadowsocks，选择Y

![](/images/posts/v2rayserver/007.png)

选择Shadowsocks端口，默认与v2ray不同即可

![](/images/posts/v2rayserver/008.png)

提示输入密码

选择加密协议，选择2.aes-256-cfb

![](/images/posts/v2rayserver/009.png)

两次确认，开始安装，完成后如下图所示，保存记录信息

![](/images/posts/v2rayserver/010.png)

#### 4) 其他要说的

管理 v2ray 用以下命令

```
sudo v2ray
```

### 3. 客户端设置

此处只介绍Windows下设置，使用v2rayN软件，设置如下

![](/images/posts/v2rayserver/011.png)

完成后，选择启用HTTP代理，选择全局模式或pac模式即可

### 参考文献
  - [V2Ray 一键搭建和优化详细图文教程](https://ccat.xyz/post/2/)
  - [Project V](https://www.v2ray.com/)
  
转载请注明：[汤锐彬的博客](https://nbsmalltree.github.io) » [V2Ray Server配置](https://nbsmalltree.github.io/2019/03/V2Ray_Server_Set/)