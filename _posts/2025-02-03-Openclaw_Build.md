---
layout: post
title: 零成本搭建Openclaw指南
categories: [shell, Linux]
tags: [Linux]
description: 零成本搭建Openclaw指南，人人都能有自己的Openclaw
comments: true
---

# 零成本搭建Openclaw指南

利用HyperAI免费的算力容器、Qwen搭建自己的Openclaw。

## 准备工作

注册在[HyperAI](https://hyper.ai/)账号后创建容器。

1. 创建容器
    > 导航：算力容器 -> 模型训练 -> 创建新容器
2. 启动容器,映射端口
3. ssh登录，开始下面的搭建

## 搭建

## 1. 更新并安装必要软件
{% highlight bash %}
[root@xxx /hyperai/home]$ apt update && apt install -y git supervisor dnsutils
{% endhighlight bash %}

### 1.1 启用supervisor（解决容器中supervisor命令报错问题）
{% highlight bash %}
systemctl is-enabled supervisor 2>/dev/null || echo 'unknown_state'
supervisord -c /etc/supervisor/supervisord.conf && sleep 1 || echo 'supervisord_failed'
supervisorctl status
{% endhighlight bash %}

## 2. 安装nvm（或者直接安装nodejs 25）
{% highlight bash %}
[root@xxx /hyperai/home]$ curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
{% endhighlight bash %}