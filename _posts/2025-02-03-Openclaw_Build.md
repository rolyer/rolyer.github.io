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

> **提示**：启动容器前先在HyperAI中映射端口18789、18791、18792

1. 创建容器
    > 导航：算力容器 -> 模型训练 -> 创建新容器
2. 启动容器，映射端口（18789、18791、18792）
3. ssh登录，开始下面的搭建

## 搭建步骤

### 1. 更新并安装必要软件
{% highlight bash %}
[root@xxx /hyperai/home]$ apt update && apt install -y git supervisor dnsutils
{% endhighlight %}

#### 1.1 启用supervisor（解决容器中supervisor命令报错问题）
{% highlight bash %}
systemctl is-enabled supervisor 2>/dev/null || echo 'unknown_state'
supervisord -c /etc/supervisor/supervisord.conf && sleep 1 || echo 'supervisord_failed'
supervisorctl status
{% endhighlight %}

### 2. 安装nvm（或者直接安装nodejs 25）
{% highlight bash %}
[root@xxx /hyperai/home]$ curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
{% endhighlight %}

#### 2.1 nvm安装后不识别nvm命令的话执行：
{% highlight bash %}
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" # This loads nvm bash_completion
{% endhighlight %}

### 3. 使用nvm安装node 25（使用node 25解决TG启用时配网错误）
{% highlight bash %}
nvm install 25
# 切换为v25
nvm use 25
{% endhighlight %}

### 4. 安装openclaw（官方脚本使用的是node 22，启用TG会报错）
{% highlight bash %}
curl -fsSL https://openclaw.ai/install.sh | bash

# 以下在openclaw安装完成后执行
## 加载环境变量
source /root/.bashrc
## 开发访问
sed -i 's/loopback/lan/g' ~/.openclaw/openclaw.json
## 拷贝长记忆
cp openclaw-backup/202602022030/workspace/MEMORY.md ~/.openclaw/workspace/
{% endhighlight %}

### 5. 编写supervisor配置文件（解决容器中不支持systemd命令）
{% highlight bash %}
echo "[program:openclaw]
command=openclaw gateway run
environment=PATH=\"/root/.nvm/versions/node/v25.5.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin\"
autorestart=true
stdout_logfile=/var/log/openclaw.out.log
stderr_logfile=/var/log/openclaw.err.log" | tee /etc/supervisor/conf.d/openclaw.conf
{% endhighlight %}

### 6. 重新加载supervisor配置，启动openclaw
{% highlight bash %}
supervisorctl reread
supervisorctl update
supervisorctl restart openclaw
supervisorctl status openclaw
{% endhighlight %}

### 7. 查看状态
{% highlight bash %}
supervisorctl status
ss -tulpn | grep :18789
# ss -tulpn | grep :18791
# ss -tulpn | grep :18792
{% endhighlight %}

### 8. 获取访问令牌

使用vim ~/.openclaw/openclaw.json找到TOKEN（节点位置：gateway->auth->token），再在浏览器中打开：

{% highlight bash %}
https://YOUR_URL/?token=92d833ea31f371debaeefa67c7acd15f48d3dd9f97e044bb
{% endhighlight %}

### 9. 设备授权

#### 9.1 查看Pending Request
{% highlight bash %}
openclaw devices list
{% endhighlight %}

#### 9.2 Approve Pending Request
{% highlight bash %}
openclaw devices approve xxxxxxx-xxxxx-xxxxx-xxxxxxx
{% endhighlight %}

### 10. TG配对

Code在TG机器人对话中获取（如何创建TG机器人网上搜下）。

{% highlight bash %}
openclaw pairing approve telegram xxxxxxxx
{% endhighlight %}

## 常见问题

### TG网络不稳，增加信任列表

**问题表现**：日志中出现 `Proxy headers detected from untrusted address. Connection will not be treated as local. Configure gateway.trustedProxies to restore local client detection behind your proxy.`

**解决方法**：执行以下命令添加可信代理配置

{% highlight bash %}
gateway -- action=config.patch raw='{"gateway": {"trustedProxies": ["127.0.0.1", "::1", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16"]}}'
{% endhighlight %}

这个命令的作用是：
- 向现有的配置中添加`trustedProxies`字段
- 包含了常见的本地和私有网络IP段
- 使用`config.patch`只会修改指定的部分，而不影响其他配置
- 配置后OpenClaw会自动重启以应用新配置

**可信代理IP段含义**：
- `127.0.0.1` - 本地回环地址
- `::1` - IPv6本地回环地址
- `10.0.0.0/8` - 私有网络A类地址范围
- `172.16.0.0/12` - 私有网络B类地址范围
- `192.168.0.0/16` - 私有网络C类地址范围

### 网络连接问题

**问题表现**：日志中出现 `fetch failed` 或 `Network request for 'sendChatAction' failed!`

**解决方法**：应用网络修复配置

{% highlight bash %}
gateway -- action=config.patch raw='{"web": {"enabled": true, "heartbeatSeconds": 30, "reconnect": {"initialMs": 1000, "maxMs": 30000, "factor": 2, "jitter": 0.1, "maxAttempts": 5}}, "env": {"shellEnv": {"enabled": true, "timeoutMs": 30000}}}'
{% endhighlight %}

这个配置的作用：
- 启用web模块
- 设置心跳间隔为30秒
- 配置重连机制（指数退避算法）
  - 初始重连间隔：1秒
  - 最大重连间隔：30秒
  - 重连倍数因子：2
  - 抖动系数：0.1（防止同步重连风暴）
  - 最大重试次数：5次
- 设置shell环境超时为30秒

### DNS解析问题

如果遇到DNS解析问题，可以执行：

{% highlight bash %}
apt-get update && apt-get install -y dnsutils
nslookup api.telegram.org
{% endhighlight %}