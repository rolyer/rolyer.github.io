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
> 模型选择Qwen

{% highlight bash %}
curl -fsSL https://openclaw.ai/install.sh | bash

🦞 OpenClaw 2026.2.1 (ed4529e) — Shell yeah—I'm here to pinch the toil and leave you the glory.

(node:4858) [DEP0040] DeprecationWarning: The `punycode` module is deprecated. Please use a userland alternative instead.
(Use `node --trace-deprecation ...` to show where the warning was created)
▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄▄
██░▄▄▄░██░▄▄░██░▄▄▄██░▀██░██░▄▄▀██░████░▄▄▀██░███░██
██░███░██░▀▀░██░▄▄▄██░█░█░██░█████░████░▀▀░██░█░█░██
██░▀▀▀░██░█████░▀▀▀██░██▄░██░▀▀▄██░▀▀░█░██░██▄▀▄▀▄██
▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀▀
                  🦞 OPENCLAW 🦞                    
 
┌  OpenClaw onboarding
│
◇  Security ──────────────────────────────────────────────────────────────────────────────╮
│                                                                                         │
│  Security warning — please read.                                                        │
│                                                                                         │
│  OpenClaw is a hobby project and still in beta. Expect sharp edges.                     │
│  This bot can read files and run actions if tools are enabled.                          │
│  A bad prompt can trick it into doing unsafe things.                                    │
│                                                                                         │
│  If you’re not comfortable with basic security and access control, don’t run OpenClaw.  │
│  Ask someone experienced to help before enabling tools or exposing it to the internet.  │
│                                                                                         │
│  Recommended baseline:                                                                  │
│  - Pairing/allowlists + mention gating.                                                 │
│  - Sandbox + least-privilege tools.                                                     │
│  - Keep secrets out of the agent’s reachable filesystem.                                │
│  - Use the strongest available model for any bot with tools or untrusted inboxes.       │
│                                                                                         │
│  Run regularly:                                                                         │
│  openclaw security audit --deep                                                         │
│  openclaw security audit --fix                                                          │
│                                                                                         │
│  Must read: https://docs.openclaw.ai/gateway/security                                   │
│                                                                                         │
├─────────────────────────────────────────────────────────────────────────────────────────╯
│
◇  I understand this is powerful and inherently risky. Continue?
│  Yes
│
◇  Onboarding mode
│  QuickStart
│
◇  QuickStart ─────────────────────────╮
│                                      │
│  Gateway port: 18789                 │
│  Gateway bind: Loopback (127.0.0.1)  │
│  Gateway auth: Token (default)       │
│  Tailscale exposure: Off             │
│  Direct to chat channels.            │
│                                      │
├──────────────────────────────────────╯
│
◇  Model/auth provider
│  Qwen
│
◇  Qwen auth method
│  Qwen OAuth
│
◑  Starting Qwen OAuth…│
◇  Qwen OAuth ─────────────────────────────────────────────────────────────────────────╮
│                                                                                      │
│  Open https://chat.qwen.ai/authorize?user_code=HDFNX3UV&client=qwen-code to approve  │
│  access.                                                                             │
│  If prompted, enter the code HDFNX3UV.                                               │
│                                                                                      │
├──────────────────────────────────────────────────────────────────────────────────────╯
◇  Qwen OAuth complete
│
◇  Model configured ─────────────────────────────╮
│                                                │
│  Default model set to qwen-portal/coder-model  │
│                                                │
├────────────────────────────────────────────────╯
│
◇  Provider notes ──────────────────────────────────────────────────────────────────────╮
│                                                                                       │
│  Qwen OAuth tokens auto-refresh. Re-run login if refresh fails or access is revoked.  │
│  Base URL defaults to https://portal.qwen.ai/v1. Override                             │
│  models.providers.qwen-portal.baseUrl if needed.                                      │
│                                                                                       │
├───────────────────────────────────────────────────────────────────────────────────────╯
│
◇  Default model
│  Keep current (qwen-portal/coder-model)
│
◇  Channel status ────────────────────────────╮
│                                             │
│  Telegram: not configured                   │
│  WhatsApp: not configured                   │
│  Discord: not configured                    │
│  Google Chat: not configured                │
│  Slack: not configured                      │
│  Signal: not configured                     │
│  iMessage: not configured                   │
│  Google Chat: install plugin to enable      │
│  Nostr: install plugin to enable            │
│  Microsoft Teams: install plugin to enable  │
│  Mattermost: install plugin to enable       │
│  Nextcloud Talk: install plugin to enable   │
│  Matrix: install plugin to enable           │
│  BlueBubbles: install plugin to enable      │
│  LINE: install plugin to enable             │
│  Zalo: install plugin to enable             │
│  Zalo Personal: install plugin to enable    │
│  Tlon: install plugin to enable             │
│                                             │
├─────────────────────────────────────────────╯
│
◇  How channels work ─────────────────────────────────────────────────────────────────────╮
│                                                                                         │
│  DM security: default is pairing; unknown DMs get a pairing code.                       │
│  Approve with: openclaw pairing approve <channel> <code>                                │
│  Public DMs require dmPolicy="open" + allowFrom=["*"].                                  │
│  Multi-user DMs: set session.dmScope="per-channel-peer" (or "per-account-channel-peer"  │
│  for multi-account channels) to isolate sessions.                                       │
│  Docs: start/pairing                  │
│                                                                                         │
│  Telegram: simplest way to get started — register a bot with @BotFather and get going.  │
│  WhatsApp: works with your own number; recommend a separate phone + eSIM.               │
│  Discord: very well supported right now.                                                │
│  Google Chat: Google Workspace Chat app with HTTP webhook.                              │
│  Slack: supported (Socket Mode).                                                        │
│  Signal: signal-cli linked device; more setup (David Reagans: "Hop on Discord.").       │
│  iMessage: this is still a work in progress.                                            │
│  Nostr: Decentralized protocol; encrypted DMs via NIP-04.                               │
│  Microsoft Teams: Bot Framework; enterprise support.                                    │
│  Mattermost: self-hosted Slack-style chat; install the plugin to enable.                │
│  Nextcloud Talk: Self-hosted chat via Nextcloud Talk webhook bots.                      │
│  Matrix: open protocol; install the plugin to enable.                                   │
│  BlueBubbles: iMessage via the BlueBubbles mac app + REST API.                          │
│  LINE: LINE Messaging API bot for Japan/Taiwan/Thailand markets.                        │
│  Zalo: Vietnam-focused messaging platform with Bot API.                                 │
│  Zalo Personal: Zalo personal account via QR code login.                                │
│  Tlon: decentralized messaging on Urbit; install the plugin to enable.                  │
│                                                                                         │
├─────────────────────────────────────────────────────────────────────────────────────────╯
│
◇  Select channel (QuickStart)
│  Telegram (Bot API)
│
◇  Telegram bot token ───────────────────────────────────────────────────────────────────╮
│                                                                                        │
│  1) Open Telegram and chat with @BotFather                                             │
│  2) Run /newbot (or /mybots)                                                           │
│  3) Copy the token (looks like 123456:ABC...)                                          │
│  Tip: you can also set TELEGRAM_BOT_TOKEN in your env.                                 │
│  Docs: https://docs.openclaw.ai/telegram  │
│  Website: https://openclaw.ai                                                          │
│                                                                                        │
├────────────────────────────────────────────────────────────────────────────────────────╯
│
◇  Enter Telegram bot token
│  xxxxxxxxx:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
│
◇  Selected channels ──────────────────────────────────────────────────────────────────────╮
│                                                                                          │
│  Telegram — simplest way to get started — register a bot with @BotFather and get going.  │
│  https://docs.openclaw.ai/channels/telegr  │
│  am                                                                              │
│  https://openclaw.ai                                                                     │
│                                                                                          │
├──────────────────────────────────────────────────────────────────────────────────────────╯
Updated ~/.openclaw/openclaw.json
Workspace OK: ~/.openclaw/workspace
Sessions OK: ~/.openclaw/agents/main/sessions
│
◇  Skills status ────────────╮
│                            │
│  Eligible: 5               │
│  Missing requirements: 44  │
│  Blocked by allowlist: 0   │
│                            │
├────────────────────────────╯
│
◇  Configure skills now? (recommended)
│  No
│
◇  Hooks ──────────────────────────────────────────────────────────╮
│                                                                  │
│  Hooks let you automate actions when agent commands are issued.  │
│  Example: Save session context to memory when you issue /new.    │
│                                                                  │
│  Learn more: https://docs.openclaw.ai/hooks                      │
│                                                                  │
├──────────────────────────────────────────────────────────────────╯
│
◇  Enable hooks?
│  Skip for now
│
◇  Systemd ───────────────────────────────────────────────────────────────────────────────╮
│                                                                                         │
│  Systemd user services are unavailable. Skipping lingering checks and service install.  │
│                                                                                         │
├─────────────────────────────────────────────────────────────────────────────────────────╯
│
◇  
Health check failed: gateway closed (1006 abnormal closure (no close frame)): no close reason
  Gateway target: ws://127.0.0.1:18789
  Source: local loopback
  Config: /root/.openclaw/openclaw.json
  Bind: loopback
│
◇  Health check help ────────────────────────────────╮
│                                                    │
│  Docs:                                             │
│  https://docs.openclaw.ai/gateway/health           │
│  https://docs.openclaw.ai/gateway/troubleshooting  │
│                                                    │
├────────────────────────────────────────────────────╯
Missing Control UI assets. Build them with `pnpm ui:build` (auto-installs UI deps).
│
◇  Optional apps ────────────────────────╮
│                                        │
│  Add nodes for extra features:         │
│  - macOS app (system + notifications)  │
│  - iOS app (camera/canvas)             │
│  - Android app (camera/canvas)         │
│                                        │
├────────────────────────────────────────╯
│
◇  Control UI ───────────────────────────────────────────────────────────────────────────────╮
│                                                                                            │
│  Web UI: http://127.0.0.1:18789/                                                           │
│  Web UI (with token):                                                                      │
│  http://127.0.0.1:18789/?token=xxxxxxxxxxxxxxxxx            │
│  Gateway WS: ws://127.0.0.1:18789                                                          │
│  Gateway: not detected (gateway closed (1006 abnormal closure (no close frame)): no close  │
│  reason)                                                                                   │
│  Docs: https://docs.openclaw.ai/web/control-ui                                             │
│                                                                                            │
├────────────────────────────────────────────────────────────────────────────────────────────╯
│
◇  Workspace backup ────────────────────────────────────────╮
│                                                           │
│  Back up your agent workspace.                            │
│  Docs: https://docs.openclaw.ai/concepts/agent-workspace  │
│                                                           │
├───────────────────────────────────────────────────────────╯
│
◇  Security ──────────────────────────────────────────────────────╮
│                                                                 │
│  Running agents on your computer is risky — harden your setup:  │
│  https://docs.openclaw.ai/security                              │
│                                                                 │
├─────────────────────────────────────────────────────────────────╯
│
◇  Dashboard ready ────────────────────────────────────────────────────────────────╮
│                                                                                  │
│  Dashboard link (with token):                                                    │
│  http://127.0.0.1:18789/?token=xxxxxxxxxxxxxxxxx  │
│  Copy/paste this URL in a browser on this machine to control OpenClaw.           │
│  No GUI detected. Open from your computer:                                       │
│  ssh -N -L 18789:127.0.0.1:18789 root@10.96.125.246                              │
│  Then open:                                                                      │
│  http://localhost:18789/                                                         │
│  http://localhost:18789/?token=xxxxxxxxxxxxxxxxx  │
│  Docs:                                                                           │
│  https://docs.openclaw.ai/gateway/remote                                         │
│  https://docs.openclaw.ai/web/control-ui                                         │
│                                                                                  │
├──────────────────────────────────────────────────────────────────────────────────╯
│
◇  Web search (optional) ─────────────────────────────────────────────────────────────────╮
│                                                                                         │
│  If you want your agent to be able to search the web, you’ll need an API key.           │
│                                                                                         │
│  OpenClaw uses Brave Search for the `web_search` tool. Without a Brave Search API key,  │
│  web search won’t work.                                                                 │
│                                                                                         │
│  Set it up interactively:                                                               │
│  - Run: openclaw configure --section web                                                │
│  - Enable web_search and paste your Brave Search API key                                │
│                                                                                         │
│  Alternative: set BRAVE_API_KEY in the Gateway environment (no config changes).         │
│  Docs: https://docs.openclaw.ai/tools/web                                               │
│                                                                                         │
├─────────────────────────────────────────────────────────────────────────────────────────╯
│
◇  What now ─────────────────────────────────────────────────────────────╮
│                                                                        │
│  What now: https://openclaw.ai/showcase ("What People Are Building").  │
│                                                                        │
├────────────────────────────────────────────────────────────────────────╯
│
└  Onboarding complete. Use the tokenized dashboard link above to control OpenClaw.

│
◇  Install shell completion script?
│  Yes
Completion installed. Restart your shell or run: source /root/.bashrc


{% endhighlight %}

    > 以下在openclaw安装完成后执行,加载环境变量
    {% highlight bash %}
    source /root/.bashrc
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
https://YOUR_URL/?token=xxxxxxxxxxxxxxxxx
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