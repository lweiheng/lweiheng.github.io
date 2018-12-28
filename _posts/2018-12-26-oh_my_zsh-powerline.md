---
layout: post
title: 'Linux 配置oh_my_zsh+powerline'
date: 2018-12-26
author: Jacob
cover: 'http://on2171g4d.bkt.clouddn.com/jekyll-banner.png'
tags: linux

---
















# Linux 配置oh_my_zsh+powerline







































* TOC

























































## Step 1 : 安装pip git wget curl

```shell
sudo apt-get install git python-pip wget curl
```

## Step 2 : 安装oh_my_zsh

```shell

```

```shell
$ sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

## Step 3 : 安装powerline fonts

```shell
git clone https://github.com/powerline/fonts.git
sudo ./fonts/install.sh
```

## Step 4 : 使用`fontconfig`配置Powerline 字体

```shell
wget https://github.com/Lokaltog/powerline/raw/develop/font/PowerlineSymbols.otf
mv PowerlineSymbols.otf ~/.local/share/fonts/
sudo fc-cache -f -v
wget https://github.com/Lokaltog/powerline/raw/develop/font/10-powerline-symbols.conf mkdir -p ~/.config/fontconfig/fonts.conf mv 10-powerline-symbols.conf ~/.config/fontconfig/fonts.conf/
```

## Step 5 : 更改zsh主题

```shell
sudo gedit ~/.zshrc
```

将zsh主题改为agnoster

## Step 6 : reboot

```shell
reboot
```


