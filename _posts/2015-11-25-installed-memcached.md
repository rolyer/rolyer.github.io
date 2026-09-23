---
layout: post
title: Memcached安装
categories: [general, Memcached]
tags: [Memcached]
description: Memcached安装介绍
comments: true
---

# Windows下安装Memcached

## 安装步骤

* 软件包下载 [memcached-win64-1.4.4-14](http://pan.baidu.com/s/1kTm3HT9)(提取码：341u),解压到指定目录,如：C:\servers\memcached

* 用cmd打开命令窗口，转到解压的目录，输入 “memcached -d install”,查看是否安装成功，输入memcached –h，出现下图窗口说明已经安装成功。

{% highlight bash %}
memcached -d install
memcached –h
{% endhighlight %}

	如果在没有安装过的情况下，出现"failed to install service or service already installed"错误，可能是cmd.exe需要用管理员身份运行。

* 启动服务，执行memcached -d start

{% highlight bash %}
memcached -d start
{% endhighlight %}

## 参数介绍

* -p 监听的端口 
* -l 连接的IP地址, 默认是本机 
* -d start 启动memcached服务 
* -d restart 重起memcached服务 
* -d stop|shutdown 关闭正在运行的memcached服务 
* -d install 安装memcached服务 
* -d uninstall 卸载memcached服务 
* -u 以的身份运行 (仅在以root运行的时候有效) 
* -m 最大内存使用，单位MB。默认64MB 
* -M 内存耗尽时返回错误，而不是删除项 
* -c 最大同时连接数，默认是1024 
* -f 块大小增长因子，默认是1.25 
* -n 最小分配空间，key+value+flags默认是48 
* -h 显示帮助

## 修改参数

windows下需要通过修改注册表信息进行设置，打开注册表，找HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\memcached在其中有一个“ImagePath”项，值为： "C:\servers\memcached\memcached.exe" -d runservice 在后面加上“-m 1024 -c 2048 -p 11210”，等即可。
重启服务后生效。