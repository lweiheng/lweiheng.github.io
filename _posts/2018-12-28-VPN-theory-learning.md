---
layout: post
title: 'VPN原理学习总结'
date: 2018-12-26
author: Jacob
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: Network Protocol

---

# VPN原理学习总结

本篇为资料的整合，仅用于个人学习理解技术原理，请勿转载

* TOC
{:toc}
## 0 VPN简介

VPN的全称为Virtual Private Network，翻译为中文为虚拟专用网络虚拟专用网络的功能是：在公用网络上建立专用网络，进行加密通讯。在企业网络中有广泛应用。

VPN属于远程访问技术，简单地说就是利用公用网络架设专用网络。例如某公司员工出差到外地，他想访问企业内网的服务器资源，这种访问就属于远程访问。

由于公共IP的短缺，我们在组建局域网时，通常使用保留地址作为内部IP，（比如最常用的C类保留地址：192.168.0.0－192.168.255.255）这些地址是不会被互联网分配的，因此它们在互联网上也无法被路由的，所以在正常情况下无法直接通过Internet外网访问到在局域网内的主机。为了实现这一目的，需要使用VPN隧道技术建立一个虚拟专用网络。而这就是



## 1 VPN实现技术

VPN的实现方法有多种，VPN网关通过对数据包的加密和数据包目标地址的转换实现远程访问。VPN有多种分类方式，主要是按协议进行分类。VPN可通过服务器、硬件、软件等多种方式实现。

## 2 VPN应用场景

在传统的企业网络配置中，要进行远程访问，传统的方法是租用DDN（数字数据网）专线或帧中继，这样的通讯方案必然导致高昂的网络通讯和维护费用。对于移动用户（移动办公人员）与远端个人用户而言，一般会通过拨号线路（Internet）进入企业的局域网，但这样必然带来安全上的隐患。

让外地员工访问到内网资源，利用VPN的解决方法就是在内网中架设一台VPN服务器。外地员工在当地连上互联网后，通过互联网连接VPN服务器，然后通过VPN服务器进入企业内网。为了保证数据安全，VPN服务器和客户机之间的通讯数据都进行了加密处理。有了数据加密，就可以认为数据是在一条专用的数据链路上进行安全传输，就如同专门架设了一个专用网络一样，但实际上VPN使用的是互联网上的公用链路，因此VPN称为虚拟专用网络，其实质上就是利用加密技术在公网上封装出一个数据通讯隧道。有了VPN技术，用户无论是在外地出差还是在家中办公，只要能上互联网就能利用VPN访问内网资源，这就是VPN在企业中应用得如此广泛的原因。

## 3 VPN的隧道技术简介

1. 通常情况下，[VPN网关](https://baike.baidu.com/item/VPN%E7%BD%91%E5%85%B3)采取双网卡结构，外网卡使用公网IP接入[Internet](https://baike.baidu.com/item/Internet)。
2. 网络一(假定为公网internet)的终端A访问网络二(假定为公司内网)的终端B，其发出的访问数据包的目标地址为终端B的内部IP地址。
3. 网络一的VPN网关在接收到终端A发出的访问数据包时对其目标地址进行检查，如果目标地址属于网络二的地址，则将该数据包进行封装，封装的方式根据所采用的VPN技术不同而不同，同时VPN网关会构造一个新VPN数据包，并将封装后的原数据包作为VPN数据包的负载，VPN数据包的目标地址为网络二的VPN网关的外部地址。
4. 网络一的VPN网关将VPN数据包发送到[Internet](https://baike.baidu.com/item/Internet)，由于VPN数据包的目标地址是网络二的VPN网关的外部地址，所以该数据包将被Internet中的[路由](https://baike.baidu.com/item/%E8%B7%AF%E7%94%B1)正确地发送到网络二的VPN网关。
5. 网络二的VPN网关对接收到的数据包进行检查，如果发现该数据包是从网络一的VPN网关发出的，即可判定该数据包为VPN数据包，并对该数据包进行解包处理。解包的过程主要是先将VPN数据包的包头剥离，再将数据包反向处理还原成原始的数据包。
6. 网络二的VPN网关将还原后的原始数据包发送至目标终端B，由于原始数据包的目标地址是终端B的IP，所以该数据包能够被正确地发送到终端B。在终端B看来，它收到的数据包就和从终端A直接发过来的一样。
7. 从终端B返回终端A的数据包处理过程和上述过程一样，这样两个网络内的终端就可以相互通讯了。 [1] 

通过上述说明可以发现，在[VPN网关](https://baike.baidu.com/item/VPN%E7%BD%91%E5%85%B3)对数据包进行处理时，有两个参数对于VPN通讯十分重要：原始数据包的目标地址（VPN目标地址）和远程VPN网关地址。根据VPN目标地址，VPN网关能够判断对哪些数据包进行VPN处理，对于不需要处理的数据包通常情况下可直接转发到上级路由；远程VPN网关地址则指定了处理后的VPN数据包发送的目标地址，即VPN隧道的另一端VPN网关地址。由于网络通讯是双向的，在进行VPN通讯时，隧道两端的VPN网关都必须知道VPN目标地址和与此对应的远端VPN网关地址。

### 3.1 pptp

PPTP是对端对端协议（PPP）的一种扩展，它采用了PPP所提供的身份验证、压缩与加密机制。PPTP能够随TCP/IP协议一道自动进行安装。PPTP与Microsoft端对端加密（MPPE）技术提供了用以对保密数据进行封装与加密的VPN服务。 MPPE将通过由MS-CHAP、MS-CHAP v2身份验证过程所生成的加密密钥对PPP帧进行加密。为对PPP帧中所包含的有效数据进行加密，虚拟专用网络客户端必须使用MS-CHAP、MS-CHAP v2身份验证协议。

PPP协议：点到点协议（Point to Point Protocol，PPP）是IETF（Internet Engineering Task Force，因特网工程任务组）推出的点到点类型线路的数据链路层协议。
​    
GRE协议：GRE（generic routing encapsulation，通用路由封装）协议是对某些网络层协议的数据报进行封装，使这些被封装的数据报能够在另一个网络层协议中传输。

#### 3.1.1 控制连接和隧道维护 

​     在PPTP建立连接过程中，客户端先向服务器1723端口发送TCP连接请求，这里的TCP连接不是标准的三次握手，第三次回应的ACK会随着载荷一起发送，这样做的好处是可以节省一些网络流量，减少不必要的开销。在TCP连接完成以后，PPTP进入控制连接的建立，首先客户端会发Start Control Connection Request报文给服务端要求建立连接，服务器端接到后发送应答报文，客户端再次发送Outgoing Call Request，等到服务器端响应后控制连接建立。
接着是标准的PPP协商，上面说过，PPTP是建立在PPP的基础上的隧道协议。这样先进行LCP层的协商，客户端跟服务器端双方都要把自己的链路层配置发送给对方，客户端的配置一般比较简单，服务器端收到后就会接受 ，而服务器端发给客户端的配置报文，客户端会对Unknow选项和不接受选项向服务器反馈，服务器收到后删除这些选项再次发送配置，客户端接受，LCP协商完成。此时在PPTP的控制层会互发Set Link Info报文来对刚刚协商好的选项进行配置。
​    
LCP协商完成后，服务器开始对客户端进行身份验证，可以选的验证方式很多，有PAP、CHAP、MS-CHAP等方式，身份验证后就进行NCP层协商。主要是来确定双方网络层接口参数，配置虚拟虚拟端口，分配IP、DNS等信息。

之后PPTP连接过程就算是完成，PPTP开始发送GRE封装的数据包，但每隔60秒，连接双方会发送Echo Request来询问链路是否可用，如果对方在此后60秒内没有响应，连接就会被终止。正常的连接终止时通过发送Stop Control Connection Request来通知对方结束连接。

#### 3.1.2 实际抓取PPTP拨号交互报文

##### 3.1.2.1 Start-control-connection-request报文

由PPTP客户端发出，请求建立控制连接；



Length ：该 PPTP 信息的八位总长，包括整个 PPTP 头。

Message Type ： 信息类型。可能值有：1、控制信息；2、管理信息。

Magic Cookie ： Magic Cookie 以连续的 0x1A2B3C4D 进行发送，其基本目的是确保接收端与 TCP数据流间的正确同步运行。

Control Message Type ：值为1；

Reserved 0 & 1 ： 保留字段，必须设置为0。

Protocol Version ： PPTP版本号。

Framing Capabilities ： 指出帧类型，该信息发送方可以提供：1、异步帧支持（Asynchronous Framing Supported）；2、同步帧支持（Synchronous Framing Supported）。
Bearer Capabilities ： 指出承载性能，该信息发送方可以提供：1、模拟访问支持（Analog Access Supported）；2、数字访问支持（Digital access supported）。

Maximum Channels ： 该 PPTP服务器 可以支持的个人 PPP 会话总数。

Firmware Revision ： 若由 PPTP服务器 出发，则包括发出 PPTP服务器时的固件修订本编号；若由 PPTP客户端 出发，则包括 PPTP客户端 PPTP 驱动版本。

Host Name ： 包括发行的 PPTP服务器 或 PPTP客户端的 DNS 名称。

Vendor Name ： 包括特定供应商字串，指当请求是由 PPTP客户端 提出时，使用的 PPTP服务器 类型或 PPTP客户端软件类型。

##### 3.1.2.2 Start-Control-Connection-Reply报文

   PPTP服务器对Star-Control-Connection-Request回应；

大部分字段的含义与Start-control-connection-request一致。不同的字段含义如下：

Control Message Type ：值为2；

Result Code：表示建立channal是否成功的结果码，值为1表示成功，值为2表示通用错误，暗示着有问题。值为3表示channal已经存在，值为4表示请求者未授权，值为5表示请求的PPTP协议版本不支持。

Error Code：表示错误码，一般值为0，除非Result Code值为2，不同的错误码表示不同的含义。（即：只有当Result Code值为2时，Error Code才有其它值）

##### 3.1.2.3 Outgoing-call-request报文

  由PPTP客户机发出，请求创建PPTP隧道，该消息包含GRE报头中call id，                         

  该id可唯一地标识一条隧道 ；



Length、PPTP Message、Magic cookie与Start-control-connection-request一致。不同的字段含义如下：

Control Message Type ：值为7。

Call ID：由PPTP客户端指定的唯一的会话ID。

Call Serial Number：是由PPTP客户端指定的唯一标识符，用于在记录会话信息中标识特定会话，与Call ID不一样的是，Call Serial Number PPTP客户端与PPTP服务器来说，唯一绑定到一个给定的会话，且是相同的。

Minimum BPS：对于此次会话可接受的最低传输速度，单位为位/秒；

Maximum BPS：对于此次会话可接受的最大传输速度，单位为位/秒；

Bearer Type： 指出承载访问支持，该信息发送方可以提供：1、模拟访问支持（Analog Access Supported）；2、数字访问支持（Digital access supported）。3、可支持的任何类型。

Framing Type： 指出帧类型，该信息发送方可以提供：1、异步帧支持（Asynchronous Framing Supported）；2、同步帧支持（Synchronous Framing Supported）。 3、异步或同步帧支持。

Packet Receive window size：PPTP客户端为此次会话提供最大接收缓冲大小；

Packet  Processing Delay：表示PPTP客户端对数据包处理的延时度量，对于PPTP客户端来说，一般设置比较小越好。

Phone number length：拔号号码长度；

Phone number：建立会话向外拔号的号码，一般对于ISDN或模拟方式拔号来说，此字段域为一个ASCII串。一般长度少于64个字节。

Sub address：额外信息域，一般长度少于64个字节。

##### 3.1.2.4 Outgoing-Call-Reply报文

PPTP服务器对Outgoing-Call- Request t回应；



Length、PPTP Message、Magic cookie与Start-control-connection-request一致。不同的字段含义如下：

Control Message Type ：值为8。

Call ID：由PPTP服务器指定的唯一的会话ID。主要用于在PPTP服务器与PPTP客户端建立的会话上，复用与解封装隧道包使用的。

Peer’s Call ID：设置的值是从接收到的Outgoing-call-request中Call ID值，是由PPTP客户端指定的，用于GRE中对于隧道数据解封与复用。

Result Code：表示响应Outgoing-call-request握手是否成功，值为1表示成功，值为2表示通用错误，暗示着有问题。值为3表示无载波，值为4表示服务器忙，无法及时响应，值为5表示无拔号音，值为6表示呼号超时，值为7表示未授权。

Error Code：表示错误码，一般值为0，除非Result Code值为2，不同的错误码表示不同的含义。

Cause Code：表示进一步错误信息描述；

Connect Speed：连接使用的实际速率；

Physical Channel ID：由PPTP服务器指定的物理信道ID。

##### 3.1.2.1 Set-Link-Info报文

  由PPTP客户机或服务器任一方发出，设置PPP协商选项；



Control Message Type ：值为15。

Peer’s Call ID：设置的值是从接收到的Outgoing-call-request中Call ID值，是由PPTP客户端指定的，用于GRE中对于隧道数据解封与复用。

Reserved0/Reserve1：保留位，必须为0;

Send ACCM：发送的ACCM值，默认值为0XFFFFFFFF；

Receive ACCM：接收的ACCM值，默认值为0XFFFFFFFF；

##### 3.1.2.6 Echo request报文

PPTP隧道维护报文，每60S发送一次；

Control Message Type ：值为5。

Peer’s Call ID：设置的值是从接收到的Outgoing-call-request中Call ID值，是由PPTP客户端指定的，用于GRE中对于隧道数据解封与复用。

Reserved0：保留位，必须为0;

Identifier：发送者用来标识Echo request与Echo reply对应标识。

3.1.2.7 Echo-reply报文
PPTP隧道维护报文，对Echo request的回应报文；

Control Message Type ：值为6。

Peer’s Call ID：设置的值是从接收到的Outgoing-call-request中Call ID值，是由PPTP客户端指定的，用于GRE中对于隧道数据解封与复用。

Reserved0/1：保留位，必须为0;

Identifier：标识值，为接收者从Echo request里标识字段复制填入。

Result Code：结果码，为1表示Echo-reply是有效的，为2表示出现一般性错误。

##### 3.1.2.8 Call-Clear-Request报文

   由PPTP客户机发出，请求终止隧道；

   Control Message Type ：值为12。

Call ID：由PPTP客户端指定的会话ID。

##### 3.1.2.9 Call-Disconnect-Notify报文

   PPTP服务器对Call-Clear-Request回应或者其他原因指示必须终止隧道；

Control Message Type ：值为13。

Call ID：由PPTP客户端指定的会话ID。

Result Code：结果码，为1表示媒介断开，为2表示出现一般性错误，为3表示为管理员关闭连接，为4表示收到Call-Clear-Request；

Error code：同上面所描述的。

Cause Code：此域表示额外说明断开原因。

##### 3.1.2.10 Stop-Control-Connection-Request报文

   由PPTP客户机或者服务器任一方发出，通知对端控制连接将被终止；

Control Message Type ：值为3。

Resverve：保留位，必须为0;

Reason：表示会话连接关闭的原因，为1表示响应会话清除请求，为2表示不支持对端PPTP版本，为3表示本地系统关闭。

##### 3.1.2.11 Stop-Control-Connection-Reply报文

  回应Stop-Control-Connection-Request消息；

  Control Message Type ：值为4。

Resverve：保留位，必须为0;

Result Code：表示关闭连接结果码，为1表示正常关闭成功，为2表示发生一般性错误。

Error Code：表示当结果为2时，对应具体的一般性错误，Result Code为1时，必须为0。

#### 3.1.3 PPP协商过程

当PPTP控制通道建立完成后，发起PPP协商

##### 3.1.3.1 LCP协商

 1、Client发送一个Configure-Request，把自己的link layer configure发给Server，Client端的Configure一般比较简单，所以Server一下子就接受了，立即回了一个Configure-Ack；

  2、Server同时也必须发送一个Configure-Request，把自己的link layer configure发给Client，而这个configure包含的内容往往比较多；

  3、如果Client收到的Configure-Request中，有unknow的配置项，就会把这些项列出来发一个Configure-Reject包给Server；

   4、Server端把Client不认识的配置项删掉，再次重发Configure-Request；
5、这次Client收到的配置项全都认识了，开始检查是不是所有的配置都可以接受，如果接受的话LCP过程结束，否则把不能接受的配置项列出来发送Configure-Nak给Server；
6、Server再次修改配置项，再次发送Configure-Request； 
7、 Client发现这次的配置项全都认识，而且全都是自己可以接受的，回发Configure-Ack。   
8、Client和Server接受彼此的配置之后，PPTP的控制层会彼此互发Set Link Info；

 


##### 3.1.3.2 PPP协议的身份验证

9.  LCP协商完毕后，PPP协议的Server端会对Client端进行身份验证；
    
10. 可以选CHAP、MS-CHAP、MS-CHAP-v2...进行验证，现在以CHAP为例，需要注意到以下几点：
    

（1） Server发送Challenge，其中包括一个challenge string和server name； 
（2） Client回Response，其中包括用户名，和密码和challeng string。其中用户名以明文发送（这个需要特别注意），密码的challenge string经过单向hash算法后以密文形式发送；
（3） Server发送success，表示身份验证成功。

 


##### 3.1.3.3 PPP协议的NCP协商
11. 身份验证通过后，PPP双方会进行NCP协商，用来确定互相通信的网络层接口参数；
12. 接下来你会看到一大堆IPCP协商数据包，它是NCP基于TCP/IP的接口协商协议。Server和Client都要把自己的Miniport信息发给对方，告诉对方我以后就用它和你通信了，你同不同意请给回个话。具体过程如下：
（1） Server把自己的Miniport信息通过Configure-Request发送给Client；
（2） Client也把自己的Miniport信息通过Configure-Request发送给Server；（这个时候Client发出的配置信息是完全无效的数据，之所以要故意发无效数据，是要求Server来给自己分配IP等信息）
（3） Client接受Server的接口配置，发送Configure-Ack；
（4） Server发现Client的配置是无效的，自己给Client发送一组有效的配置信息，通过Configure-Nak发送给Client，其中主要是给他分配IP；
（5） Client根据Server端发出的Configure-Nak，提取出给自己分配的IP、DNS等信息，并设置Miniport接口；
（6） Client根据修改后的配置，再次发送Configure-Request；
（7） Server接受Client的配置，发送Configure-Ack；

##### 3.1.3.4 CCP报文

CCP中包括了MPPC和MPPE的参数协商，也就是Microsoft

Point-to-Point Compression 和 Microsoft Point-to-Point Encryption的参数协商，用来确定数据包中的压缩和加密算法和参数。

#### 3.1.4 数据传输

PPTP隧道建立完成后，使用GRE封装PPP报文在IP网络环境中传输。


### 3.2 l2tp

L2TP（Layer 2 Tunneling Protocol，二层隧道协议）是VPDN（Virtual Private Dial-up Network，虚拟私有拨号网）隧道协议的一种。

VPDN是指利用公共网络（如ISDN或PSTN）的拨号功能接入公共网络，实现虚拟专用网，从而为企业、小型ISP、移动办公人员等提供接入服务。即，VPDN为远端用户与私有企业网之间提供了一种经济而有效的点到点连接方式。

VPDN采用专用的网络通信协议，在公共网络上为企业建立安全的虚拟专网。企业驻外机构和出差人员可从远程经由公共网络，通过虚拟隧道实现和企业总部之间的网络连接，而公共网络上其它用户则无法穿过虚拟隧道访问企业网内部的资源。

#### 3.2.1  L2TP协议背景

PPP定义了一种封装技术，可以在二层的点到点链路上传输多种协议数据包，当用户与NAS之间运行PPP协议时，二层链路端点与PPP会话点驻留在相同硬件设备（NAS）上。

L2TP（RFC 2661）是一种对PPP链路层数据包进行隧道传输的技术，允许二层链路端点（LAC）和PPP会话点（LNS）驻留在通过分组交换网络连接的不同设备上，从而扩展了PPP模型，使得PPP会话可以跨越帧中继或Internet等网络。

L2TP结合了L2F和PPTP的各自优点，成为IETF有关二层隧道协议的工业标准。

#### 3.2.2 L2TP协议结构

图 2描述了控制通道以及PPP帧和数据通道之间的关系。PPP帧在不可靠的L2TP数据通道上进行传输，控制消息在可靠的L2TP控制通道内传输。

图 2 L2TP协议结构

![img](http://www.h3c.com.cn/res/200904/30/20090430_758268_image002_605932_30003_0.png)

 

图 3描述了LAC与LNS之间的L2TP数据报文的封装结构。通常L2TP数据以UDP报文的形式发送。L2TP注册了UDP 1701端口，但是这个端口仅用于初始的隧道建立过程中。L2TP隧道发起方任选一个空闲的端口（未必是1701）向接收方的1701端口发送报文；接收方收到报文后，也任选一个空闲的端口（未必是1701），给发送方的指定端口回送报文。至此，双方的端口选定，并在隧道保持连通的时间段内不再改变。

图 3 L2TP报文封装结构图

![img](http://www.h3c.com.cn/res/200904/30/20090430_758269_image003_605932_30003_0.png)

 

#### 3.2.3 隧道和会话

在一个LNS和LAC对之间存在着两种类型的连接。

l              隧道（Tunnel）连接：它对应了一个LNS和LAC对。

l              会话（Session）连接：它复用在隧道连接之上，用于表示承载在隧道连接中的每个PPP会话过程。

在同一对LAC和LNS之间可以建立多个L2TP隧道，隧道由一个控制连接和一个或多个会话连接组成。会话连接必须在隧道建立（包括身份保护、L2TP版本、帧类型、硬件传输类型等信息的交换）成功之后进行，每个会话连接对应于LAC和LNS之间的一个PPP数据流。

控制消息和PPP数据报文都在隧道上传输。L2TP使用Hello报文来检测隧道的连通性。LAC和LNS定时向对端发送Hello报文，若在一段时间内未收到Hello报文的应答，隧道断开。

#### 3.2.4 控制消息和数据消息

L2TP中存在两种消息：控制消息和数据消息。

l              控制消息用于隧道和会话连接的建立、维护以及传输控制。它的传输是可靠传输，并且支持对控制消息的流量控制和拥塞控制。

l              数据消息用于封装PPP帧，并在隧道上传输。它的传输是不可靠传输，若数据报文丢失，不予重传，不支持对数据消息的流量控制和拥塞控制。

控制消息和数据消息共享相同的报文头。L2TP报文头中包含隧道标识符（Tunnel ID）和会话标识符（Session ID）信息，用来标识不同的隧道和会话。隧道标识相同、会话标识不同的报文将被复用在一个隧道上。报文头中的隧道标识符与会话标识符由对端分配。

#### 3.2.5 两种典型的L2TP隧道模式

L2TP隧道的建立包括以下两种典型模式。

l              NAS-Intiated

如图 4所示，由LAC端（指NAS）发起L2TP隧道连接。远程系统的拨号用户通过PPPoE/ISDN拨入LAC，由LAC通过Internet向LNS发起建立隧道连接请求。拨号用户的私网地址由LNS分配（S1500的板子相当于拨号用户？不是LAC?）；对远程拨号用户的验证与计费既可由LAC侧代理完成，也可在LNS侧完成。

图 4 NAS-Initiated L2TP隧道模式

![img](http://www.h3c.com.cn/res/200904/30/20090430_758270_image004_605932_30003_0.png)

 

l              Client-Initiated

如图 5所示，直接由LAC客户（指本地支持L2TP协议的用户）发起L2TP隧道连接。LAC客户获得Internet访问权限后，可直接向LNS发起隧道连接请求，无需经过一个单独的LAC设备建立隧道。LAC客户的私网地址由LNS分配。

在Client-Initiated模式下，LAC客户需要具有公网地址，能够直接通过Internet与LNS通信。

图 5 Client-Initiated L2TP隧道模式



![img](http://www.h3c.com.cn/res/200904/30/20090430_758271_image005_605932_30003_0.png)

常用的VPN拨号上网属于第二种方式

#### 3.2.6 L2TP隧道的建立过程

L2TP应用的典型组网如图 6所示。

图 6 L2TP应用的典型组网

![img](http://www.h3c.com.cn/res/200904/30/20090430_758272_image006_605932_30003_0.png)

 

下面以NAS-Initiated模式的L2TP隧道为例，介绍L2TP的呼叫建立流程。

图 7 L2TP隧道的呼叫建立流程

![img](http://www.h3c.com.cn/res/200904/30/20090430_758273_image007_605932_30003_0.png)

 

如图 7所示，L2TP隧道的呼叫建立流程过程为：

(1)        远端系统Host发起呼叫连接请求；

(2)        Host和LAC端（RouterA）进行PPP LCP协商；

(3)        LAC对Host提供的用户信息进行PAP或CHAP认证；

(4)        LAC将认证信息（用户名、密码）发送给RADIUS服务器进行认证；

(5)        RADIUS服务器认证该用户，如果认证通过，LAC准备发起Tunnel连接请求；

(6)        LAC端向指定LNS发起Tunnel连接请求；

(7)        在需要对隧道进行认证的情况下，LAC端向指定LNS发送CHAP challenge信息，LNS回送该challenge响应消息CHAP response，并发送LNS侧的CHAP challenge，LAC返回该challenge的响应消息CHAP response；

(8)        隧道验证通过；

(9)        LAC端将用户CHAP response、response identifier和PPP协商参数传送给LNS；

(10)    LNS将接入请求信息发送给RADIUS服务器进行认证；

(11)    RADIUS服务器认证该请求信息，如果认证通过则返回响应信息；

(12)    若用户在LNS侧配置强制本端CHAP认证，则LNS对用户进行认证，发送CHAP challenge，用户侧回应CHAP response；

(13)    LNS再次将接入请求信息发送给RADIUS服务器进行认证；

(14)    RADIUS服务器认证该请求信息，如果认证通过则返回响应信息；

(15)    验证通过，LNS端会给远端用户分配一个企业网内部IP地址，用户即可以访问企业内部资源。

### 3.3 IPSec

IPSec VPN是目前VPN技术中点击率非常高的一种技术，同时提供VPN和信息加密两项技术，这一期专栏就来介绍一下IPSec VPN的原理。

#### 3.3.1 IPSec VPN应用场景

![img](http://www.h3c.com.cn/res/201005/12/20100512_977659_image001_675214_30005_0.jpg)

IPSec VPN的应用场景分为3种：

\1.      Site-to-Site（站点到站点或者网关到网关）：如弯曲评论的3个机构分布在互联网的3个不同的地方，各使用一个商务领航网关相互建立VPN隧道，企业内网（若干PC）之间的数据通过这些网关建立的IPSec隧道实现安全互联。

\2.      End-to-End（端到端或者PC到PC）： 两个PC之间的通信由两个PC之间的IPSec会话保护，而不是网关。

\3.      End-to-Site（端到站点或者PC到网关）：两个PC之间的通信由网关和异地PC之间的IPSec进行保护。

VPN只是IPSec的一种应用方式，IPSec其实是IP Security的简称，它的目的是为IP提供高安全性特性，VPN则是在实现这种安全特性的方式下产生的解决方案。IPSec是一个框架性架构，具体由两类协议组成：

\1.      AH协议（Authentication Header，使用较少）：可以同时提供数据完整性确认、数据来源确认、防重放等安全特性；AH常用摘要算法（单向Hash函数）MD5和SHA1实现该特性。

\2.      ESP协议（Encapsulated Security Payload，使用较广）：可以同时提供数据完整性确认、数据加密、防重放等安全特性；ESP通常使用DES、3DES、AES等加密算法实现数据加密，使用MD5或SHA1来实现数据完整性。

为何AH使用较少呢？因为AH无法提供数据加密，所有数据在传输时以明文传输，而ESP提供数据加密；其次AH因为提供数据来源确认（源IP地址一旦改变，AH校验失败），所以无法穿越NAT。当然，IPSec在极端的情况下可以同时使用AH和ESP实现最完整的安全特性，但是此种方案极其少见。

#### 3.3.2 IPSec封装模式

介绍完IPSec VPN的场景和IPSec协议组成，再来看一下IPSec提供的两种封装模式（传输Transport模式和隧道Tunnel模式）

![img](http://www.h3c.com.cn/res/201005/12/20100512_977660_image002_675214_30005_0.jpg)

上图是传输模式的封装结构，再来对比一下隧道模式：

![img](http://www.h3c.com.cn/res/201005/12/20100512_977661_image003_675214_30005_0.jpg)

可以发现传输模式和隧道模式的区别：

\1.      传输模式在AH、ESP处理前后IP头部保持不变，主要用于End-to-End的应用场景。

\2.      隧道模式则在AH、ESP处理之后再封装了一个外网IP头，主要用于Site-to-Site的应用场景。

从上图我们还可以验证上一节所介绍AH和ESP的差别。下图是对传输模式、隧道模式适用于何种场景的说明。

![img](http://www.h3c.com.cn/res/201005/12/20100512_977662_image004_675214_30005_0.jpg)

从这张图的对比可以看出：

\1.      隧道模式可以适用于任何场景

\2.      传输模式只能适合PC到PC的场景

隧道模式虽然可以适用于任何场景，但是隧道模式需要多一层IP头（通常为20字节长度）开销，所以在PC到PC的场景，建议还是使用传输模式。

为了使大家有个更直观的了解，我们看看下图，分析一下为何在Site-to-Site场景中只能使用隧道模式：

![img](http://www.h3c.com.cn/res/201005/12/20100512_977663_image005_675214_30005_0.jpg)

如上图所示，如果发起方内网PC发往响应方内网PC的流量满足网关的兴趣流匹配条件，发起方使用传输模式进行封装：

\1.      IPSec会话建立在发起方、响应方两个网关之间。

\2.      由于使用传输模式，所以IP头部并不会有任何变化，IP源地址是192.168.1.2，目的地址是10.1.1.2。

\3.      这个数据包发到互联网后，其命运注定是杯具的，为什么这么讲，就因为其目的地址是10.1.1.2吗？这并不是根源，根源在于互联网并不会维护企业网络的路由，所以丢弃的可能性很大。

\4.      即使数据包没有在互联网中丢弃，并且幸运地抵达了响应方网关，那么我们指望响应方网关进行解密工作吗？凭什么，的确没什么好的凭据，数据包的目的地址是内网PC的10.1.1.2，所以直接转发了事。

\5.      最杯具的是响应方内网PC收到数据包了，因为没有参与IPSec会话的协商会议，没有对应的SA，这个数据包无法解密，而被丢弃。

我们利用这个反证法，巧妙地解释了在Site-to-Site情况下不能使用传输模式的原因。并且提出了使用传输模式的充要条件：兴趣流必须完全在发起方、响应方IP地址范围内的流量。比如在图中，发起方IP地址为6.24.1.2，响应方IP地址为2.17.1.2，那么兴趣流可以是源6.24.1.2/32、目的是2.17.1.2/32，协议可以是任意的，倘若数据包的源、目的IP地址稍有不同，对不起，请使用隧道模式。

#### 3.3.3 IPSec协商

![img](http://www.h3c.com.cn/res/201005/12/20100512_977664_image006_675214_30005_0.jpg)

IPSec除了一些协议原理外，我们更关注的是协议中涉及到方案制定的内容：

\1.      兴趣流：IPSec是需要消耗资源的保护措施，并非所有流量都需要IPSec进行处理，而需要IPSec进行保护的流量就称为兴趣流，最后协商出来的兴趣流是由发起方和响应方所指定兴趣流的交集，如发起方指定兴趣流为192.168.1.0/24à10.0.0.0/8，而响应方的兴趣流为10.0.0.0/8à192.168.0.0/16，那么其交集是192.168.1.0/24ßà10.0.0.0/8，这就是最后会被IPSec所保护的兴趣流。

\2.      发起方：Initiator，IPSec会话协商的触发方，IPSec会话通常是由指定兴趣流触发协商，触发的过程通常是将数据包中的源、目的地址、协议以及源、目的端口号与提前指定的IPSec兴趣流匹配模板如ACL进行匹配，如果匹配成功则属于指定兴趣流。指定兴趣流只是用于触发协商，至于是否会被IPSec保护要看是否匹配协商兴趣流，但是在通常实施方案过程中，通常会设计成发起方指定兴趣流属于协商兴趣流。

\3.      响应方：Responder，IPSec会话协商的接收方，响应方是被动协商，响应方可以指定兴趣流，也可以不指定（完全由发起方指定）。

\4.      发起方和响应方协商的内容主要包括：双方身份的确认和密钥种子刷新周期、AH/ESP的组合方式及各自使用的算法，还包括兴趣流、封装模式等。

\5.      SA：发起方、响应方协商的结果就是曝光率很高的SA，SA通常是包括密钥及密钥生存期、算法、封装模式、发起方、响应方地址、兴趣流等内容。

我们以最常见的IPSec隧道模式为例，解释一下IPSec的协商过程：



![img](http://www.h3c.com.cn/res/201006/04/20100604_989257_img_13_675214_30005_0.gif)

上图描述了由兴趣流触发的IPSec协商流程，原生IPSec并无身份确认等协商过程，在方案上存在诸多缺陷，如无法支持发起方地址动态变化情况下的身份确认、密钥动态更新等。伴随IPSec出现的IKE（Internet Key Exchange）协议专门用来弥补这些不足：

\1.      发起方定义的兴趣流是源192.168.1.0/24目的10.0.0.0/8，所以在接口发送发起方内网PC发给响应方内网PC的数据包，能够得以匹配。

\2.      满足兴趣流条件，在转发接口上检查SA不存在、过期或不可用，都会进行协商，否则使用当前SA对数据包进行处理。

\3.      协商的过程通常分为两个阶段，第一阶段是为第二阶段服务，第二阶段是真正的为兴趣流服务的SA，两个阶段协商的侧重有所不同，第一阶段主要确认双方身份的正确性，第二阶段则是为兴趣流创建一个指定的安全套件，其最显著的结果就是第二阶段中的兴趣流在会话中是密文。

IPSec中安全性还体现在第二阶段SA永远是单向的：

![img](http://www.h3c.com.cn/res/201005/12/20100512_977666_image008_675214_30005_0.jpg)

从上图可以发现，在协商第二阶段SA时，SA是分方向性的，发起方到响应方所用SA和响应放到发起方SA是单独协商的，这样做的好处在于即使某个方向的SA被破解并不会波及到另一个方向的SA。这种设计类似于双向车道设计。

IPSec虽然只是5个字母的排列组合，但其所涉及的协议功能众多、方案又极其灵活，本期主要介绍IPSec的基本原理，在后续专栏还会继续介绍IPSec的其它方面知识。

## 4 VPN与proxy的区别

### 4.1 proxy技术简介


科学上网的工具很多，八仙过海，各显神通，而且综合了各种技术。尝试从以下四个方面来解析一些其中的原理。大致先原理，再工具的顺序。

- dns
- http/https proxy
- vpn
- socks proxy

一个http请求发生了什么？

这个是个比较流行的面试题，从中可以引出很多的内容。大致分为下面四个步骤：

- dns解析，得到IP
- 向目标IP发起TCP请求
- 发送http request
- 服务器回应，浏览器解析

还有很多细节，更多参考：

<http://fex.baidu.com/blog/2014/05/what-happen/>

<http://stackoverflow.com/questions/2092527/what-happens-when-you-type-in-a-url-in-browser>

<http://div.io/topic/609?page=1> 从FE的角度上再看输入url后都发生了什么

#### 4.1.1 DNS/域名解析

可以看到dns解析是最初的一步，也是最重要的一步。比如访问亲友，要知道他的正确的住址，才能正确地上门拜访。

dns有两种协议，一种是UDP（默认），一种是TCP。

##### udp 方式，先回应的数据包被当做有效数据

在linux下可以用dig来检测dns。国内的DNS服务器通常不会返回正常的结果。
下面以google的8.8.8.8 dns服务器来做测试，并用wireshark来抓包，分析结果。

```
`dig @8.8.8.8  www.youtube.com`
```

[![dns-udp-youtube](https://hengyunabc.github.io/img/dns-udp-youtube-badresponse-fast.png)](https://hengyunabc.github.io/img/dns-udp-youtube-badresponse-fast.png)dns-udp-youtube

从wireshark的结果，可以看到返回了三个结果，**前面两个是错误的，后面的是正确的**。

但是，对于dns客户端来说，它只会取最快回应的的结果，后面的正确结果被丢弃掉了。**因为中间被插入了污染包，所以即使我们配置了正确的dns服务器，也解析不到正确的IP。**

##### tcp 方式，有时有效，可能被rest

再用TCP下的DNS来测试下:

```
`dig @8.8.8.8 +tcp   www.youtube.com`
```

[![dns-tcp-youtube-reset](https://hengyunabc.github.io/img/dns-tcp-youtube-reset.png)](https://hengyunabc.github.io/img/dns-tcp-youtube-reset.png)dns-tcp-youtube-reset

从wireshark的结果，可以看出在TCP三次握手成功时，本地发出了一个查询www.youtube.com的dns请求，结果，很快收到了一个RST回应。而RST回应是在TCP连接断开时，才会发出的。所以可以看出，**TCP通讯受到了干扰，DNS客户端因为收到RST回应，认为对方断开了连接，因此也无法收到后面正确的回应数据包了。**

再来看下解析twitter的结果：

```
`dig @8.8.8.8 +tcp  www.twitter.com`
```

结果：

```
`www.twitter.com.        590     IN      CNAME   twitter.com. twitter.com.            20      IN      A       199.59.150.7 80 twitter.com.            20      IN      A       199.59.150.7 twitter.com.            20      IN      A       199.59.149.230 twitter.com.            20      IN      A       199.59.150.39`
```

这次返回的IP是正确的。但是尝试用telnet 去连接时，会发现连接不上。

```
`telnet 199.59.150.7 80`
```

但是，在国外服务器去连接时，可以正常连接，完成一个http请求。**可见一些IP的访问被禁止了**。

```
`$ telnet 199.59.150.7 80 Trying 199.59.150.7... Connected to 199.59.150.7. Escape character is '^]'. GET / HTTP/1.0 HOST:www.twitter.com  HTTP/1.0 301 Moved Permanently content-length: 0 date: Sun, 08 Feb 2015 06:28:08 UTC location: https://www.twitter.com/ server: tsa_a set-cookie: guest_id=v1%3A142337688883648506; Domain=.twitter.com; Path=/; Expires=Tue, 07-Feb-2017 06:28:08 UTC x-connection-hash: 0f5eab0ea2d6309109f15447e1da6b13 x-response-time: 2`
```

##### 黑名单/白名单

想要获取到正确的IP，自然的黑名单/白名单两种思路。

下面列出一些相关的项目：

```
`https://github.com/holmium/dnsforwarder https://code.google.com/p/huhamhire-hosts/ https://github.com/felixonmars/dnsmasq-china-list`
```

##### 本地DNS软件

- 修改hosts文件
  相信大家都很熟悉，也有一些工具可以自动更新hosts文件的。
- 浏览器pac文件
  主流浏览器或者其插件，都可以配置pac文件。pac文件实际上是一个JS文件，可以通过编程的方式来控制dns解析结果。其效果类似hosts文件，不过pac文件通常都是由插件控制自动更新的。只能控制浏览器的dns解析。
- 本地dns服务器，dnsmasq
  在linux下，可以自己配置一个dnsmasq服务器，然后自己管理dns。不过比较高级，也比较麻烦。

顺便提一下，实际上，kubuntu的NetworkManager会自己启动一个私有的dnsmasq进程来做dns解析。不过它侦听的是127.0.1.1，所以并不会造成冲突。

```
`/usr/sbin/dnsmasq --no-resolv --keep-in-foreground --no-hosts --bind-interfaces --pid-file=/run/sendsigs.omit.d/network-manager.dnsmasq.pid --listen-address=127.0.1.1 --conf-file=/var/run/NetworkManager/dnsmasq.conf`
```

##### 路由器智能DNS

基于OpenWRT/Tomoto的路由器可以在上面配置dns server，从而实现在路由器级别智能dns解析。现在国内的一些路由器是基于OpenWRT的，因此支持配置dns服务器。
参考项目：

```
`https://github.com/clowwindy/ChinaDNS`
```

#### 4.1.2 http proxy

##### 4.1.2.1 http proxy请求和没有proxy的请求的区别

在chrome里没有设置http proxy的请求头信息是这样的：

```
`GET /nocache/fesplg/s.gif Host: www.baidu.com`
```

在设置了http proxy之后，发送的请求头是这样的：

```
`GET http://www.baidu.com//nocache/fesplg/s.gif Host: www.baidu.com Proxy-Connection: keep-alive`
```

区别是配置http proxy之后，会在请求里发送完整的url。

client在发送请求时，如果没有proxy，则直接发送path，如果有proxy，则要发送完整的url。

实际上http proxy server可以处理两种情况，即使客户端没有发送完整的url，因为host字段里，已经有host信息了。

为什么请求里要有完整的url？

历史原因。

##### 4.1.2.2 目标服务器能否感知到http proxy的存在？

当我们使用http proxy时，有个问题可能会关心的：目标服务器能否感知到http proxy的存在？

一个配置了proxy的浏览器请求头：

```
`GET http://55.75.138.79:9999/ HTTP/1.1 Host: 55.75.138.79:9999 Proxy-Connection: keep-alive`
```

实际上目标服务器接收到的信息是这样子的：

```
`GET / HTTP/1.1 Host: 55.75.138.79:9999 Connection: keep-alive`
```

可见，http proxy服务器并没有把proxy相关信息发送到目标服务器上。

因此，目标服务器是没有办法知道用户是否使用了http proxy。

##### 4.1.2.3 http proxy keep-alive

实际上Proxy-Connection: keep-alive这个请求头是错误的，不在标准里：

因为http1.1 默认就是Connection: keep-alive

如果client想要http proxy在请求之后关闭connection，可以用Proxy-Connection: close 来指明。

<http://homepage.ntlworld.com/jonathan.deboynepollard/FGA/web-proxy-connection-header.html>

#### 4.1.2.4 http proxy authentication

当http proxy需要密码时：

第一次请求没有密码，则会回应

```
`HTTP/1.1 407 Proxy authentication required Proxy-Authenticate: Basic realm="Polipo"`
```

浏览器会弹出窗口，要求输入密码。
如果密码错误的话，回应头是：

```
`HTTP/1.1 407 Proxy authentication incorrect`
```

如果是配置了密码，发送的请求头则是：

```
`GET http://www.baidu.com/ HTTP/1.1 Host: www.baidu.com Proxy-Connection: keep-alive Proxy-Authorization: Basic YWRtaW46YWRtaW4=`
```

Proxy-Authorization实际是Base64编码。

```
`base64("admin:admin") == "YWRtaW46YWRtaW4="`
```

##### 4.1.2.5 http proxy对于不认识的header和方法的处理：

http proxy通常会尽量原样发送，因为很多程序都扩展了http method，如果不支持，很多程序都不能正常工作。

客户端用OPTIONS 请求可以探测服务器支持的方法。但是意义不大。

#### 4.1.3 https proxy

当访问一个https网站时，[https://github.com](https://github.com/)

先发送connect method，如果支持，会返回200

```
`CONNECT github.com:443 HTTP/1.1 Host: github.com Proxy-Connection: keep-alive User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/39.0.2171.95 Safari/537.36   HTTP/1.1 200 OK`
```

##### http tunnel

<http://en.wikipedia.org/wiki/HTTP_tunnel#HTTP_CONNECT_tunneling>

通过connect method，http proxy server实际上充当tcp转发的中间人。
比如，用nc 通过http proxy来连42端口：

```
`$ nc -x10.2.3.4:8080 -Xconnect host.example.com 42`
```

原理是利用CONNECT方法，让http proxy服务器充当中间人。

#### 4.1.4 https proxy的安全性？

proxy server可以拿到什么信息？

通过一个http proxy去访问支付宝是否安全？

- 可以知道host，即要访问的是哪个网站
- 拿不到url信息
- https协议保证不会泄露通信内容
- TLS(Transport Layer Security) 在握手时，生成强度足够的随机数
- TLS 每一个record都要有一个sequence number，每发一个增加一个，并且是不能翻转的。
- TLS 保证不会出现重放攻击

TLS的内容很多，这里说到关于安全的一些关键点。

注意事项：

- 确保是https访问
- 确保访问网站的证书没有问题

是否真的安全了？更强的攻击者！

流量劫持 —— 浮层登录框的隐患

<http://fex.baidu.com/blog/2014/06/danger-behind-popup-login-dialog/>

**所以，尽量不要使用来路不明的http/https proxy，使用公开的wifi也要小心。**

#### 4.1.5 goagent工作原理

- local http/https proxy
- 伪造https证书，导入浏览器信任列表里
- 浏览器配置http/https proxy
- 解析出http/https request的内容。然后把这些请求内容打包，发给GAE服务器
- 与GAE通信通过http/https，内容用RC4算法加密
- GAE服务器，再调用google提供的 urlfetch，来获得请求的回应，然后再把回应打包，返回给客户端。
- 客户端把回应传给浏览器
- 自带dns解析服务器
- 在local/certs/ 目录下可以找到缓存的伪造的证书

fiddler抓取https数据包是同样原理。

goagent会为每一个https网站伪造一个证书，并缓存起来。比如下面这个github的证书：
[![goagent-github-cert.png](https://hengyunabc.github.io/img/goagent-github-cert.png)](https://hengyunabc.github.io/img/goagent-github-cert.png)goagent-github-cert.png

goagent的代码在3.0之后，支持了很多其它功能，变得有点混乱了。

以3.2.0 版本为例：

主要的代码是在server/gae/gae.py 里。
<https://github.com/goagent/goagent/blob/v3.2.0/server/gae/gae.py#L107>

一些代码实现的细节：

- 支持最长的url是2083，因为gae sdk的限制。
  <https://github.com/AppScale/gae_sdk/blob/master/google/appengine/api/taskqueue/taskqueue.py#L241>
- 如果回应的内容是/text, json, javascript，且 > 512会用gzip来压缩
- 处理一些Content-Range 的回应内容。Content-Range 的代码虽然只有一点点，但是如果是不熟悉的话，要花上不少工夫。
- goagent的生成证书的代码在 local/proxylib.py的这个函数里：

```
`@staticmethoddef _get_cert(commonname, sans=()):`
```

##### 4.1.5.1 为什么goagent可以看视频？

因为很多网站都是http协议的。有少部分是rmtp协议的，也有是rmtp over http的。

在youku看视频的一个请求数据：

```
`http://14.152.72.22/youku/65748B784A636820C5A81B41C7/030002090454919F64A167032DBBC7EE242548-46C9-EB9D-916D-D8BA8D5159D3.flv?&start=158 response： Connection:close Content-Length:7883513 Content-Type:video/x-flv Date:Wed, 17 Dec 2014 17:55:24 GMT ETag:"316284225" Last-Modified:Wed, 17 Dec 2014 15:21:26 GMT Server:YOUKU.GZ`
```

可以看到，有ETag，有长度信息等。

##### 4.1.5.1 goagent缺点

- 只是http proxy，不能代理其它协议
- google的IP经常失效
- 不支持websocket协议
- 配置复杂

#### 4.1.6 vpn

##### 4.1.6.1 流行的vpn类型

- PPTP，linux pptpd，安全性低，不能保证数据完整性或者来源，MPPE加密暴力破解
- L2TP，linux xl2tpd，预共享密钥可以保证安全性
- SSTP，基于HTTPS，微软提出。linux开源实现SoftEther VPN
- OPENVPN，基于SSL，预共享密钥可以保证安全性
- 所谓的SSL VPN，各家厂商有自己的实现，没有统一的标准
- 新型的staless VPN，像sigmavpn/ShadowVPN等

现状：

- PPTP/L2TP 可用，但可能会不管用
- SoftEther VPN/OPENVPN 可能会导致服务器被封IP，连不上，慎用
- ShadowVPN可用，sigmavpn没有测试

猜测下为什么PPTP，L2TP这些方案容易被检测到？

可能是因为它们的协议都有明显的标头：

- 转发的是ppp协议数据，握手有特征
- PPTP协议有GRE标头和PPP标头
- L2TP有L2TP标头和PPP标头
- L2TP要用到IPsec

参考：

[https://technet.microsoft.com/zh-cn/library/cc771298(v=ws.10).aspx](https://technet.microsoft.com/zh-cn/library/cc771298%28v=ws.10%29.aspx)

##### 4.1.6.2 网页版的SSL VPN

有些企业，或者学校里，会有这种VPN：

- 网页登陆帐号
- 设置IE代理，为远程服务器地址
- 通过代理浏览内部网页

这种SSL VPN原理很简单，就是一个登陆验证的http proxy，其实并不能算是VPN？

##### 4.1.6.3 新型的staless vpnVPN，sigmavpn/ShadowVPN

这种新型VPN的原理是，利用虚拟的网络设备TUN和TAP，把请求数据先发给虚拟设备，然后把数据加密转发到远程服务器。（VPN都这原理？）

```
`you <-> local <-> protocol <-> remote <-> ...... <-> remote <-> protocol <-> local <-> peer`
```

这种新型VPN的特点是很轻量，没有传统VPN那么复杂的握手加密控制等，而向个人，而非企业。SigmaVPN号称只有几百行代码。

参考：

<http://zh.wikipedia.org/wiki/TUN%E4%B8%8ETAP>

<https://code.google.com/p/sigmavpn/wiki/Introduction>

##### 4.1.6.4 ubuntu pptp vpn server安装

ubuntu官方参考文档：
<https://help.ubuntu.com/community/PPTPServer>

- vps 要开启ppp和nat网络转发的功能

- **设置MTU，建议设置为1200以下**，因为中间网络可能很复杂，MTU太大可能导致连接失败

  `iptables -A FORWARD -p tcp --syn -s 192.168.0.0/24 -j TCPMSS --set-mss 1200`

#### 4.1.7 socks proxy

- rfc 文档： <http://tools.ietf.org/html/rfc1928>
- wiki上的简介： <http://en.wikipedia.org/wiki/SOCKS#SOCKS5>
- socks4/socks4a 已经过时
- socks5

socks5支持udp，所以如果客户端把dns查询也走socks的话，那么就可以直接解决dns的问题了。

##### 4.1.7.1 socks proxy 握手的过程

socks5流程

- 客户端查询服务器支持的认证方式
- 服务器回应支持的认证方式
- 客户端发送认证信息，服务器回应
- 如果通过，客户端直接发送TCP/UDP的原始数据，以后proxy只单纯转发数据流，不做任何处理了
- **socks proxy 自身没有加密机制，简单的TCP/UDP forward**

socks协议其实是相当简单的，用wireshark抓包，结合netty-codec-socks，很容易可以理解其工作过程。
<https://github.com/netty/netty/tree/master/codec-socks>

##### 4.1.7.2 ssh socks proxy

如果有一个外国的服务器，可以通过ssh连接登陆，那么可以很简单地搭建一个本地的socks5代理。

XShell可以通过“转移规则”来配置本地socks服务器，putty也有类似的配置：
[![xshell-sock5-proxy.png](https://hengyunabc.github.io/img/xshell-sock5-proxy.png)](https://hengyunabc.github.io/img/xshell-sock5-proxy.png)xshell-sock5-proxy.png

linux下命令行启动一个本地sock5服务器：

```
`ssh -D 1080 user@romoteHost`
```

ssh还有一些端口转发的技巧，这对于测试网络程序，绕过防火墙也是很有帮助的。

参考：<http://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/>

##### 4.1.7.3 shadowsocks的工作原理

shadowsocks是非常流行的一个代理工具，其原理非常简单。

- 客户端服务器预共享密码
- 本地socks5 proxy server（有没有想起在学校时用的ccproxy？）
- 软件/浏览器配置本地socks代理
- 本地socks server把数据包装，AES256加密，发送到远程服务器
- 远程服务器解密，转发给对应的服务器

```
`app => local socks server(encrypt) => shadowsocks server(decrypt) => real host                                                                         app <= (decrypt) local socks server <= (encrypt) shadowsocks server <= real host`
```

其它的一些东东：

- 一个端口一个密码，没有用户的概念
- 支持多个workder并发
- 协议简单，比socks协议还要简单，抽取了socks协议的部分

##### 4.1.7.4 shadowsoks的优点

- 中间没有任何握手的环节，直接是TCP数据流
- 速度快

##### 4.1.7.5 shadowsocks的安全性

- 服务器可以解出所有的TCP/UDP数据
- 中间人攻击，重放攻击

所以，**对于第三方shadow socks服务器，要慎重使用。**

在使用shadowsocks的情况下，https通迅是安全的，但是仍然有危险，参见上面http proxy安全的内容。

##### 4.1.7.6 vpn和socks代理的区别

从原理上来说，socks代理会更快，因为转发的数据更少。

因为vpn转发的是ppp数据包，ppp协议是数据链路层(data link layer)的协议。socks转发的是TCP/UDP数据，是传输(transport)层。

VPN的优点是很容易配置成全局的，这对于很多不能配置代理的程序来说很方便。而配置全局的socks proxy比较麻烦，目前貌似还没有简单的方案。

##### 4.1.7.7 linux下一些软件配置代理的方法

- bash/shell

对于shell，最简单的办法是在命令的前面设置下http_porxy的环境变量。

```
`http_proxy=http://127.0.0.1:8123 wget http://test.com`
```

推荐的做法是在~/.bashrc 文件里设置两个命令，开关http proxy：

```
`alias proxyOn='export https_proxy=http://127.0.0.1:8123 && http_proxy=http://127.0.0.1:8123'alias proxyOff='unset https_proxy && unset  http_proxy'`
```

注意，如果想sudo的情况下，http proxy仍然有效，要配置env_keep。

在/etc/sudoers.d/目录下增加一个env_keep的文件，内容是：

```
`Defaults env_keep += " http_proxy https_proxy ftp_proxy "`
```

参考：
<https://help.ubuntu.com/community/AptGet/Howto#Setting_up_apt-get_to_use_a_http-proxy>

- GUI软件

现在大部分软件都可以设置代理。
gnome和kde都可以设置全局的代理。

##### 4.1.7.8 linux下不支持代理的程序使用socks代理：tsocks

tsocks利用LD_PRELOAD机制，代理程序里的connect函数，然后就可以代理所有的TCP请求了。
不过dns请求，默认是通过udp来发送的，所以tsocks不能代理dns请求。

默认情况下，tsocks会先加载~/.tsocks.conf，如果没有，再加载/etc/tsocks.conf。对于local ip不会代理。

使用方法：

```
`sudo apt-get install tsocksLD_PRELOAD=/usr/lib/libtsocks.so wget http://www.facebook.com`
```



基于路由器的方案有很多，原理和本机的方案是一样的，只不过把这些措施前移到路由器里。

路由器的方案的优点是很明显的：

- 手机/平板不用设置
- 公司/局域网级的代理

但是需要专门的路由器，刷固件等。

shadowsocks, shadowvpn都可以跑在路由器上。

一些项目收集：

<https://github.com/lifetyper/FreeRouter_V2>

<https://gist.github.com/wen-long/8644243>

<https://github.com/ashi009/bestroutetb>



## 5 感谢

[L2TP技术原理](https://blog.csdn.net/xiaoshengqdlg/article/details/37879177)

[PPTP协议工作原理](https://blog.csdn.net/leehomkey/article/details/61924540)

[proxy解析](https://www.cnblogs.com/UnGeek/p/5828045.html)