---
layout: post
title: Mac让iTerm2记住用户名密码expect脚本
categories: [shell, Mac]
tags: [Mac]
description: Mac让iTerm2记住用户名密码expect脚本
comments: true
---

## 前言

使用iTerm2的时候，总是要一遍遍的敲用户名、密码。 我在想， 能不能像Windows的软件一样，可以直接让软件记住。然后只要点击一下，就直接ssh到远程服务器上面去了。现在可以用expect脚本实现。

{% highlight bash %}
#!/usr/bin/expect

set timeout 30
spawn ssh -p [lindex $argv 0] [lindex $argv 1]@[lindex $argv 2]
expect {
        "(yes/no)?"
        {send "yes\n";exp_continue}
        "password:"
        {send "[lindex $argv 3]\n"}
}
interact
{% endhighlight bash %}

这里[lindex $argv 0]， [lindex $argv 1]， [lindex $argv 2]， [lindex $argv 3] 分别代表着4个参数。将这个文件 item2login.sh 复制到 /usr/local/bin 就可以了。 然后在iTerm2里面配置。

如图1：
![如图1]({{ site.BASE_PATH }}/assets/media/WX20170328-135505@2x.png)

然后看一条具体的实例
![如图1]({{ site.BASE_PATH }}/assets/media/WX20170328-135834@2x.png)

{% highlight bash %}
item2login.sh 22 xxxx 192.168.0.101 xxxxxx
{% endhighlight bash %}

脚本 端口号 用户名 服务器地址 密码 一定要一一对应
文章来源：[Mac 让 iTerm2 记住用户名密码 expect 脚本](https://codingstyle.cn/topics/31)
