---
layout: post
title: Mac 平台安装MySQL
data: 2021-04-02
tags: Mac
---



1、下载对应安装包`mysql-8.0.23-macos10.15-x86_64.tar`

2、sudo 解压该tar包到`/usr/local`目录下

```bash
sudo tar -xvf mysql-8.0.23-macos10.15-x86_64.tar -C /usr/local
```

3、进入`/usr/local`路径下，重命名包为mysql

```bash
sudo mv mysql-8.0.23-macos10.15-x86_64 mysql
```

4、修改文件夹所属组

```bash
sudo chown -R root:wheel mysql
```

5、进入`/usr/local/mysql`路径，初始化数据库

```bash
sudo bin/mysqld --initialize --user=mysql
```

【重要】**记住安装完成后到初始密码**

```
2021-04-02T10:58:22.939832Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: kx9m7!jr=/wY
```

6、启动mysql数据库，检测是否启动成功

回到`/usr/local/mysql`路径

```bash
# 启动
sudo support-files/mysql.server start

# 重启
sudo support-files/mysql.server restart

# 停止
sudo support-files/mysql.server stop

# 检查 MySQL 运行状态
sudo support-files/mysql.server status
```

7、修改mysql初始密码

要在mysql服务启动前提下

进入`/usr/local/mysql/bin`路径下，执行

```bash
sudo ./mysqladmin -u root -p password
```

提示输入初始密码，输入初始密码，并输入两边新密码

8、利用mysql自带客户端连接mysql

```bash
./mysql -u root -p root
```

- 查看表空间`show databases;`
- 使用某一表空间`use sys`其中的sys是某一表空间
- 查看当前表空间下的所有表`show tables;`
- 查看表结构`desc table_name `

9、问题

一段时间后，如果执行mysql服务状态查询、起停时，报了这个错误

```bash
ERROR! Multiple MySQL running but PID file could not be found (33200 53254 )
```

解决办法，关闭所有的mysql进程

```bash
ps -ef | grep mysql
kill -9 进程号
```

然后重启mysql即可

10、安装mac版navicat后，提示“已损坏，移至废纸篓”，在终端执行

```bash
sudo xattr -r -d com.apple.quarantine /Applications/Navicat\ Premium.app
```

