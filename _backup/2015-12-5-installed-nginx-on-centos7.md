---
layout: post
title: Centos7下Nginx的安装与配置
categories: [general, Nginx]
tags: [Linux, CentOS, Nginx]
description: 本篇文章讲述centos7下Nginx的安装与配置
comments: true
---

本篇文章讲述centos7下Nginx的安装与配置。

# 安装准备

首先由于nginx的一些模块依赖一些lib库，所以在安装nginx之前，必须先安装这些lib库，这些依赖库主要有g++、gcc、openssl-devel、pcre-devel和zlib-devel 所以执行如下命令安装

{% highlight bash %}
$ yum install gcc-c++  
$ yum install pcre pcre-devel  
$ yum install zlib zlib-devel  
$ yum install openssl openssl--devel  
{% endhighlight bash %}

安装之前，最好检查一下是否已经安装有nginx
{% highlight bash %}
find -name nginx
{% endhighlight bash %}
如果系统已经安装了nginx，那么就先卸载
{% highlight bash %}
yum remove nginx
{% endhighlight bash %}
# 第一种方式：通过yum安装

直接通过 yum install nginx 肯定是不行的,因为yum没有nginx，所以首先把 nginx 的源加入 yum 中。

运行下面的命令:
{% highlight bash %}
[root@localhost local]# rpm -ivh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
获取http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
警告：/var/tmp/rpm-tmp.9t6wd7: 头V4 RSA/SHA1 Signature, 密钥 ID 7bd9bf62: NOKEY
准备中...                          ################################# [100%]
正在升级/安装...
   1:nginx-release-centos-7-0.el7.ngx ################################# [100%]
[root@localhost local]# yum info nginx
已加载插件：fastestmirror
nginx                                                                                                                                                       | 2.9 kB  00:00:00
nginx/x86_64/primary_db                                                                                                                                     |  21 kB  00:00:01
Loading mirror speeds from cached hostfile
 * base: mirrors.163.com
 * extras: mirrors.163.com
 * updates: mirrors.163.com
可安装的软件包
名称    ：nginx
架构    ：x86_64
时期       ：1
版本    ：1.10.3
发布    ：1.el7.ngx
大小    ：673 k
源    ：nginx/x86_64
简介    ： High performance web server
网址    ：http://nginx.org/
协议    ： 2-clause BSD-like license
描述    ： nginx [engine x] is an HTTP and reverse proxy server, as well as
         : a mail proxy server.

[root@localhost local]# yum install nginx
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.163.com
 * extras: mirrors.163.com
 * updates: mirrors.163.com
正在解决依赖关系
--> 正在检查事务
---> 软件包 nginx.x86_64.1.1.10.3-1.el7.ngx 将被 安装
--> 解决依赖关系完成

依赖关系解决

===========================================================================
 Package        架构          版本                  源                   大小
===========================================================================
正在安装:
 nginx        x86_64     1:1.10.3-1.el7.ngx       nginx               673 k

事务概要
===========================================================================
安装  1 软件包

总下载量：673 k
安装大小：2.3 M
Is this ok [y/d/N]: y
Downloading packages:
nginx-1.10.3-1.el7.ngx.x86_64.rpm                                                                                                                           | 673 kB  00:00:10
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
警告：RPM 数据库已被非 yum 程序修改。
  正在安装    : 1:nginx-1.10.3-1.el7.ngx.x86_64                     1/1
----------------------------------------------------------------------

Thanks for using nginx!

Please find the official documentation for nginx here:
* http://nginx.org/en/docs/

Commercial subscriptions for nginx are available on:
* http://nginx.com/products/

----------------------------------------------------------------------
  验证中      : 1:nginx-1.10.3-1.el7.ngx.x86_64                     1/1

已安装:
  nginx.x86_64 1:1.10.3-1.el7.ngx

完毕！
[root@localhost local]# service nginx start
Redirecting to /bin/systemctl start  nginx.service
{% endhighlight bash %}

## 可能遇到的问题:
具体情况如下  1。本机能ping通虚拟机  2。虚拟机也能ping通本机  3。虚拟机能访问自己的web  4。本机无法访问虚拟己的web  这个问题的原因是服务器的80端口没有打开或防火墙没有关闭

## 解决办法:
{% highlight bash %}
如果是centos6:
解决方法如下：
/sbin/iptables -I INPUT -p tcp --dport 80 -j ACCEPT
然后保存：
/etc/rc.d/init.d/iptables save
重启防火墙
/etc/init.d/iptables restart

CentOS防火墙的关闭，关闭其服务即可：
查看CentOS防火墙信息：/etc/init.d/iptables status
关闭CentOS防火墙服务：/etc/init.d/iptables stop
永久关闭防火墙：
chkconfig –level 35 iptables off

如果是centos7
[root@rhel7 ~]# systemctl status firewalld.service
[root@rhel7 ~]# systemctl stop firewalld.service
[root@rhel7 ~]# systemctl disable firewalld.service
[root@rhel7 ~]# systemctl status firewalld.service
{% endhighlight bash %}

## 扩展知识：
{% highlight bash %}
启动一个服务：systemctl start firewalld.service
关闭一个服务：systemctl stop firewalld.service
重启一个服务：systemctl restart firewalld.service
显示一个服务的状态：systemctl status firewalld.service
在开机时启用一个服务：systemctl enable firewalld.service
在开机时禁用一个服务：systemctl disable firewalld.service
查看服务是否开机启动：systemctl is-enabled firewalld.service;echo $?
查看已启动的服务列表：systemctl list-unit-files|grep enabled
{% endhighlight bash %}

# 第二种方式：通过手动下载安装包解压安装

{% highlight bash %}
#首先进入/usr/local目录
cd /usr/local
#从官网下载最新版的nginx
wget -c http://nginx.org/download/nginx-1.11.13.tar.gz
#解压nginx压缩包
tar -zxvf nginx-1.11.13.tar.gz
cd  nginx-1.11.13
# 接下来安装，使用--prefix参数指定nginx安装的目录,make、make install安装，默认安装在/usr/local/nginx，安装完毕后，进入安装后目录（/usr/local/nginx）便可以启动或停止它了。
./configure   
make  
make install
{% endhighlight bash %}
