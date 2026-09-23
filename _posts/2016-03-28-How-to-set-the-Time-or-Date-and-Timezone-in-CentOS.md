---
layout: post
title: How to set the Time / Date and Timezone in CentOS
categories: [Linux]
tags: [CentOS]
description: CentOS上如何设置时间和时区。
comments: true
---

## 一、时区
显示时区
date --help 获取帮助
date -R
date +%z
上面两个命令都可
{% highlight bash %}
[root@localhost ~]# date -R; date +%z  
Fri, 28 Mar 2016 09:16:56 +0800
+0800
{% endhighlight bash %}

修改时区
{% highlight bash %}
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime  
{% endhighlight bash %}
时区的信息存在/usr/share/zoneinfo/下面，本机的时区信息存在/etc/localtime，利用tab键技巧，可以任意修改时区tzselect，互动式命令，不过用了好象不太行，还是用上面的吧。

## 二、时间
概念：Linux时间有两个
系统时间：也叫软件时间(sys)， 1970年1月1日到当前时间的秒数
BOIS时间：也叫硬件时间(hc)
显示时间
{% highlight bash %}
[root@localhost ~]# date;hwclock -r  
2016年 03月 28日 星期五 09:17:59 CST
2016年03月28日 星期五 09时18分01秒  -1.029707 秒
{% endhighlight bash %}

### 设置时间
1. date -s
{% highlight bash %}
date -s 20160328  
date -s 23:40:00
{% endhighlight bash %}

没有网络的情况下可以用这个
### 2、ntpdate
{% highlight bash %}
ntpdate time.nist.gov && hwclock -w  
{% endhighlight bash %}
连网更新时间，如果成功，将系统时间，写入BOIS
hwclock -w 或 hwclock --systohc
可以做到crontab里

### 3、启动ntpd服务，开启后2就不能用了。
先用ntpdate更新一下，确保时间不至于差别太大
{% highlight bash %}
rpm -qa | grep ntp #查询一下可安装了
chkconfig --list | grep ntp #看下服务情况
chkconifg ntpd on
service ntpd start 或/etc/init.d/ntpd start
{% endhighlight bash %}
必要的话，设置一下/etc/ntp.conf，再把服务reload一下。
ntp的知识参考一下鸟哥的服务器篇
