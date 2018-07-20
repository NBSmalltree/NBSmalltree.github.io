---
layout: post
title: 饥荒Ubuntu服务器搭建
data: 2018-07-19
tags: Linux
---

### 初衷

　　饥荒联机版必须一方在线才能玩，这就导致主机方想下线了，另一方也不能玩了，而且主机方压力很大，所以就在弄了个服务器，大佬搞了个饥荒Windows云服务器，前段时间玩的时候很卡，检查后台是因为内存不足，毕竟免费套餐只有1核2G内存，在饥荒地图大的时候，内存就会吃紧，而Windows本身就占了600M，所以当时大佬随口一提说换一个Linux的服务器，还把他的京东云账号共享出来。一开始是很畏难的，没有接触过云服务器，又对Linux的命令不熟悉，但是因为不想写论文，就捣鼓了一下:)

![](/images/posts/dstserver/001.png)

### 技术准备

　　下文内容涉及四个基本技术，很多是我在实践过程中学习的，一并写在这里

#### **FTP传输**

　　主要分为两部分：Linux环境下的FTP服务器搭建 + Windows下的连接

##### **Linux环境下的FTP服务器搭建**

###### 1. 安装ftp服务器

`sudo apt-get install vsftpd`

###### 2. 如果安装失败或者配置出现问题，卸载 ftp服务器

`sudo apt-get purge vsftpd`

###### 3. 创建共享文件夹

`sudo mkdir /home/ftp`

###### 4. 修改 vsftpd.conf 配置文件

`sudo vim /etc/vsftpd.conf`

修改如下:

```
# 阻止 vsftpd 在独立模式下运行
listen=NO                 
# vsftpd 将监听 ipv6 而不是 IPv4
listen_ipv6=YES           
# 关闭匿名登录
anonymous_enable=NO       
# 允许本地用户登录
local_enable=YES          
# 启用可以修改文件的 FTP 命令
write_enable=YES          
# 本地用户新增档案时的umask 值
local_umask=022           
# 当用户第一次进入新目录时显示提示消息 
dirmessage_enable=YES     
# 显示在您的本地时区的时间目录列表
use_localtime=YES         
# 一个存有详细的上传和下载信息的日志文件
xferlog_enable=YES        
# 在服务器上针对 PORT 类型的连接使用端口 20（FTP 数据）
connect_from_port_20=YES  
# 不单独建立ftp用户，直接使用Ubuntu桌面用户就可以登陆
chroot_local_user=YES
chroot_list_enable=NO
# 能够登录的用户名单
chroot_list_file=/etc/vsftpd.chroot_list   
# 使用uft8文件系统
utf8_filesystem=YES
在文件最后添加下列内容
# 锁定一个共享目录
local_root=/home/eagle/ftp
# 给共享目录添加写权限
allow_writeable_chroot=YES
```

###### 5. 启动/重启/关闭 ftp 服务器

```
sudo service vsftpd start
sudo service vsftpd restart
sudo service vsftpd stop
```

###### 6. 浏览器登录ftp服务器

浏览器地址栏输入`ftp:// 117.48.210.178`

用当前云服务器的账号密码登陆

##### **Windows下的连接**

使用WinSCP，采用SFTP协议，并输入用户名和密码即可

![](/images/posts/dstserver/002.png)
![](/images/posts/dstserver/003.png)

#### SSH连接

这里也分为两个部分：云服务器配置 + XShell6连接

##### **云服务器配置**

云服务器配置方面非常方便，只需根据需要点点鼠标即可，这边选择的是Ubuntu16.04 64位 默认1核2GB内存，默认用户名root，密码自己定义

![](/images/posts/dstserver/004.png)

##### **XShell6连接**

###### 1. 填入公网ip

![](/images/posts/dstserver/005.png)

###### 2. 用户身份验证信息登入

![](/images/posts/dstserver/006.png)

###### 3. 点击连接

![](/images/posts/dstserver/007.png)

#### Linux命令

主要包含，删除、移动、复制、解压缩

|功能|命令|
|:-- |:-----|
|删除文件|`rm [filename]`|
|删除文件夹|`rmdir [filename]`|
|移动|`mv [filename] [dstdirname]`|
|复制|`cp [filename] [dstdirname]`|
|解压缩|`unzip [filename]` 或 `unzip [filename] [dstdirname]`|

#### Vim使用

区别两个模式：编辑模式和审阅模式

##### **编辑模式**

　　按i进入，按Esc退出

##### **审阅模式**

 - `H` 光标左移
 - `L` 光标右移
 - `J` 光标下移
 - `K` 光标上移

　　使用感受而言还是蛮爽的

### 基础框架搭建

　　主要参考[官网教程](https://forums.kleientertainment.com/topic/64441-dedicated-server-quick-setup-guide-linux/)

###### 1. 安装依赖库

`sudo apt-get install libstdc++6:i386 libgcc1:i386 libcurl4-gnutls-dev:i386`

如果没有对应版本，sudo apt-get search [libname]，找到对应版本安装

这边选择i386系列是因为饥荒的Linux版只有32位版本，Linux下的32和64位是用i386和x86_64来区分的

如果是32为操作系统，直接

`sudo apt-get install libstdc++6 libgcc1 libcurl4-gnutls-dev`

###### 2. 安装steamcmd

```
mkdir ~/steamcmd
cd ~/steamcmd
wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz
tar -xvzf steamcmd_linux.tar.gz
```

这边把具体的下载安装放到了.sh文件中进行，可以保证每次运行时的版本更新

###### 3. 建立地面和洞穴文件夹

```
mkdir -p ~/.klei/DoNotStarveTogether/MyDediServer/Master
mkdir -p ~/.klei/DoNotStarveTogether/MyDediServer/Caves
```

建成后的路径:

```
~/.klei/DoNotStarveTogether/MyDediServer/Master
~/.klei/DoNotStarveTogether/MyDediServer/Caves
```

###### 4. 建立自己的饥荒服务器密钥

![](/images/posts/dstserver/008.png)
![](/images/posts/dstserver/009.png)

按`Generate Server Token`按钮，生成密钥，我这边点了两次，其实两个都是一样的，复制其中一个到`~/.klei/DoNotStarveTogether/MyDediServer/cluster_token.txt`

`vi cluster_token.txt`

复制保存即可

也可以使用以下命令:

```
echo 'YourServerTokenHere' > ~/.klei/DoNotStarveTogether/MyDediServer/cluster_token.txt
```

###### 5. 建立cluster.ini文件

这个是服务器的基础配置文件，包含了名称、密码、游戏模式等参数

在`~/.klei/DoNotStarveTogether/MyDediServer/cluster.ini`中

`vi cluster.ini`

输入

```
[GAMEPLAY]
game_mode = survival
max_players = 6
pvp = false
pause_when_empty = true


[NETWORK]
cluster_description = This server is super duper!
cluster_name = Super Server
cluster_intention = cooperative
cluster_password =


[MISC]
console_enabled = true


[SHARD]
shard_enabled = true
bind_ip = 127.0.0.1
master_ip = 127.0.0.1
master_port = 10889
cluster_key = supersecretkey
```

也可以

```
base64 -di > ~/.klei/DoNotStarveTogether/MyDediServer/cluster.ini <<< 'W0dBTUVQTEFZXQpnYW1lX21vZGUgPSBzdXJ2aXZhbAptYXhfcGxheWVycyA9IDYKcHZwID0gZmFsc2UKcGF1c2Vfd2hlbl9lbXB0eSA9IHRydWUKCgpbTkVUV09SS10KY2x1c3Rlcl9kZXNjcmlwdGlvbiA9IFRoaXMgc2VydmVyIGlzIHN1cGVyIGR1cGVyIQpjbHVzdGVyX25hbWUgPSBTdXBlciBTZXJ2ZXIKY2x1c3Rlcl9pbnRlbnRpb24gPSBjb29wZXJhdGl2ZQpjbHVzdGVyX3Bhc3N3b3JkID0gCgoKW01JU0NdCmNvbnNvbGVfZW5hYmxlZCA9IHRydWUKCgpbU0hBUkRdCnNoYXJkX2VuYWJsZWQgPSB0cnVlCmJpbmRfaXAgPSAxMjcuMC4wLjEKbWFzdGVyX2lwID0gMTI3LjAuMC4xCm1hc3Rlcl9wb3J0ID0gMTA4ODkKY2x1c3Rlcl9rZXkgPSBzdXBlcnNlY3JldGtleQo='
```

###### 6. 更改上述文件的服务器名称和密码

包含`cluster_name` 和 `cluster_password`

###### 7. 建立地面的server.ini

在`~/.klei/DoNotStarveTogether/MyDediServer/Master/server.ini`内

`vi server.ini`

输入

```
[NETWORK]
server_port = 11000


[SHARD]
is_master = true


[STEAM]
master_server_port = 27018
authentication_port = 8768
```

也可以

```
base64 -di > ~/.klei/DoNotStarveTogether/MyDediServer/Master/server.ini <<< 'W05FVFdPUktdCnNlcnZlcl9wb3J0ID0gMTEwMDAKCgpbU0hBUkRdCmlzX21hc3RlciA9IHRydWUKCgpbU1RFQU1dCm1hc3Rlcl9zZXJ2ZXJfcG9ydCA9IDI3MDE4CmF1dGhlbnRpY2F0aW9uX3BvcnQgPSA4NzY4Cg=='
```

###### 8. 建立洞穴的server.ini

在`~/.klei/DoNotStarveTogether/MyDediServer/Caves/server.ini`内

`vi server.ini`

输入

```
[NETWORK]
server_port = 11001


[SHARD]
is_master = false
name = Caves


[STEAM]
master_server_port = 27019
authentication_port = 8769
```

也可以

```
base64 -di > ~/.klei/DoNotStarveTogether/MyDediServer/Caves/server.ini <<< 'W05FVFdPUktdCnNlcnZlcl9wb3J0ID0gMTEwMDEKCgpbU0hBUkRdCmlzX21hc3RlciA9IGZhbHNlCm5hbWUgPSBDYXZlcwoKCltTVEVBTV0KbWFzdGVyX3NlcnZlcl9wb3J0ID0gMjcwMTkKYXV0aGVudGljYXRpb25fcG9ydCA9IDg3NjkK'
```

###### 9. 建立洞穴的worldgenoverride.lua

在`~/.klei/DoNotStarveTogether/MyDediServer/Caves/worldgenoverride.lua`

`vi worldgenoverride.lua`

输入

```
return {
    override_enabled = true,
    preset = "DST_CAVE",
}
```

也可以

```
base64 -di > ~/.klei/DoNotStarveTogether/MyDediServer/Caves/worldgenoverride.lua <<< 'cmV0dXJuIHsKICAgIG92ZXJyaWRlX2VuYWJsZWQgPSB0cnVlLAogICAgcHJlc2V0ID0gIkRTVF9DQVZFIiwKfQo='
```

###### 10. 建立运行文件

在`~/`目录

`vi run_dedicated_servers.sh`

输入

```
#!/bin/bash

steamcmd_dir="$HOME/steamcmd"
install_dir="$HOME/dontstarvetogether_dedicated_server"
cluster_name="MyDediServer"
dontstarve_dir="$HOME/.klei/DoNotStarveTogether"

function fail()
{
        echo Error: "$@" >&2
        exit 1
}

function check_for_file()
{
    if [ ! -e "$1" ]; then
            fail "Missing file: $1"
    fi
}

cd "$steamcmd_dir" || fail "Missing $steamcmd_dir directory!"

check_for_file "steamcmd.sh"
check_for_file "$dontstarve_dir/$cluster_name/cluster.ini"
check_for_file "$dontstarve_dir/$cluster_name/cluster_token.txt"
check_for_file "$dontstarve_dir/$cluster_name/Master/server.ini"
check_for_file "$dontstarve_dir/$cluster_name/Caves/server.ini"

./steamcmd.sh +force_install_dir "$install_dir" +login anonymous +app_update 343050 validate +quit

check_for_file "$install_dir/bin"

cd "$install_dir/bin" || fail 

run_shared=(./dontstarve_dedicated_server_nullrenderer)
run_shared+=(-console)
run_shared+=(-cluster "$cluster_name")
run_shared+=(-monitor_parent_process $$)

"${run_shared[@]}" -shard Caves  | sed 's/^/Caves:  /' &
"${run_shared[@]}" -shard Master | sed 's/^/Master: /'
```

也可以

```
base64 -di > ~/run_dedicated_servers.sh <<< 'IyEvYmluL2Jhc2gKCnN0ZWFtY21kX2Rpcj0iJEhPTUUvc3RlYW1jbWQiCmluc3RhbGxfZGlyPSIkSE9NRS9kb250c3RhcnZldG9nZXRoZXJfZGVkaWNhdGVkX3NlcnZlciIKY2x1c3Rlcl9uYW1lPSJNeURlZGlTZXJ2ZXIiCmRvbnRzdGFydmVfZGlyPSIkSE9NRS8ua2xlaS9Eb05vdFN0YXJ2ZVRvZ2V0aGVyIgoKZnVuY3Rpb24gZmFpbCgpCnsKICAgICAgICBlY2hvIEVycm9yOiAiJEAiID4mMgogICAgICAgIGV4aXQgMQp9CgpmdW5jdGlvbiBjaGVja19mb3JfZmlsZSgpCnsKICAgIGlmIFsgISAtZSAiJDEiIF07IHRoZW4KICAgICAgICAgICAgZmFpbCAiTWlzc2luZyBmaWxlOiAkMSIKICAgIGZpCn0KCmNkICIkc3RlYW1jbWRfZGlyIiB8fCBmYWlsICJNaXNzaW5nICRzdGVhbWNtZF9kaXIgZGlyZWN0b3J5ISIKCmNoZWNrX2Zvcl9maWxlICJzdGVhbWNtZC5zaCIKY2hlY2tfZm9yX2ZpbGUgIiRkb250c3RhcnZlX2Rpci8kY2x1c3Rlcl9uYW1lL2NsdXN0ZXIuaW5pIgpjaGVja19mb3JfZmlsZSAiJGRvbnRzdGFydmVfZGlyLyRjbHVzdGVyX25hbWUvY2x1c3Rlcl90b2tlbi50eHQiCmNoZWNrX2Zvcl9maWxlICIkZG9udHN0YXJ2ZV9kaXIvJGNsdXN0ZXJfbmFtZS9NYXN0ZXIvc2VydmVyLmluaSIKY2hlY2tfZm9yX2ZpbGUgIiRkb250c3RhcnZlX2Rpci8kY2x1c3Rlcl9uYW1lL0NhdmVzL3NlcnZlci5pbmkiCgouL3N0ZWFtY21kLnNoICtmb3JjZV9pbnN0YWxsX2RpciAiJGluc3RhbGxfZGlyIiArbG9naW4gYW5vbnltb3VzICthcHBfdXBkYXRlIDM0MzA1MCB2YWxpZGF0ZSArcXVpdAoKY2hlY2tfZm9yX2ZpbGUgIiRpbnN0YWxsX2Rpci9iaW4iCgpjZCAiJGluc3RhbGxfZGlyL2JpbiIgfHwgZmFpbCAKCnJ1bl9zaGFyZWQ9KC4vZG9udHN0YXJ2ZV9kZWRpY2F0ZWRfc2VydmVyX251bGxyZW5kZXJlcikKcnVuX3NoYXJlZCs9KC1jb25zb2xlKQpydW5fc2hhcmVkKz0oLWNsdXN0ZXIgIiRjbHVzdGVyX25hbWUiKQpydW5fc2hhcmVkKz0oLW1vbml0b3JfcGFyZW50X3Byb2Nlc3MgJCQpCgoiJHtydW5fc2hhcmVkW0BdfSIgLXNoYXJkIENhdmVzICB8IHNlZCAncy9eL0NhdmVzOiAgLycgJgoiJHtydW5fc2hhcmVkW0BdfSIgLXNoYXJkIE1hc3RlciB8IHNlZCAncy9eL01hc3RlcjogLycKCgo='
```

###### 11. 赋予可执行文件权限

`chmod u+x ~/run_dedicated_servers.sh`

###### 12. 执行

`~/run_dedicated_servers.sh`

或者在`~/`目录下

`./run_dedicated_servers.sh`

**到这一步基本就可以了，除了mod以外原版的饥荒已经跑在服务器上了**

### mod的应用

主要参考[1](https://www.jianshu.com/p/772ce385e7ba) [2](http://hi.ktsee.com/608.html)

###### 1. 创建下载Mod的脚本（实际上无法自动下载，仍未解决）

```
cd ~/.klei/DoNotStarveTogether/MyDediServer
vi dedicated_server_mods_setup.lua
```

输入:

```
ServerModSetup("1301033176")
ServerModSetup("356420397")
ServerModSetup("363112314")
ServerModSetup("378160973")
ServerModSetup("466732225")
ServerModSetup("501385076")
ServerModSetup("596438350")
ServerModSetup("635020562")
ServerModSetup("648064643")
ServerModSetup("676297854")
ServerModSetup("764204839")
```

###### 2. 创建Mod配置文件

- Master端

```
cd ~/.klei/DoNotStarveTogether/MyDediServer/Master
vi modoverrides.lua
```

输入:

```
return {
  ["workshop-1301033176"]={ configuration_options={ LANG="auto" }, enabled=true },
  ["workshop-356420397"]={ configuration_options={  }, enabled=true },
  ["workshop-363112314"]={ configuration_options={  }, enabled=true },
  ["workshop-378160973"]={
    configuration_options={
      ENABLEPINGS=true,
      FIREOPTIONS=2,
      OVERRIDEMODE=false,
      SHAREMINIMAPPROGRESS=true,
      SHOWFIREICONS=true,
      SHOWPLAYERICONS=true,
      SHOWPLAYERSOPTIONS=2 
    },
    enabled=true 
  },
  ["workshop-466732225"]={ configuration_options={  }, enabled=true },
  ["workshop-501385076"]={ configuration_options={  }, enabled=true },
  ["workshop-596438350"]={ configuration_options={ uses=10000000000 }, enabled=true },
  ["workshop-635020562"]={ configuration_options={ role=2 }, enabled=true },
  ["workshop-648064643"]={ configuration_options={  }, enabled=true },
  ["workshop-676297854"]={
    configuration_options={
      campfire=true,
      coldfire=true,
      coldfirepit=true,
      deluxe_firepit=true,
      endo_firepit=true,
      fire_rez_health_level="default",
      fire_rez_health_penalty=0,
      fire_rez_hunger_level="default",
      fire_rez_lighting_strike_on_rez=true,
      fire_rez_sanity_level="default",
      fire_rez_spawn_skelly=true,
      firepit=true,
      heat_star=true,
      ice_star=true,
      null_option=true 
    },
    enabled=true 
  },
  ["workshop-764204839"]={
    configuration_options={
      AncGuardian1=2500,
      AncGuardian6=10000,
      Antlion1=3000,
      Antlion6=6000,
      Bearger1=3000,
      Bearger6=6000,
      Beefalo1=500,
      Beefalo6=1000,
      Beeguard1=100,
      Beeguard6=150,
      Beequeen1=3000,
      Beequeen6=22500,
      Buzzard1=125,
      Buzzard6=250,
      Clockworks1=300,
      Clockworks6=900,
      Deer1=350,
      Deer6=700,
      DeerGem1=750,
      DeerGem6=1500,
      Deerclops1=2000,
      Deerclops6=4000,
      Dragonfly1=3500,
      Dragonfly6=27500,
      Ewecus1=400,
      Ewecus6=800,
      Klaus11=2000,
      Klaus16=10000,
      Klaus21=5488,
      Klaus26=27440,
      Koalefant1=500,
      Koalefant6=1000,
      Krampus1=200,
      Krampus6=300,
      Merm1=250,
      Merm6=500,
      Moose1=3000,
      Moose6=6000,
      Mosling1=350,
      Mosling6=525,
      Pig_Guard1=300,
      Pig_Guard6=600,
      Poisonedtree1=12,
      Poisonedtree6=18,
      RLobster1=1500,
      RLobster6=3000,
      Shadow_Bishop11=250,
      Shadow_Bishop16=800,
      Shadow_Bishop21=800,
      Shadow_Bishop26=2500,
      Shadow_Bishop31=2500,
      Shadow_Bishop36=7500,
      Shadow_Knight11=300,
      Shadow_Knight16=900,
      Shadow_Knight21=900,
      Shadow_Knight26=2700,
      Shadow_Knight31=2700,
      Shadow_Knight36=8100,
      Shadow_Rook11=350,
      Shadow_Rook16=1000,
      Shadow_Rook21=1000,
      Shadow_Rook26=4000,
      Shadow_Rook31=4000,
      Shadow_Rook36=10000,
      Shootius1=1000,
      Shootius6=3000,
      Skeleton_A1=6000,
      Skeleton_A6=12000,
      Skeleton_C1=2000,
      Skeleton_C6=4000,
      Skeleton_F1=2000,
      Skeleton_F6=4000,
      Slurtle1=600,
      Slurtle6=1200,
      Spider_hider1=150,
      Spider_hider6=225,
      Spider_spitter1=175,
      Spider_spitter6=350,
      Spider_warrior1=200,
      Spider_warrior6=400,
      Spiderqueen1=1250,
      Spiderqueen6=2500,
      Tallbird1=400,
      Tallbird6=800,
      Teenbird1=300,
      Teenbird6=600,
      Tentacle_pillar1=500,
      Tentacle_pillar6=750,
      Toadstool1=7500,
      Toadstool6=52500,
      ToadstoolMisery1=11111,
      ToadstoolMisery6=99999,
      Treeguard1=2000,
      Treeguard6=3000,
      Voaltgoat1=350,
      Voaltgoat6=700,
      Walrus1=150,
      Walrus6=300,
      Warg1=600,
      Warg6=1800,
      Werepig1=350,
      Werepig6=525,
      max=0,
      min=0,
      mode=1,
      nulloption=1 
    },
    enabled=true 
  } 
}
```

- Caves端

同上操作，只是换个路径

```
cd ~/.klei/DoNotStarveTogether/MyDediServer/Caves
vi modoverrides.lua
```

填入内容同上

###### 3. 上传本地mod到服务器端

我的windows本地，打包mods.zip

`F:\SteamLibrary\steamapps\common\Don't Starve Together`
　　
上传至`~/dontstarvetogether_dedicated_server`文件夹

解压并覆盖

`unzip mods.zip`

重新运行服务器即可

### 关于其他

###### 1. 饥荒服务器经常会找不到列表，可以选择直连的方式

在进入游戏在线登陆后，键入Alt + ~ ，进入命令控制窗口，输入

`c_connect("117.48.210.178", 11000)`

###### 2. 服务器端经常会掉，是因为饥荒联机版版本更新，只有相同版本的才可一起连接，重启服务器即可



### 参考文献
  - [饥荒官网教程](https://forums.kleientertainment.com/topic/64441-dedicated-server-quick-setup-guide-linux/)
  - [国内教程补充](http://hi.ktsee.com/608.html)
  - [mod上传办法](https://www.jianshu.com/p/772ce385e7ba)
  
转载请注明：[汤锐彬的博客](https://nbsmalltree.github.io) » [饥荒Ubuntu服务器搭建](https://nbsmalltree.github.io/2018/07/DST_Ubuntu_Server/)
