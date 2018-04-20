---
layout: post
title: Windows配置Jekyll
date: 2018-04-17 
tag: wits
---

1. 下载 [Ruby Installer on Windows](https://rubyinstaller.org/)
2. 验证是否安装完成
```
$ ruby -v
```
3. 安装Jekyll
```
$ gem install jekyll
```
4. 安装补充包
```
$ gem install bundler minima tzinfo-data wdm
```
5. 进入到某一欲意建博客的根目录
6. 创建博客
```
$ Jekyll new myBlog
```
7. 进入博客目录
```
$ cd myBlog
``` 
8. 启动本地服务
```
$ Jekyll serve
``` 
9. 发现问题
```
> Jekyll 3.7.3 | Error : Permission denied – bind(2) for 127.0.0.1:4000
``` 
**解决方法：**

修改_config.yml，后面添加一行：port: 4001（数字前添加空格）


