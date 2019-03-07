---
layout: post
title: ShawdowSockes Server配置
data: 2019-03-07
tags: Linux
---


### 1. 引言

本人之前尝试过内网穿透，但不幸没有成功，打算从基础的代理服务器配置入手，尝试配置ShadowSockes Server，选择了腾讯云的国内VPS作为测试，参考了两篇教程[1](http://www.mser.us/2018/02/18/handbook-of-ss/)[2](https://teddysun.com/486.html)

### 2. Ubuntu设置

#### 1) 登陆ssh

使用putty登陆云服务器

![](/images/posts/ssserver/001.png)

#### 2) 下载脚本

```
wget –no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
```

结束之后如下图所示

![](/images/posts/ssserver/002.png)

教程中有提示，如果出现错误提示bash:wget:command not found，需要先执行yum -y install wget命令

#### 3) 运行脚本

```
chmod +x shadowsocks-all.sh
```

![](/images/posts/ssserver/003.png)

```
sudo ./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

出现此界面，默认回车，选择python版本，因为之前下载的是四合一的脚本

![](/images/posts/ssserver/004.png)

提示输入服务器密码，我们这边测试输入123456

![](/images/posts/ssserver/005.png)

提示输入服务端口，默认13740，这里的端口选择有点讲究，我之前选择过3333，可能占用了其他程序的端口，导致代理无法连接，我们选择默认或教程建议的8443端口，本次演示选择8443

![](/images/posts/ssserver/006.png)

提示选择传输加密方式，考虑到苹果端的通用性，选择7.aes-256-cfb

![](/images/posts/ssserver/007.png)

最后一步回车确认，安装过程可能需要几分钟，可能中间会因为服务器网络连接而失败，多试几次就好了

![](/images/posts/ssserver/008.png)

保存记录好自己的信息

![](/images/posts/ssserver/009.png)

#### 4) 开启端口

这一步可选，如果无法代理再选择强制开启

查看本地端口开启情况

```
sudo ufw status
```

![](/images/posts/ssserver/010.png)

打开8443端口

```
sudo ufw allow 8443
```

![](/images/posts/ssserver/011.png)

防火墙开启

```
sudo ufw enable
```

![](/images/posts/ssserver/012.png)

防火墙重启

```
sudo ufw reload
```

![](/images/posts/ssserver/013.png)

#### 5) 其他要说的

最后附上脚本的常规操作说明

```
/etc/init.d/shadowsocks-python start | stop | restart | status
```

### 3. 客户端设置

此处iphone和安卓的操作均为转载

#### 1) Windows下设置

使用[shadowsocks](https://github.com/shadowsocks/shadowsocks-windows/releases)

下载安装完成后，打开在编辑服务器选项卡中设置

![](/images/posts/ssserver/014.png)

完成后，选择启用系统代理，选择全局模式或pac模式即可

### 参考文献
  - [手把手教你搭建安全好用完全属于自己的科学上网神器](http://www.mser.us/2018/02/18/handbook-of-ss/)
  - [Shadowsocks 一键安装脚本（四合一）](https://teddysun.com/486.html)
  
转载请注明：[汤锐彬的博客](https://nbsmalltree.github.io) » [ShawdowSockes Server配置](https://nbsmalltree.github.io/2019/03/ShawdowSockes_Server_Set/)