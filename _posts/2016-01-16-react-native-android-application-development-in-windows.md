---
layout: post
title: 在Windows下搭建React Native Android开发环境
categories: [general, Android]
tags: [React Native, Android]
description: 本文将讲述如何在Windows下搭建React Native Android开发环境
comments: true
---

# 安装JDK

从[Java官网之JDK下载列表](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载JDK并安装。请注意选择x86还是x64版本。将JDK的bin目录加入到了系统PATH环境变量。注意：下载链接不能直接使用，需要先接受协议（这里有存入cookies），可以通过[Java官网之JDK下载列表](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载JDK。

设置环境变量PATH：jdk的位置。例如：（PATH => C:\Program Files\Java\jdk1.8.0_11）

# 安装Android SDK

单独安装Android SDK，在墙的环境下，为了速度我选择了使用[AndroidDevTools](http://androiddevtools.cn/)。

设置环境变量ANDROID_HOME：Android SDK Manager的位置 例如：（PATH => C:\Program Files\Android SDK Tools）

设置环境变量PATH：例如：（PATH => %ANDROID_HOME%\tools;%ANDROID_HOME%\platform-tools）

注：创建相应模拟器后面需求运行并安装APK。Windows下使用cygwin。

# 安装React-native-cli

{% highlight bash %}
npm install -g react-native-cli
{% endhighlight bash %}

# 初始化项目

{% highlight bash %}
react-native init reactNative
{% endhighlight bash %}
> 输出信息：
{% highlight bash %}
This will walk you through creating a new React Native project in C:\dev\react
Installing react-native package from npm...
Setting up new React Native app in C:\dev\react
To run your app on iOS:
   Open C:\dev\react\ios\react.xcodeproj in Xcode
   Hit the Run button
To run your app on Android:
   Have an Android emulator running (quickest way to get started), or a device connected
   cd C:\dev\react
   react-native run-android
{% endhighlight bash %}

# 运行packager(启动Server)

{% highlight bash %}
npm start
{% endhighlight bash %}
> 或
{% highlight bash %}
react-native start
{% endhighlight bash %}

可以用浏览器访问http://localhost:8081/index.android.bundle?platform=android看看是否可以看到打包后的脚本


# 在Android中运行

{% highlight bash %}
react-native run-android
{% endhighlight bash %}

App运行成功截屏如下：

![Welcome to React Native]({{ site.baseurl }}/assets/media/Welcome-to-React-Native-Screenshots.jpg)


# 安卓调试

目前Windows下无法自动打开chrome进行调试，所以手动打开chrome，访问如下地址：http://localhost:8081/debugger-ui 即可。

# react-native打包

## 原理

> 我们的debug程序中，js代码是调试时，不断从server加载到移动端的。但是发布成独立apk时，总不能让app启动的时候远程加载js代码吧。RN的做法是将所有js打成一个bundle文件，作为android的资源，放在assets目录下面。而assets下的文件是会在安装时，随工程一并安装到移动端本地的。这样apk安装好后，RN会负责去加载。

## 步骤

1. 将js代码导成资源

目前android版本需要手动去做。参考[issues](https://github.com/facebook/react-native/issues/2743#issuecomment-140697340)。相信Facebook后面的版本会马上会发布自动导资源的命令。实际现在也挺简单：当react-native的server启动后。把`http://localhost:8081/index.android.bundle?platform=android`这个地址的js拷出来即可。

* cd to the project directory

* Start the react-native packager if not started

* Download the bundle to the asset folder: 
  > `curl "http://localhost:8081/index.android.bundle?platform=android" -o "android/app/src/main/assets/index.android.bundle"`

2. 生成签名apk

   参考[官网教程](https://facebook.github.io/react-native/docs/signed-apk-android.html#content)。这里不用android studio，纯命令行就可以，官网教程很详细。最后生成的apk在`android/app/build/outputs`下面。


# 错误记录

> 错误1：No connected devices!

{% highlight bash %}
FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':app:installDebug'.
> com.android.builder.testing.api.DeviceException: No connected devices!

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug                                                                      option to get more log output.

BUILD FAILED
{% endhighlight bash %}

解决方法：开启AVD模拟器

> 错误2：Watcher took too long to load

{% highlight bash %}
React packager ready.

 ERROR  Watcher took too long to load
Try running `watchman version` from your terminal
https://facebook.github.io/watchman/docs/troubleshooting.html
Error: Watcher took too long to load
Try running `watchman version` from your terminal
https://facebook.github.io/watchman/docs/troubleshooting.html
    at [object Object]._onTimeout (index.js:103:16)
    at Timer.listOnTimeout (timers.js:92:15)

See http://facebook.github.io/react-native/docs/troubleshooting.html
for common problems and solutions.

{% endhighlight bash %}

解决方法：如果你碰到了ERROR Watcher took too long to load的报错，请尝试将这个文件中的MAX_WAIT_TIME值改得更大一些 (文件的具体路径是node_modules\react-native\packager\react-packager\src\DependencyResolver\FileWatcher\index.js或node_modules/react-native/packager/react-packager/src/FileWatcher/index.js)。[参考](http://reactnative.cn/docs/linux-windows-support.html#content)
