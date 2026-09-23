---
layout: post
title: ubuntu下Nginx的安装与配置
categories: [general, Nginx]
tags: [Linux, Nginx]
description: 本篇文章讲述Nginx的安装与配置，安装环境是Ubuntu系统
comments: true
---

本篇文章讲述Nginx的安装与配置，安装环境是Ubuntu系统

# 1.Niginx的介绍

Nginx (“engine x”) 是一个高性能的 HTTP 和 反向代理 服务器，也是一个 IMAP/POP3/SMTP 服务器。 Nginx 是由 Igor Sysoev 为俄罗斯访问量第二的 Rambler.ru 站点开发的，第一个公开版本0.1.0发布于2004年10月4日。其将源代码以类BSD许可证的形式发布，因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。2011年6月1日，nginx 1.0.4发布78。
Nginx是一款轻量级的Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，并在一个BSD-like 协议下发行。由俄罗斯的程序设计师Igor Sysoev所开发，供俄国大型的入口网站及搜索引擎Rambler（俄文：Рамблер）使用。其特点是占有内存少，并发能力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户有：百度、新浪、网易、腾讯等。

Nginx是一个非常轻量级的HTTP服务器，Nginx，它的发音为“engine X”， 是一个高性能的HTTP

# 2.Niginx的优缺点

优点：Nginx 可以在大多数 Unix like OS 上编译运行，并有 Windows 移植版。 Nginx 的1.4.0稳定版已经于2013年4月24日发布，一般情况下，对于新建站点，建议使用最新稳定版作为生产版本，已有站点的升级急迫性不高。Nginx 的源代码使用 2-clause BSD-like license。
Nginx 是一个很强大的高性能Web和反向代理服务器，它具有很多非常优越的特性：
在高连接并发的情况下，Nginx是Apache服务器不错的替代品：Nginx在美国是做虚拟主机生意的老板们经常选择的软件平台之一。能够支持高达 50,000 个并发连接数的响应，感谢Nginx为我们选择了 epoll and kqueue作为开发模型。

缺点:Niginx的模块相对Apache的bug比较多，Nginx的rewrite没有Apache强大。Apache也相对比Nginx稳定。

# 3.对PHP支持

目前各种web 服务器对PHP的支持一共有三种：

（1）通过web 服务器内置的模块来实现，例如Apache的mod_php5，类似的Apache内置的mod_perl可以对perl支持。

（2）通过CGI来实现，这个就好比之前perl的CGI，该种方式的缺点是性能差，因为每次服务器遇到这些脚本都需要重新启动脚本解析器来执行脚本然后将结果返回给服务器；另一方面就是不太安全；该方面几乎很少使用了。

（3）最新出现一种叫做FastCGI。所谓FastCGI就是对CGI的改进。它一般采用C/S结构，一般脚本处理器会启动一个或者多个daemon进程，每次web 服务器遇到脚本的时候，直接交付给FastCGI的进程来执行，然后将得到的结果(通常为html)返回给浏览器。

# 4.Apache+mod_php模式

默认情况下我们使用的是Apache+mod_php模式，Apache对PHP的支持是通过Apache的模块来支持的。

如果从源代码编译安装PHP的话，如果希望Apache支持PHP的话，在./configure步骤需要指定–with-apxs2=/usr/local/apache2/bin/apxs 表示告诉编译器通过Apache的mod_php5/apxs来提供对PHP5的解析；而且在最后一步make install的时候我们会看到将动态链接库libphp5.so拷贝到apache2的安装目录的modules目录下，并且还需要在httpd.conf配置文件中添加LoadModule语句来动态将libphp5.so模块加载进来，从而实现Apache对php的支持。

# 5.Nginx+FastCGI模式

Nginx完全是轻量级的，必须借助第三方的FastCGI处理器才可以对PHP进行解析，因此其实这样看来Nginx是非常灵活的，它可以和任何第三方提供解析的处理器实现连接从而实现对PHP的解析(在nginx.conf中很容易设置)。

Nginx可以使用spwan-fcgi。在早期版本中需要安装lighttpd，但是在9.10版本以后直接安装spawn-fcgi就可以。现在出现了新的第三方的PHP的FastCGI处理器，叫做PHP-FPM，可以了解一下。

# 6.安装FastCGI基于spawn-fcgi

/usr/bin/spawn-fcgi这个文件来管理 FastCGI，它原属于lighttpd这个包里面，但 9.10 后，spawn-fcgi被分离出来单独成包。
（1）使用apt-get在线安装命令如下：

{% highlight bash %}
sudo apt-get install spawn-fcgi
{% endhighlight bash %}

（2）源代码安装如下，下载地址为：
http://www.lighttpd.net/download/spawn-fcgi-1.6.3.tar.gz
解压缩以后，进入目录下执行如下安装命令：

{% highlight bash %}
./configure
make
make install
{% endhighlight bash %}

安装之后，spawn-fcgi命令就可以直接使用了，它的可执行文件在/usr/local/bin/spawn-fcgi。

# 7.安装FastCGI基于php-fpm

PHP-FPM是一个PHP FastCGI管理器，是只用于PHP的,可以在 http://php-fpm.org/download下载得到.
PHP-FPM其实是PHP源代码的一个补丁，旨在将FastCGI进程管理整合进PHP包中。必须将它patch到你的PHP源代码中，在编译安装PHP后才可以使用。
新版PHP已经集成php-fpm了，不再是第三方的包了，推荐使用。PHP-FPM提供了更好的PHP进程管理方式，可以有效控制内存和进程、可以平滑重载PHP配置，比spawn-fcgi具有更多优点，所以被PHP官方收录了。

安装php及php常用插件

{% highlight bash %}
sudo apt-get install php5-cgi php-apc php5-curl php5-gd php5-mysql php5-mcrypt php5-memcache php5-memcached
{% endhighlight bash %}

安装Php-fpm

{% highlight bash %}
sudo apt-get install php5-fpm
{% endhighlight bash %}

查看php5运行进程

{% highlight bash %}
ps -waux | grep php5
{% endhighlight bash %}

打开关闭php5进程

{% highlight bash %}
sudo service php5-fpm stop
sudo service php5-fpm start
sudo service php5-fpm restart
sudo service php5-fpm status
{% endhighlight bash %}

{% highlight bash %}
配置php5监听端口  /etc/php5/fpm/pool.d/www.conf

把
listen = /var/run/php5-fpm.sock  改为
listen = 127.0.0.1:9000

重新运行php进程
{% endhighlight bash %}

# 8.安装Nginx

{% highlight bash %}
apt-get install nginx
{% endhighlight bash %}

启动Nginx服务

{% highlight bash %}
apt-get install nginx
{% endhighlight bash %}

Nginx相关配置：
请参考文章[Nginx配置及说明](https://rolyer.github.io/general/nginx/2015/12/6/nginx-configuration.html)

