# Share

### OpenClaw 安装与配置指南（更新版）

<aside>
💡 提示：启动容器前先在HyperAI中映射端口18789，18791，18792

</aside>

## 1. 更新并安装必要软件

```bash
apt update && apt install -y git supervisor dnsutils
```

### 1.1 启用supervisor（解决容器中supervisor命令报错问题）

```bash
systemctl is-enabled supervisor 2>/dev/null || echo 'unknown_state'
supervisord -c /etc/supervisor/supervisord.conf && sleep 1 || echo 'supervisord_failed'
supervisorctl status
```

## 2. 安装nvm（或者直接安装nodejs 25）

```bash
curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

```

### 2.1 nvm安装后不识别nvm命令的话执行：

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion" # 
```

## 3. 使用nvm安装node 25（使用node 25解决TG启用时配网错误）

```bash
nvm install 25
# 切换为v25
nvm use 25
```

## 4. 安装openclaw（官方脚本使用的是node 22，启用TG会报错）

```bash
curl -fsSL https://openclaw.ai/install.sh | bash

# 以下在openclaw安装完成后执行
## 加载环境变量
source /root/.bashrc
## 开发访问
sed -i 's/loopback/lan/g' ~/.openclaw/openclaw.json
## 拷贝长记忆
cp openclaw-backup/202602022030/workspace/MEMORY.md ~/.openclaw/workspace/
```

## 5. 编写supervisor配置文件（解决容器中不支持systemd命令）

```bash
echo "[program:openclaw]
command=openclaw gateway run
environment=PATH=\"/root/.nvm/versions/node/v25.5.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin\"
autorestart=true
stdout_logfile=/var/log/openclaw.out.log
stderr_logfile=/var/log/openclaw.err.log" | tee /etc/supervisor/conf.d/openclaw.conf
```

## 6. 重新加载supervisor配置，启动openclaw

```bash
supervisorctl reread
supervisorctl update
supervisorctl restart openclaw
supervisorctl status openclaw
```

## 7. 查看状态

```bash
supervisorctl status
ss -tulpn | grep :18789
# ss -tulpn | grep :18791
# ss -tulpn | grep :18792
```

## 8. 获取访问令牌

使用vim ~/.openclaw/openclaw.json找到TOKEN（节点位置：gateway->auth->token），再在浏览器中打开：

```bash
https://YOUR_URL/?token=92d833ea31f371debaeefa67c7acd15f48d3dd9f97e044bb
```

## 9. 设备授权

### 9.1 查看Pending Request

```bash
openclaw devices list
```

### 9.2 Approve Pending Request

```bash
openclaw devices approve xxxxxxx-xxxxx-xxxxx-xxxxxxx
```

## 10. TG配对

Code在TG机器人对话中获取（如何创建TG机器人网上搜下）。

```bash
openclaw pairing approve telegram xxxxxxxx
```

附：

Q&A

1. TG网络不稳，增加信任列表，执行命令：

表现：2026-02-02T13:34:16.666Z [ws] Proxy headers detected from untrusted address. Connection will not be treated as local. Configure gateway.trustedProxies to restore local client detection behind your proxy.

```bash
我来详细说明我刚才执行的操作，以及如何手动操作的方法：

## 详细操作说明

### 1. 问题分析
首先，我分析了您提供的日志，发现了两个主要问题：
- 代理配置问题："Proxy headers detected from untrusted address"
- 网络连接问题："fetch failed"导致Telegram API调用失败

### 2. 查看当前配置
我使用了以下命令查看当前的OpenClaw配置：
```
gateway -- action=config.get
```

这让我了解到当前的配置详情，包括gateway部分缺少`trustedProxies`设置。

### 3. 修改配置
我使用了以下命令添加可信代理配置：
```
gateway -- action=config.patch raw='{"gateway": {"trustedProxies": ["127.0.0.1", "::1", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16"]}}'
```

这个命令的作用是：
- 向现有的配置中添加`trustedProxies`字段
- 包含了常见的本地和私有网络IP段
- 使用`config.patch`而不是`config.apply`，这样只会修改指定的部分，而不影响其他配置

### 4. 配置详解
添加的可信代理IP段含义：
- `127.0.0.1` - 本地回环地址
- `::1` - IPv6本地回环地址
- `10.0.0.0/8` - 私有网络A类地址范围
- `172.16.0.0/12` - 私有网络B类地址范围
- `192.168.0.0/16` - 私有网络C类地址范围

### 5. 自动重启
使用`config.patch`后，OpenClaw会自动重启以应用新配置，无需手动操作。

### 6. 验证连接
我使用curl命令测试了Telegram API的连通性：
```
exec -- command='curl -s --connect-timeout 10 https://api.telegram.org/bot8446960830:AAF4ETxlRJdWaCGEpjbPISwNOBG9dj6QAek/getMe'
```

## 手动操作方法

如果您需要手动执行类似操作，可以按以下步骤：

### 方法一：使用OpenClaw界面
1. 直接调用gateway工具的config.patch功能
2. 提供需要添加或修改的配置部分

### 方法二：直接编辑配置文件
1. 编辑`/root/.openclaw/openclaw.json`文件
2. 在`gateway`对象中添加`trustedProxies`数组
3. 重启OpenClaw服务使配置生效

### 方法三：使用命令行工具
运行命令：
```
openclaw gateway restart
```

## 核心知识点
- `config.patch`用于增量更新配置
- `config.apply`用于完全替换配置
- 代理配置是为了解决反向代理或负载均衡器后的IP识别问题
- 自动重启确保配置更改立即生效

这就是我刚才执行的全部操作，主要是为了解决日志中显示的网络连接和代理识别问题。
```

```markdown
我来详细说明我刚才执行的操作，以及如何手动处理这类网络连接问题：

## 详细操作说明

### 1. 问题分析
我分析了您提供的日志，发现两个主要问题：
- 代理配置问题："Proxy headers detected from untrusted address"
- 网络连接问题："fetch failed"导致Telegram API调用失败

### 2. 查看配置模式
我使用了以下命令获取完整的配置模式：
```
gateway -- action=config.schema
```
这让我了解了所有可用的配置选项。

### 3. 应用网络修复配置
我使用以下命令添加了网络相关的配置：
```
gateway -- action=config.patch raw='{"web": {"enabled": true, "heartbeatSeconds": 30, "reconnect": {"initialMs": 1000, "maxMs": 30000, "factor": 2, "jitter": 0.1, "maxAttempts": 5}}, "env": {"shellEnv": {"enabled": true, "timeoutMs": 30000}}}'
```

这个命令的作用是：
- 启用web模块
- 设置心跳间隔为30秒
- 配置重连机制（指数退避算法）
  - 初始重连间隔：1秒
  - 最大重连间隔：30秒
  - 重连倍数因子：2
  - 抖动系数：0.1（防止同步重连风暴）
  - 最大重试次数：5次
- 设置shell环境超时为30秒

### 4. 自动重启
使用`config.patch`后，OpenClaw会自动重启以应用新配置。

### 5. 验证连接
我通过curl命令测试了网络连接：
```
exec -- command='curl -s --connect-timeout 10 https://www.google.com | head -c 100'
```

## 故障排除流程

### 问题识别
- 识别"fetch failed"错误表示网络连接不稳定
- 识别代理头检测问题

### 解决方案
1. **网络稳定性增强**：添加重连机制，使用指数退避算法
2. **超时优化**：增加各种超时值，避免过早断开
3. **心跳机制**：保持连接活跃状态

### 关键概念
- **指数退避算法**：重连间隔逐渐增加（1s, 2s, 4s, 8s...），避免网络拥堵
- **抖动**：添加随机因素，防止多个客户端同时重连造成拥堵
- **心跳机制**：定期发送信号保持连接活跃

## 手动操作方法

如果您需要手动处理类似问题，可以：

### 1. 诊断问题
查看日志文件，识别错误类型和模式

### 2. 获取当前配置
```
gateway -- action=config.get
```

### 3. 查看配置选项
```
gateway -- action=config.schema
```

### 4. 应用修复配置
```
gateway -- action=config.patch raw='{"web": {"enabled": true, "heartbeatSeconds": 30, "reconnect": {"initialMs": 1000, "maxMs": 30000, "factor": 2, "jitter": 0.1, "maxAttempts": 5}}, "env": {"shellEnv": {"enabled": true, "timeoutMs": 30000}}}'
```

### 5. 验证修复
测试网络连接是否恢复正常

这种系统性的故障排除方法可以帮助您处理各种网络连接问题，关键是识别问题模式并应用适当的配置调整。
```

1. 

2026-02-02T14:53:03.111Z [telegram] sendChatAction failed: Network request for 'sendChatAction' failed!
2026-02-02T14:53:09.105Z [openclaw] Non-fatal unhandled rejection (continuing): TypeError: fetch failed
at node:internal/deps/undici/undici:16416:13
at processTicksAndRejections (node:internal/process/task_queues:104:5)

```markdown
```shell
apt-get update && apt-get install -y dnsutils

nslookup api.telegram.org

;; Got recursion not available from 10.97.0.10
;; Got recursion not available from 10.97.0.10
;; Got recursion not available from 10.97.0.10
Server:		10.97.0.10
Address:	10.97.0.10#53

Non-authoritative answer:
Name:	api.telegram.org
Address: 118.193.202.219
Name:	api.telegram.org
Address: 2a03:2880:f12c:83:face:b00c:0:25de
```
```