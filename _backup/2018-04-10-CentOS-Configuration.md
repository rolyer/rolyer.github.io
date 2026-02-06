---
layout: post
title: CentOS 7初始化配置
categories: [shell, Linux]
tags: [Linux]
description: CentOS 7 Server mini 系统初始化配置
comments: true
---

# 网络配置

## 查看信息
{% highlight bash %}
ip link show
{% endhighlight bash %}

## 配置网络
编辑网络配置文件vi /etc/sysconfig/network-scripts/ifcfg-ens33后重启网络
{% highlight bash %}
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
#注释下面-行
#BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens33
UUID=b0d28265-67ed-417b-8988-b3bf35f7b0d1
DEVICE=ens33
#注ONBOOT=on修改为ONBOOT=yes
ONBOOT=yes

#增加如下配置
IPADDR0=192.168.1.183
PREFIX0=24
GATEWAY0=192.168.1.1
DNS1=192.168.1.1
{% endhighlight bash %}

# 开启SSH服务

## 查看是否已安装openssh-server
{% highlight bash %}
[root@localhost ~]# yum list installed | grep openssh-server
openssh-server.x86_64                 7.4p1-16.el7                     @anaconda
{% endhighlight bash %}
上面显示已经安装了，若没有安装运行yum install openssh-server进行安装。

## 编辑sshd服务配置文件
编辑/etc/ssh/sshd_config
1. 去除关于监听端口、监听地址前的 # 号

{% highlight bash %}
Port 22
ListenAddress 0.0.0.0
ListenAddress ::
{% endhighlight bash %}

2. 开启允许远程登录

{% highlight bash %}
PermitRootLogin yes
{% endhighlight bash %}

3. 开启使用用户名密码来作为连接验证

{% highlight bash %}
PasswordAuthentication yes
{% endhighlight bash %}

4. 开启sshd服务
{% highlight bash %}
[root@localhost ~]# sudo service sshd start
Redirecting to /bin/systemctl start sshd.service
{% endhighlight bash %}

检查sshd服务是否已经开启
{% highlight bash %}
[root@localhost ~]# ps -e | grep sshd
  6769 ?        00:00:00 sshd
 19714 ?        00:00:00 sshd
{% endhighlight bash %}

检查22号端口是否开启监听
{% highlight bash %}
[root@localhost ~]# netstat -an | grep 22
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN     
tcp        0      0 192.168.1.183:22        192.168.1.125:53975     ESTABLISHED
tcp6       0      0 :::22                   :::*                    LISTEN   
{% endhighlight bash %}