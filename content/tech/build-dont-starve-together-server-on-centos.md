+++
Categories = []
Description = "Build DST server on centos 7.3"
Tags = ["饥荒", "游戏"]
date = "2016-10-03T14:19:23+08:00"
title = "在Centos7.3下开设一个饥荒服务器"

+++
# Centos7.2下建立饥荒服务器

坑爹的Klei，2年前把新建服务器的方式换了，然后饥荒wiki上不更新。找了很久才找到教程，主要参考：
[Klei论坛Linux配置教程(配置文件有问题)](http://forums.kleientertainment.com/topic/64441-dedicated-server-quick-setup-guide-linux/)  
[知名博主ttionya（配置文件主要参考）](http://blog.ttionya.com/article-1233.html)  
[Klei论坛关于配置文件解释](http://forums.kleientertainment.com/topic/64552-dedicated-server-settings-guide/)  

思路上来说，主要需要先安装Steam，然后通过Steam安装DST(Don't Starve Together)。然后进行相关配置文件，然后run就好了。要新建一个服务器需要开启2个DST实例，每个实例大概需要500-600M内存，再加上其他mod啊什么乱七八糟的，建议在2G内存服务器上开服。1G内存的服务器基本开不起来。CPU倒不是太大问题，基本上单核也就能吃得消。
忘了说了，请不要在root账户下开服，不安全。除了第一步，其他请在另外非管理员账户下做。

<!--more-->

## 安装相关依赖
`yum install glibc.i686 libstdc++.i686 libcurl.i686`

## 安装SteamCMD
这步是安装Steamcmd，一个可以在Terminal下运行stream的工具。可以参考[教程](https://developer.valvesoftware.com/wiki/SteamCMD#Linux)进行安装。
```bash
mkdir ~/steamcmd
cd ~/steamcmd
wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz
tar -xvzf steamcmd_linux.tar.gz
```

## 创建地图服务器相关文件夹
新建两个文件夹，`~/.klei/DoNotStarveTogether/MyDediServer/Master`作为主世界的地图及相关配置，`~/.klei/DoNotStarveTogether/MyDediServer/Caves`作为洞穴相关配置文件的存放地点。

## 创建cluster_token.txt
`cluster_token.txt`主要用来做服务器验证。网上说的在命令行里输入`TheNet:GenerateServerToken()`方法已经不能用了。正确的方法是，打开DST，有下架有一个Account，在打开的页面里面，选择`Generate Server Token`，这样就得到了一个token。
![DST_account_page](https://qhsong.blob.core.windows.net/qhsong-blog/2016/10/DST_account_page.png)

把这个token粘贴到~/.klei/DoNotStarveTogether/MyDediServer/cluster_token.txt文件中。

## 通用配置文件cluster.ini
设置一些通用的配置，保存到`~/.klei/DoNotStarveTogether/MyDediServer/cluster.ini`

```
[MISC]
max_snapshots =6
console_enabled = true
  
[SHARD]
shard_enabled = true
bind_ip = 127.0.0.1
master_ip = 127.0.0.1
master_port = 11001
cluster_key = dst
  
[STEAM]
steam_group_only = false
steam_group_id = 0
steam_group_admins = false
  
[NETWORK]
offline_server = false
tick_rate = 15
whitelist_slots = 2
cluster_name = Your server name
cluster_description = Your server Description
lan_only_cluster = false
cluster_intention = cooperative
  
[GAMEPLAY]
max_players = 16
pvp = true
game_mode = endless
pause_when_empty = true
vote_kick_enabled = true
```

## 独立配置部分
接下来单独配置洞穴和主世界文件。
主世界文件保存在`~/.klei/DoNotStarveTogether/MyDediServer/Master/server.ini
`
文件内容为：

```
[SHARD]
is_master = true
 
[STEAM]
authentication_port = 12345
master_server_port = 12346
 
[NETWORK]
server_port = 10999
```

洞穴部分，文件位置为`~/.klei/DoNotStarveTogether/MyDediServer/Caves/server.ini`， 其内容如下：

```
[SHARD]
is_master = false
name = caves
 
[STEAM]
authentication_port = 12347
master_server_port = 12348
 
[NETWORK]
server_port = 11000
```

## 配置地图相关属性
所有的和地图相关的属性都保存在了`worldgenoverride.lua`文件中，若采用默认配置，可参考[这里](http://blog.ttionya.com/article-1235.html)文件配置。必须要配置，即使使用默认也要配置。

## 运行脚本
粘贴如下脚本并运行，最好是在HOME目录下运行。这个脚本会帮你安装DST，以及剩下一系列的事情。

```bash
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
然而这个脚本是运行不了的，他会提示你`libcurl-gnutls.so.4`找不到。我们需要到`/~/dontstarvetogether_dedicated_server/bin/lib32`下执行

```
ln -s /usr/lib/libcurl.so.4 libcurl-gnutls.so.4
```
完了之后回到$HOME，再次运行脚本。应该能看到执行成功。接下来你可以到[http://my.jacklul.com/dstservers](http://my.jacklul.com/dstservers)可以查你服务器运行情况。如果你是输入的是服务器IP，那么就可以直接查询你服务器上的开服情况。

## mod配置
无mod不欢啊，mod配置详见知名博主ttionya的文章，里面很详细。只要把文件路径改一下就行了

##最后
Have Fun~~

国庆打饥荒根本停不下来。。。


