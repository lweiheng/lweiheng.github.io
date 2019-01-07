---
layout: post
title: 'CentOS 7 安装 metasploit-framework'
date: 2019-1-1
author: Jacob
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: tools

---

# CentOS 7 安装 metasploit-framework

* TOC
{:toc}

## 0 远程登录CentOS 7

笔者使用SecureCRT登录申请好的VPS，其他的远程登录软件同样可以，此处使用SecureCRT是因为它的强大(-_-)

## 1 一键安装metasploit-framework

```shell
 
apt-get install curl,wget
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall && \
  chmod 755 msfinstall && \
  ./msfinstall
```

成功安装后请运行下属代码，以使下载的最新版本的msf连接数据库。

```shell
adduser msf
su msf
./msfconsole
```

PS.在运行时请cd到msfconsole所在目录，应在"/opt/metasploit-framework/bin"

初次运行msf会创建数据库，但是msf默认使用的PostgreSQL数据库不能与root用户关联，这也这也就是需要新建用户msf来运行metasploit的原因所在。如果你一不小心手一抖，初次运行是在root用户下，请使用以下命令，然后使用非root用户初始化数据库。

```shell
msfdb reinit
```

