# EarthWorm 常用命令总结

[TOC]

## 0 介绍

EarthWorm是内网穿透的神器，拥有三项功能正向代理，反向代理，端口转发。

为实现这些功能，EarthWorm建立了六大功能模块。

分别是

ssocksd , rcsocks , rssocks , 
 lcx_listen , lcx_tran , lcx_slave

从三项功能的角度可以这样划分

| 功能名称 |           对应模块            |
| :------: | :---------------------------: |
| 正向代理 |            ssocksd            |
| 反向代理 |        rcsocks,rssocks        |
| 端口转发 | lcx_listen,lcx_slave,lcx_tran |

下面对命令使用方法和使用场景进行总结

## 1 命令详解

下面是ew的帮助提示

```shell
VERSION : free1.2 
 ./xxx ([-options] [values])*
 options :
 Eg: ./xxx -s ssocksd -h 
 -s state setup the function.You can pick one from the 
 following options:
 ssocksd , rcsocks , rssocks , 
 lcx_listen , lcx_tran , lcx_slave
 -l listenport open a port for the service startup.
 -d refhost set the reflection host address.
 -e refport set the reflection port.
 -f connhost set the connect host address .
 -g connport set the connect port.
 -h help show the help text, By adding the -s parameter,
 you can also see the more detailed help.
 -a about show the about pages
 -v version show the version. 
 -t usectime set the milliseconds for timeout. The default 
 value is 1000 
 ......
```



```shell
版本 : free1.2 
 ./xxx ([-options] [values])*
 options :
 Eg: ./xxx -s ssocksd -h 
 -s 该选项指定你需要使用的功能模块，以下6项内容中选择一项：
  ssocksd , rcsocks , rssocks , 
 lcx_listen , lcx_tran , lcx_slave
 -l 该命令为服务启动开启指定端口
 -d 指定转发或反弹的主机地址
 -e 指定转发或反弹的主机端口
 -f 指定连接或映射的主机地址
 -g 指定连接或映射的主机端口
 -h 打开帮助提示，当与-s参数共同使用时可以看到更详细的内容
 -a 关于
 -v 显示版本 
 -t usectime set the milliseconds for timeout. The default 
 value is 1000 
 ......
```

## 2 使用实例

### 2.1 建立ssocks5代理

```shell
 You can create a SOCKS5 server like this : 
 ./ew -s ssocksd --listenport 1080
 or 
 ./ew -s ssocksd -l 1080
```

通过此方法可以建立socks5本机代理，远程计算机可以通过类似proxifier等代理工具使用本机代理。

### 2.2 反弹ssocks5代理

此处需要使用两个功能模块rcsosks，rssocks

rcsocks：

```shell
./ew_for_linux64 -s rcsocks -h 

 You can create a rcsocks tunnel like this : 
 ./ew -s rcsocks --listenPort 1080 --refPort 8888
 or 
 ./ew -s rcsocks -l 1080 -e 8888
```

rssocks：

```shell
./ew_for_linux64 -s rssocks -h

 You can create a rssocks Server like this : 
 ./ew -s rssocks --refHost xxx.xxx.xxx.xxx --refPort 8888
 or 
 ./ew -s rssocks -d xxx.xxx.xxx.xxx -e 8888
```

在很多场景，无法直接连接需要当做代理的主机，那么怎么解决这个问题呢？EarthWorm的解决方法十分简单，考虑到我们无法直接连接代理主机，但是代理主机可以主动连接我们，因此需要等待该主机主动连接承担代理任务。而这就是rccosks，rssocks的命令设计想法。rcsocks建立本地代理客户端，指定本地监听反弹的端口，rssocks建立本地代理服务端，并指定建立服务端后主动连接的远程主机的IP和端口。

### 2.3 端口转发

该功能涉及三个功能模块：lcx_listen,lcx_slave,lcx_tran

相关用法如下：

lcx_listen：

```shell
./ew_for_linux64 -s lcx_listen -h

 You can create a lcx_listen tunnel like this : 
 ./ew -s lcx_listen --listenPort 1080 --refPort 8888
 or 
 ./ew -s lcx_listen -l 1080 -e 8888
```



lcx_slave：

```shell
./ew_for_linux64 -s lcx_slave -h 

 You can create a lcx_slave tunnel like this : 
 ./ew -s lcx_slave --refhost [ref_ip] --refport 1080 -connhost [connIP] --connport 8888
 or 
 ./ew -s lcx_slave -d [ref_ip] -e 1080 -f [connIP] -g 8888
```



lcx_tran：

```shell
./ew_for_linux64 -s lcx_tran -h 

 You can create a lcx_tran tunnel like this : 
 ./ew -s lcx_tran --listenport 1080 -connhost xxx.xxx.xxx.xxx --connport 8888
 or 
 ./ew -s lcx_tran -l 1080 -f [connIP] -g 8888
```

以上三个模块可以建立任意形式的端口转发，可以将内网最深处的主机端口转发出来

常见的使用命令组合如下：

```shell
./ew -s lcx_listen -l 1080 -e 8888
./ew -s lcx_tran -l 1080 -f [connIP] -g 8888
```



数据流向  local:1080  < --- >  local:8888  < --- >  remoteIP:1080

用于实现将本地端口转发到远程可直接连接的某主机的端口，



```shell
./ew -s lcx_listen -l 1080 -e 8888
./ew -s lcx_slave -d [ref_ip] -e 1080 -f [connIP] -g 1080
```

数据流向  connIP:8888  < --- >  connIP:1080  < --- >  ref_ip:1080 



还可以有许多组合方式，需要时进行总结。