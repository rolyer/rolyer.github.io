---
layout: post
title: IntelliJ IDEA 14 + Maven + Jetty 实现热部署项目
categories: [general, Nginx]
tags: [Linux, Nginx]
description: IntelliJ IDEA下通过Maven、Jetty实现热部署项目的方法
comments: true
---

# 前言

在Maven下测试/调试时，我们可以使用Tomcat、Jetty、Jboss。使用Jetty Plugin的时候不用敲打包、部署，然后再启动服务器的指令，只需敲一句：mvn jetty:run，而且当我们修改资源文件、Java代码时，Jetty 能自动扫描到并及时给予反馈进行重加载，这对修改java文件很有帮助，不用每次修改java文件都要重启服务器，省掉了不少没必要浪费的时间。

**需要注意的是：在IntelliJ IDEA，由于没有自动编译，所以修改不会生效，需要按Ctrl+Shift+F9(修改多个文件按Ctrl+F9)编译一下。**

热部署的好处：代码修改后，不必重新启动Web服务器。


# 步骤

- 在pom.xml(Web工程的pom.xml)文件中配置jetty插件的参数：scanIntervalSeconds，配置pom.xml片断如下：

{% highlight bash %}
<build>
	<plugins>
		<plugin>
			<groupId>org.mortbay.jetty</groupId>
			<artifactId>maven-jetty-plugin</artifactId>
			<version>6.1.26</version>
			<configuration>
				<webAppSourceDirectory>${project.basedir}/src/main/webapp</webAppSourceDirectory>
				<connectors>
					<connector implementation="org.mortbay.jetty.nio.SelectChannelConnector">
						<port>8000</port>
					</connector>
				</connectors>
				<webAppConfig>
					<contextPath>/web</contextPath>
				</webAppConfig>
				 <!-- 每5秒的间隔扫描一次,实现热部署 -->
				<scanIntervalSeconds>5</scanIntervalSeconds>
			</configuration>
		</plugin>
	</plugins>
</build>
{% endhighlight bash %}

- 执行mvn jetty:run运行项目（DEBUG使用：mvnDebug jetty:run）

使用“-Plocal”指定profile

{% highlight bash %}
cd steel-cbms\cbms-web
mvn jetty:run -Plocal
{% endhighlight bash %}

- 修改Java代码好后Ctrl+Shift+F9编译代码。如果是项目的配置jetty plugin能够自动检测到，并自动重新加载配置文件。
我们将控制台看到“[INFO] Reload Mechanic: automatic”信息：
{% highlight bash %}
[INFO] Webapp source directory = C:\dev\gtxh\steel-cbms\cbms-web\src\main\webapp
[INFO] Reload Mechanic: automatic
[INFO] Classes = C:\dev\gtxh\steel-cbms\cbms-web\target\classes
[INFO] Context path = /web
[INFO] Tmp directory =  determined at runtime
[INFO] Web defaults = org/mortbay/jetty/webapp/webdefault.xml
[INFO] Web overrides =  none
[INFO] web.xml file = C:\dev\gtxh\steel-cbms\cbms-web\src\main\webapp\WEB-INF\web.xml
[INFO] Webapp directory = C:\dev\gtxh\steel-cbms\cbms-web\src\main\webapp
[INFO] No Transaction manager found - if your webapp requires one, please configure one.
[INFO] Initializing Spring root WebApplicationContext
{% endhighlight bash %}

> 如果相关依赖包下载不到，请使用VPN。

参考：[jetty-maven-plugin](http://www.eclipse.org/jetty/documentation/current/jetty-maven-plugin.html)