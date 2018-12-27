

# 使用 ubuntu 18.04 远程部署VCSA6.5到ESXi（后附vCenter for windows 5.5，6.0安装指南）

@[toc]
**转载请注明出处**
## 0 VCSA简介



VCSA是VMware vSphere vCenter server的安装套件，其本质是一个深度定制的Linux系统，6.5版本的VCSA只能通过远程安装的方式安装到一个已经安装好ESXi系统的机器上，成为ESXi的一个虚拟机。VCSA6.5的部署方式分为两种，一种为命令行进行部署，一种为使用可视化界面进行部署。在使用可视化界面进行部署时，VCSA6.5支持linux x64 ， mac ， windows 三种操作系统。其详细介绍见官网
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095256628.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


## 1 VCSA下载

可以从[官网](https://my.vmware.com/cn/web/vmware/evalcenter?p=vsphere-eval)下载安装最新版本的VCSA，此处下载了VCSA6.5

> VMware-VCSA-all-6.5.0-8024368.iso

## 2 安装前准备

在安装VCSA之前，首先要了解安装过程中所用到的机器的地址，以及安装完成后的最终状态。

在本次安装中，我们使用安装ubuntu 18.04 x64操作系统的主机挂载VCSA进行远程安装。

使用的挂载iso命令如下：

```shell
sudo mkdir /media/iso
sudo mount -o loop XXX.iso /media/iso
```

在安装之前相关硬件IP地址如下：

ubuntu 18.04 x64 10.10.18.222

ESXi 10.10.18.4

设置VCSA ip为192.168.30.165



安装完成后VCSA（vCenter）表现为ESXi下的一个虚拟机 。



PS.笔者在实践过程中首先使用的是安装有Windows Server 2012 R2 操作系统的虚拟机进行安装，由于一些未知原因在第一阶段的部署中出错，所以使用安装有ubuntu 18.04 x64的物理机进行远程安装，因此第一阶段的教程的相关截图为Windows Server 2012 R2中的截图。

## 3 VCSA安装

VCSA的安装可以划分为三个阶段（官方分为两个阶段），其中第一阶段用户远程部署VCSA虚拟机，第二个阶段远程配置vCenter server 服务，本文中第三阶段为登录管理页面。

### 3.1 第一阶段

#### 3.1.1 挂载运行installer

挂载VCSA安装程序VMware-VCSA-all-6.5.0-8024368.iso

打开查看iso文件，运行installer。

PS.ubuntu 下使用如下命令运行安装程序

> ```tcl
> cd /media/iso
> cd ./vcsa-ui-installer/lin64
> sudo ./installer
> ```
>
>
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095351484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


PS.此处使用Windows Server 2012 R2 进行远程安装，所以选择Win32下的 installer。

#### 3.1.2 安装介绍

以下为安装介绍截图，next即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095430373.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.1.3 软件使用协议

以下为软件使用协议，Accept and next
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095454615.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.1.4 选择部署方式

以下为部署方式选择，如果部署虚拟机数目不大，使用嵌入式即PSC即可。此处我们使用PSC，选择第一个然后next。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095516996.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.1.5 设置ESXi IP等相关信息

vCenter与ESXi的关系通俗的来讲是：“虚拟机管理主机”。ESXi为主机，vCenter为安装其上的主机。因此在远程安装vCenter时需要与ESXi进行通信。设置好相关信息后next。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095537202.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


此时会发现，出现了证书认证的警告，yes即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095557313.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.1.6 填写vCenter虚拟机相关信息

在此处填写vCenter虚拟机的名称和密码，请注意是虚拟机的名称和密码。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095615407.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.1.7 选择管理规模和存储大小

此处依据之后管理的硬件情况进行选择即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095650480.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.1.8 指定vCenter虚拟机在ESXi上的存储地点

指定存储vCenter虚拟机的硬盘，建议此处选择精简磁盘模式即Thin Disk Mode，这样使用的存储空间会动态变化，提高资源利用率。
![在这里插入图片描述](https://img-blog.csdnimg.cn/2018122709571025.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.1.9 配置网络设置

在配置网络设置时，需要确定System name，如果在所搭建的硬件环境中存在DNS服务，此处可以输入一个网址，并在DNS服务器中添加解析。（如果拥有公网IP，可以通过DNS服务商购买域名）如果不准备使用DNS服务，可以直接输入IP地址。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095725471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.1.10 显示第一阶段安装的相关设置
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095742135.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.1.11 开始部署
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095759958.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2018122709581673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


等待进度条完成后即可。

### 3.2 第二阶段

#### 3.2.1 配置vCenter Server

使用与VCSA同一网段的虚拟主机，访问VCSA进行部署。点击Set up vCenter Server Appliance开始安装。

PS.点击后需要输入在第一阶段设置的VCSA root用户密码（密码在3.1.5配置的）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095831369.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2018122709584536.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.2.2 查看简介

简单了解后next 即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/2018122709585715.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.2.3 查看VCSA配置

相关配置均在第一阶段完成，这里可以查询和更改。

在此处，将同步设置为与ESXi同步

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095915762.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227102326981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.2.4 进行SSO配置

此处使用默认的域名和网站地址，这里设置远程管理的用户名密码。

PS.远程管理默认用户名为administrator
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095928808.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.2.5 询问是否加入CEIP

其实就是询问是否加入VMware的提高用户体验项目，与其他软件相同，会要求提供相关信息。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095942525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.2.6 开始部署前审查

审查配置，无误后点击Finish即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227095959822.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.2.7 开始安装
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100012404.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100028339.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.2.8 安装完成
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100041862.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


### 3.3 第三阶段

#### 3.3.1 跳转管理页面

点击在第二阶段完成时提示的页面此处为https://vCenter.vsphere.local:443出现如下图所示界面，点击HTML5管理页面即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100055392.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.3.2 登录管理页面

点击后出现如下界面，输入之前设置的vsphere client 用户名密码即可。PS.用户名默认为administrator@vsphere.local（在3.2.4中配置了administrator密码）
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100123356.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 3.3.3 登录后页面

登录后界面如下图
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100155992.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)






## 4 使用相应版本的ESXi进行验证

添加新的ESXi，验证vCenter安装情况

### 4.1 新建数据中心

#### 4.1.1 鼠标右键vCenter650.vsphere.local，点击 New Datacenter
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100210223.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 4.1.2 输入数据中心名称

此处建立Test数据中心
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100244947.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100302902.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


### 4.2 添加ESXi主机

#### 4.2.1 开始添加主机

鼠标右键点击Test数据中心，选择“Add Host...”，开始添加ESXi主机
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100316183.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 4.2.2 输入ESXi主机IP

输入IP，NEXT即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100329738.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 4.2.3 设置ESXi主机用户密码

输入ESXi主机用户密码，NEXT即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100342777.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


出现认证界面，YES即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100359387.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 4.2.4 主机信息

查看主机信息，无误后NEXT即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100414741.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 4.2.5 认证证书

NEXT即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100428428.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 4.2.6 禁用锁定模式

选择disable，NEXT即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100441187.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 4.2.7 选择主机所在数据中心位置

此处只有Test数据中心，同时数据中心下属没有文件夹，所以直接挂在数据中心根目录下即可。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100453424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 4.2.8 部署前信息汇总

无误后点击FINISH即可
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100512420.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)


#### 4.2.9 ESXi主机添加成功

等待任务进度完成后，ESXi主机即成功添加
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227100527933.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)



## 5 vCenter for Windows 5.5,6.0安装指南
### 5.1 vCenter for Windows 5.5
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227102747398.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)
### 5.2  vCenter for Windows 6.0
![在这里插入图片描述](https://img-blog.csdnimg.cn/20181227102800923.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxMzEwNjg5,size_16,color_FFFFFF,t_70)
