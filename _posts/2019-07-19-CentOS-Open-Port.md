---
layout: post
title: CentOS 7开放端口
categories: [shell, Linux]
tags: [Linux]
description: CentOS 7对外开放端口
comments: true
---

# 开放端口

## 添加对外开放的端口(16010)
{% highlight bash %}
[rolyer@localhost hbase-2.2.0]$ sudo firewall-cmd --zone=public --add-port=16010/tcp --permanent
[sudo] password for rolyer: 
success
{% endhighlight bash %}
注：返回success为成功。
命令含义：
1. --zone 作用域
2. --add-port=8080/tcp 添加端口，格式为：端口/通讯协议
3. --permanent 永久生效

## 重启防火墙
{% highlight bash %}
[rolyer@localhost hbase-2.2.0]$ systemctl restart firewalld.service
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: root
Password: 
==== AUTHENTICATION COMPLETE ===
{% endhighlight bash %}

## 查看已经对外开放的端口
{% highlight bash %}
[rolyer@localhost hbase-2.2.0]$ sudo firewall-cmd --list-ports
[sudo] password for rolyer: 
16010/tcp 16030/tcp
{% endhighlight bash %}

## 其他相关命令
1. 关闭防火墙

{% highlight bash %}
systemctl stop firewalld.service
{% endhighlight bash %}

2. 查看防火墙状态

{% highlight bash %}
systemctl status firewalld.service
{% endhighlight bash %}

3. 查看监听(Listen)的端口

{% highlight bash %}
netstat -lntp
{% endhighlight bash %}

4. 检查端口被哪个进程占用

{% highlight bash %}
netstat -lnp|grep 16010
{% endhighlight bash %}

5. 删除端口配置
{% highlight bash %}
firewall-cmd --zone= public--remove-port=16030/tcp --permanent
{% endhighlight bash %}