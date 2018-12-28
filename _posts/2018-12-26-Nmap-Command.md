---
layout: post
title: 'Nmap命令详解'
date: 2018-12-26
author: Jacob
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: Nmap

---

# Nmap命令详解

* TOC

## 0 Nmap 介绍

## 1 Nmap命令详解

### 1.1 Nmap 命令help详解（内附中文翻译）

```

```

Nmap 7.70 ( https://nmap.org )
Usage: nmap [Scan Type(s)][Options] {target specification}
TARGET SPECIFICATION: **指定目标的命令**
  Can pass hostnames, IP addresses, networks, etc.
  Ex: scanme.nmap.org, microsoft.com/24, 192.168.0.1; 10.0.0-255.1-254
  -iL <inputfilename>: Input from list of hosts/networks

>  **指定输入文件，可用于指定扫描的目标 ex： -iL 192.168.0.0/16**

  -iR <num hosts>: Choose random targets

>  **选择随机目标 ex：-iR 200 （选定随机的200个目标）**

  --exclude <host1[,host2][,host3],...>: Exclude hosts/networks

> **排除主机，即扫描的时候越过该主机**

  --excludefile <exclude_file>: Exclude list from file

> **同上，引进黑名单 ex：--excludefile host.txt**  

HOST DISCOVERY: **主机发现**

  -sL: List Scan - simply list targets to scan

>  **显示扫描主机的列表，有助于查看**

  -sn: Ping Scan - disable port scan

>  **使用ping进行扫描，由于现有网络主机大部分防火墙拒绝icmp协议，一般无用**

  -Pn: Treat all hosts as online -- skip host discovery

> **不进行主机发现，直接进行深层扫描，即使用此命令时，nmap认为主机在线**

  -PS/PA/PU/PY[portlist]: TCP SYN/ACK, UDP or SCTP discovery to given ports

> **使用四种方式探测端口**
>
> **-PS 端口列表用,隔开[tcp80 syn 扫描]**
> **-PA 端口列表用,隔开[ack扫描](PS+PA测试状态包过滤防火墙[非状态的PA可以过])【默认扫描端口1-1024】**
> **-PU 端口列表用,隔开[udp高端口扫描 穿越只过滤tcp的防火墙]**

  -PE/PP/PM: ICMP echo, timestamp, and netmask request discovery probes

> **-PE 的ICMP Echo扫描简单来说是通过向目标发送ICMP Echo数据包来探测目标主机是否存活，但由于许多主机的防火墙会禁止这些报文，所以仅仅ICMP扫描通常是不够的**
>
> **-PP 的ICMP time stamp时间戳扫描在大多数防火墙配置不当时可能会得到回复，可以以此方式来判断目标主机是否存活。倘若目标主机在线，该命令还会探测其开放的端口以及运行的服务**
>
> **-PM 的ICMP address maskPing地址掩码扫描会试图用备选的ICMP等级Ping指定主机，通常有不错的穿透防火墙的效果**

  -PO[protocol list]: IP Protocol Ping

> **“ -PO ”选项进行一个IP协议ping。**
>
> **语法:`nmap –PO 协议 目标`**
>
> **IP协议ping用指定的协议发送一个包给目标。如果没有指定协议，默认的协议是 1 (ICMP), 2 (IGMP)和4 (IP-in-IP)**

  -n/-R: Never do DNS resolution/Always resolve [default: sometimes]

> **选择是否进行dns解析**
>
> **“ -n ”参数用来说明不使用反向域名解析。**
>
> **语法:`nmap –n 目标`**
>
> **反向域名解析会明显的降低Nmap扫描的速度。使用“-n”选项会极大的减少扫描时，特别是当扫描大量的主机时。如果你不关心目标系统的DNS信息，更喜欢进行一个能快速产生结果的扫描时，可以使用这个选项**

  --dns-servers <serv1[,serv2],...>: Specify custom DNS servers

> **明确使用的DNS服务器**

  --system-dns: Use OS's DNS resolver

> **使用主机nmap运行主机的dns**
>
> **选项-*system-dns*指示*NMAP*使用主机系统自带的DNS解析器,而不是其自身内部的方法**

  --traceroute: Trace hop path to each host

> **跟踪扫描主机的网络路线**

SCAN TECHNIQUES: 
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans

> **指定nmap的扫描方式，默认为sT**

  -sU: UDP Scan

> **使用UDP协议进行扫描**

  -sN/sF/sX: TCP Null, FIN, and Xmas scans

> **指定使用TCP Null, FIN, and Xmas scans秘密扫描方式来协助探测对方的TCP端口状态**

  --scanflags <flags>: Customize TCP scan flags

> **定制TCP包的flags**

  -sI <zombie host[:probeport]>: Idle scan

> **指定使用idle scan方式来扫描目标主机（前提需要找到合适的zombie host）**

  -sY/sZ: SCTP INIT/COOKIE-ECHO scans

> **使用SCTP INIT/COOKIE-ECHO来扫描SCTP协议端口的开放的情况**

  -sO: IP protocol scan

> **使用IP protocol 扫描确定目标机支持的协议类型**

  -b <FTP relay host>: FTP bounce scan

> **使用FTP bounce scan扫描方式**

PORT SPECIFICATION AND SCAN ORDER:
  -p <port ranges>: Only scan specified ports

​    Ex: -p22; -p1-65535; -p U:53,111,137,T:21-25,80,139,8080,S:9

> 扫描指定的端口
> 实例: -p22; -p1-65535; -p U:53,111,137,T:21-25,80,139,8080,S:9（其中T代表TCP协议、U代表UDP协议、S代表SCTP协议）

  --exclude-ports <port ranges>: Exclude the specified ports from scanning

>

  -F: Fast mode - Scan fewer ports than the default scan

> **快速模式，仅扫描TOP 100的端口**

  -r: Scan ports consecutively - don't randomize

> **不进行端口随机打乱的操作（如无该参数，nmap会将要扫描的端口以随机顺序方式扫描，以让nmap的扫描不易被对方防火墙检测到）**

  --top-ports <number>: Scan <number> most common ports

> **扫描开放概率最高的number个端口（nmap的作者曾经做过大规模地互联网扫描，以此统计出网络上各种端口可能开放的概率。以此排列出最有可能开放端口的列表，具体可以参见文件：nmap-services。默认情况下，nmap会扫描最有可能的1000个TCP端口）**

  --port-ratio <ratio>: Scan ports more common than <ratio>

> **扫描指定频率以上的端口。与上述--top-ports类似，这里以概率作为参数，让概率大于--port-ratio的端口才被扫描。显然参数必须在在0到1之间，具体范围概率情况可以查看nmap-services文件**

SERVICE/VERSION DETECTION: **网络主机服务版本探测**
  -sV: Probe open ports to determine service/version info

> **指定让Nmap进行版本侦测**

  --version-intensity <level>: Set from 0 (light) to 9 (try all probes)

> **指定版本侦测强度（0-9），默认为7。数值越高，探测出的服务越准确，但是运行时间会比较长。**

  --version-light: Limit to most likely probes (intensity 2)

> **指定使用轻量侦测方式 (intensity 2)**

  --version-all: Try every single probe (intensity 9)

> **尝试使用所有的probes进行侦测 (intensity 9)**

  --version-trace: Show detailed version scan activity (for debugging)

> **显示出详细的版本侦测过程信息。**

SCRIPT SCAN:**脚本扫描**
  -sC: equivalent to --script=default

> **指定script脚本为默认值**

  --script=<Lua scripts>: <Lua scripts> is a comma separated list of
​           directories, script-files or script-categories

> **使用某个或某类脚本进行扫描，支持通配符描述**

  --script-args=<n1=v1,[n2=v2,...]>: provide arguments to scripts

> **为脚本提供默认参数**

  --script-args-file=filename: provide NSE script args in a file

> **使用文件来为脚本提供参数**

  --script-trace: Show all data sent and received

> **显示脚本执行过程中发送与接收的数据**

  --script-updatedb: Update the script database.

> **更新脚本数据库，每次更新脚本后**

  --script-help=<Lua scripts>: Show help about scripts.
​           <Lua scripts> is a comma-separated list of script-files or
​           script-categories.

> **显示脚本的帮助信息，其中<Luascripts>部分可以逗号分隔的文件或脚本类别**

OS DETECTION:**操作系统探测**
  -O: Enable OS detection

> **指定Nmap进行OS侦测。**

  --osscan-limit: Limit OS detection to promising targets

> **限制Nmap只对确定的主机的进行OS探测（至少需确知该主机分别有一个open和closed的端口）。**

  --osscan-guess: Guess OS more aggressively

> **大胆猜测对方的主机的系统类型。由此准确性会下降不少，但会尽可能多为用户提供潜在的操作系统。**

TIMING AND PERFORMANCE:
  Options which take <time> are in seconds, or append 'ms' (milliseconds),
  's' (seconds), 'm' (minutes), or 'h' (hours) to the value (e.g. 30m).
  -T<0-5>: Set timing template (higher is faster)

> **-T0  偏执的：非常非常慢，用于IDS逃逸**
>
> **-T1  猥琐的：相当慢，用于IDS逃逸**
>
> **-T2  有礼貌的：降低速度以消耗更小的带宽，比默认慢十倍**
>
> **-T3  普通的：默认，根据目标的反应自动调整时间模式**
>
> **-T4  野蛮的：假定处在一个很好的网络环境，请求可能会淹没目标**
>
> **-T5  疯狂的：非常野蛮，很可能会淹没目标端口或是漏掉一些开放端口**

  --min-hostgroup/max-hostgroup <size>: Parallel host scan group sizes

> **平行的主机扫描组的大小**

  --min-parallelism/max-parallelism <numprobes>: Probe parallelization

> **并行探测**

  --min-rtt-timeout/max-rtt-timeout/initial-rtt-timeout <time>: Specifies
​      probe round trip time.

> **指定每轮探测的时间**

--max-retries <tries>: Caps number of port scan probe retransmissions.

> **扫描探测的上限次数设定**

  --host-timeout <time>: Give up on target after this long

>  **设置timeout时间**

  --scan-delay/--max-scan-delay <time>: Adjust delay between probes

> **调整两次探测之间的延迟**

  --min-rate <number>: Send packets no slower than <number> per second
  --max-rate <number>: Send packets no faster than <number> per second

> **每秒发送数据包不少于<number>次**

FIREWALL/IDS EVASION AND SPOOFING:**防火墙/IDS规避**
  -f; --mtu <val>: fragment packets (optionally w/given MTU)

> **指定使用分片、指定数据包的MTU.**

  -D <decoy1,decoy2[,ME],...>: Cloak a scan with decoys

> **用一组IP地址掩盖真实地址，其中ME填入自己的IP地址**

  -S <IP_Address>: Spoof source address

> **伪装成其他IP地址**

  -e <iface>: Use specified interface

> **使用特定的网络接口,指定网卡**

  -g/--source-port <portnum>: Use given port number

> **使用指定源端口**

  --proxies <url1,[url2],...>: Relay connections through HTTP/SOCKS4 proxies

> **使用代理**

  --data <hex string>: Append a custom payload to sent packets

> **给数据包添加指定数据**

  --data-string <string>: Append a custom ASCII string to sent packe**ts**

> **给数据包添加指定ASCII字符串**

  --data-length <num>: Append random data to sent packets

> **填充随机数据让数据包长度达到Num**

  --ip-options <options>: Send packets with specified ip options

> **使用指定的IP选项来发送数据包。**

  --ttl <val>: Set IP time-to-live field

> **设置time-to-live时间,没什么用**

 --spoof-mac <mac address/prefix/vendor name>: Spoof your MAC address

> **伪装MAC地址，伪装mac地址为指定值**

  --badsum: Send packets with a bogus TCP/UDP/SCTP checksum

> **使用错误的checksum来发送数据包（正常情况下，该类数据包被抛弃，如果收到回复，说明回复来自防火墙或IDS/IPS）**
>
> **可用户检测是否有防火墙或IDS/IPS**

OUTPUT:
  -oN/-oX/-oS/-oG <file>: Output scan in normal, XML, s|<rIpt kIddi3,
​     and Grepable format, respectively, to the given filename.

> **指定输出文件格式，分为正常，XML**

  -oA <basename>: Output in the three major formats at once

> **用<basename>生成以上格式的文件** 

  -v: Increase verbosity level (use -vv or more for greater effect)

>  **这个选项使用两次，会提供更详细的信息**

  -d: Increase debugging level (use -dd or more for greater effect)

>  **(提高或设置调试级别，最高级别-d9，输出更多的细节)**

  --reason: Display the reason a port is in a particular state



  --open: Only show open (or possibly open) ports
  --packet-trace: Show all packets sent and received
  --iflist: Print host interfaces and routes (for debugging)
  --append-output: Append to rather than clobber specified output files
  --resume <filename>: Resume an aborted scan
  --stylesheet <path/URL>: XSL stylesheet to transform XML output to HTML
  --webxml: Reference stylesheet from Nmap.Org for more portable XML
  --no-stylesheet: Prevent associating of XSL stylesheet w/XML output
MISC:
  -6: Enable IPv6 scanning
  -A: Enable OS detection, version detection, script scanning, and traceroute
  --datadir <dirname>: Specify custom Nmap data file location
  --send-eth/--send-ip: Send using raw ethernet frames or IP packets
  --privileged: Assume that the user is fully privileged
  --unprivileged: Assume the user lacks raw socket privileges
  -V: Print version number
  -h: Print this help summary page.
EXAMPLES:
  nmap -v -A scanme.nmap.org
  nmap -v -sn 192.168.0.0/16 10.0.0.0/8
  nmap -v -iR 10000 -Pn -p 80
SEE THE MAN PAGE (https://nmap.org/book/man.html) FOR MORE OPTIONS AND EXAMPLES

### 1.2 Nmap 命令思维导图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227101003871.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)
![](/home/jacob/Desktop/Nmap.png)

## 2 Nmap 常见使用场景以及相关命令

### 2.1 Nmap常用扫描命令

#### 2.1.1 扫描固定端口，以sql Server为例

```shell
//扫描并输出数据库信息

nmap.exe -sS -p 1433,1434 -f --script ms-sql-info  -oG - -oX m
s-sql.xml 110.42.96.0/24

//爆破数据库

nmap.exe -sT -p 1433,1434 -f --script ms-sql-brute -script-arg
s userdb=C:\WorkSpace\Nmap\temp\sqluser.dic,passdb=C:\WorkSpace\Nmap\temp\sqlpas
s.dic -oG - -oX ms-sql-test.xml 110.42.96.0/24
```

#### 2.1.2 获取远程主机的系统类型及开放端口

nmap -sS -P0 -sV -O <target>

这里的 < target > 可以是单一 IP, 或主机名，或域名，或子网

-sS TCP SYN 扫描 (又称半开放,或隐身扫描)
-P0 允许你关闭 ICMP pings.
-sV 打开系统版本检测
-O 尝试识别远程操作系统

其它选项:

-A 同时打开操作系统指纹和版本检测
-v 详细输出扫描情况.

```shell
nmap -sS -P0 -A -v < target >
```

#### 2.1.3 列出开放了指定端口的主机列表

```shell
nmap -sT -p 80 -oG – 192.168.1.* | grep open
```

#### 2.1.4 在网络寻找所有在线主机

```shell
nmap -sP 192.168.0.*
nmap -sP 192.168.0.0/24
```

#### 2.1.5 Ping 指定范围内的 IP 地址

```shell
nmap -sP 192.168.1.100-254
```

#### 2.1.6 在某段子网上查找未占用的 IP

```shell
nmap -T4 -sP 192.168.2.0/24 && egrep “00:00:00:00:00:00″ /proc/net/arp
```

#### 2.1.7 在局域网上扫找 Conficker 蠕虫病毒

```shell
nmap -PN -T4 -p139,445 -n -v –script=smb-check-vulns –script-args safe=1 192.168.0.1-254
```

#### 2.1.8 扫描网络上的恶意接入点 （rogue APs）.

```shell
nmap -A -p1-85,113,443,8080-8100 -T4 –min-hostgroup 50 –max-rtt-timeout2000 –initial-rtt-timeout 300 –max-retries 3 –host-timeout 20m–max-scan-delay 1000 -oA wapscan 10.0.0.0/8
```

#### 2.1.9 使用诱饵扫描方法来扫描主机端口

```shell
sudo nmap -sS 192.168.0.10 -D 192.168.0.2
```

#### 2.1.10 为一个子网列出反向 DNS 记录

```shell
nmap -R -sL 209.85.229.99/27 | awk ‘{if($3==”not”)print”(“$2″) no PTR”;else print$3″ is “$2}’ | grep ‘(‘
```

#### 2.1.11 显示网络上共有多少台 Linux 及 Win 设备?

```shell
sudo nmap -F -O 192.168.0.1-255 | grep “Running: ” > /tmp/os; echo “$(cat /tmp/os | grep Linux \| wc -l) Linux device(s)”; echo “$(cat /tmp/os | grep Windows | wc -l) Window(s) device”
```

#### 2.1.12

### 2.2 Nmap脚本使用

nmap脚本主要分为以下几类，在扫描时可根据需要设置--script=类别这种方式进行比较笼统的扫描：

auth: 负责处理鉴权证书（绕开鉴权）的脚本

broadcast: 在局域网内探查更多服务开启状况，如dhcp/dns/sqlserver等服务

brute: 提供暴力破解方式，针对常见的应用如http/snmp等

default: 使用-sC或-A选项扫描时候默认的脚本，提供基本脚本扫描能力

discovery: 对网络进行更多的信息，如SMB枚举、SNMP查询等

dos: 用于进行拒绝服务攻击

exploit: 利用已知的漏洞入侵系统

external: 利用第三方的数据库或资源，例如进行whois解析

fuzzer: 模糊测试的脚本，发送异常的包到目标机，探测出潜在漏洞 intrusive: 入侵性的脚本，此类脚本可能引发对方的IDS/IPS的记录或屏蔽

malware: 探测目标机是否感染了病毒、开启了后门等信息

safe: 此类与intrusive相反，属于安全性脚本

version: 负责增强服务与版本扫描（Version Detection）功能的脚本

vuln: 负责检查目标机是否有常见的漏洞（Vulnerability），如是否有MS08_067

#### 2.2.1 探测Web应用防火墙，以及防火墙指纹检测

```shell
nmap -p80,443 --script http-waf-detect --script-args="http-waf-detect.aggro,http-waf-detect.detectBodyChanges" targetWebsite.com
nmap -p80,443 --script http-waf-fingerprint targetWebsite.comStarting 
nmap -p80,443 --script http-waf-fingerprint --script-args http-waf-fingerprint.intensive=1 targetWebsiteStarting
```

#### 2.2.2 探测Web应用错误代码

```shell
nmap -p80,443 --script http-errors targetWebsite.com
```

根据维基百科，下面是五个HTTP状态代码类别的列表。Web应用程序渗透测试人员应熟悉所有状态代码及其定义。

1xx（信息）：收到请求，继续处理

2xx（成功）：请求已成功接收，理解和接受

3xx（重定向）：需要采取进一步措施才能完成请求

4xx（客户端错误）：请求包含错误的语法或无法满足

5xx（服务器错误）：服务器无法满足明显有效的请求在HTTP的错误的Nmap脚本可用于确定进一步调查有趣的状态代码。

#### 2.2.3 爬取Web应用

```shell
nmap -vv -p80,443 --script http-errors --script-args "httpspider.url=/docs/,httpspider.maxpagecount=3,httpspider.maxdepth=1" targetwebsite.com
```

#### 2.2.4 爆破子域

```shell
nmap -p80,443 --script dns-brute targetWebsite.com
nmap -p80,443 --script dns-brute --script-args dns-brute.threads=25,dns-brute.hostlist=/root/Desktop/custom-subdomain-wordlist.txt targetWebsite.com
```

#### 2.2.5 提取图片嵌入式信息EXIF

从照片中提取EXIF数据

可交换图像文件（更多称为EXIF）是以JPEG，PNG，PDF和更多文件类型存储的信息。此嵌入数据有时可以显示有趣的信息，包括时间戳，设备信息和GPS坐标。大多数网站仍然无法正确清理图像中的EXIF数据，从而使自己或用户面临风险。

作为渗透测试人员，了解目标使用何种设备将有助于我们确定要生成哪种类型的有效负载。用于捕捉黑帽子的EXIF数据的典型例子是Higinio Ochoa被捕。FBI代理使用Higinio上传到互联网的照片中的GPS数据推断出女友的地理位置。

Nmap的http-exif-spider脚本可用于从网站上的照片中提取有趣的EXIF数据。这样的脚本对Instagram，Twitter和Facebook等主流网站没用。当用户上传新照片时，主要网站会擦除EXIF数据。但是，个人博客，小型企业和企业组织可能不会采取强有力的安全预防措施或监控员工在线发布的内容。在照片中找到GPS数据并不罕见。

```shell
nmap -p80,443 --script http-exif-spider targetWebsite.com
```

尝试从大型照片中提取EXIF数据时，Nmap可能会生成一条错误消息，指出“当前的http缓存大小超过最大大小”。这是Nmap告诉我们照片太大并且超出了默认的最大文件大小值。使用http.max-cache-size参数并根据需要增加值。下面我把它设置为一个任意高的数字。

```shell
nmap -p80,443 --script http-exif-spider --script-args="http.max-cache-size=99999999" targetWebsite.com
```



## 3 感谢及Nmap资料汇总

[Hr-Papers|Nmap渗透测试脚本指南](https://www.freebuf.com/column/166679.html)

[Nmap的漏洞利用脚本初探](https://blog.csdn.net/qq_29647709/article/details/79582939)

[编写自己的Nmap脚本](https://www.cnblogs.com/liun1994/p/7041531.html)

[nmap脚本使用总结](https://www.cnblogs.com/h4ck0ne/p/5154683.html)

[nmap命令-----高级用法](https://www.cnblogs.com/nmap/p/6232969.html)

[Nmap命令的29个实用范例](https://www.cnblogs.com/hongfei/p/3801357.html)

[nmap 使用总结](https://www.cnblogs.com/zhaijiahui/p/8367327.html)

[Nmap扫描原理与用法](https://blog.csdn.net/aspirationflow/article/details/7694274)

[Nmap和Zenmap详解](https://blog.csdn.net/qq_36119192/article/details/82079150)

[nmap使用方法--方便自己查](https://blog.csdn.net/ruins44/article/details/51160069/)

[Nmap和Zenmap详解](https://blog.csdn.net/qq_36119192/article/details/82079150#nmap%C2%A0%20-PE%2F-PP%2F-PM)

[端口扫描之王——nmap入门精讲（一）](https://www.cnblogs.com/st-leslie/p/5115280.html)

[Nmap 讲解从简到难---01](https://blog.csdn.net/m0_37268841/article/details/80404613)
