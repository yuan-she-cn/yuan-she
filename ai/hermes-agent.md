# Hermes Agent

Hermes Agent 是一个由 Nous Research 团队开发的开源 AI 智能体框架，他被定位为一个“与你一同成长的智能体”，核心特点是拥有持久记忆和自主进化能力。官方网站 [https://hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com)

## 安装 Hermes Agent

### 准备工作

**订阅 Nous Portal**

1. 访问 Nous Portal 官网 [https://portal.nousresearch.com](https://portal.nousresearch.com)
2. 输入[邮箱] -> Submit -> 输入[验证码]
3. Billing -> Subscription -> 选择[Free] -> Subscribe via Stripe
4. 填写[银行卡] -> 订阅

### 在 Ubuntu 中安装

**示例环境**

| 软件   | 版本        |
| ------ | ----------- |
| Ubuntu | 24.04.3 LTS |

```bash shell
# 安装依赖
sudo apt install -y git

# 安装 Hermes Agent
sudo curl -fsSL https://hermes-agent.nousresearch.com/install.sh | sudo bash

# 配置 Hermes Agent
# 选择配置模式（使用快速设置，使用 Nous Portal 订阅）
#   Quick Setup (Nous Portal) — free OAuth login, no API keys, model + tools (recommended)
# 授权 Nous Portal 订阅
#   访问指定网页
#   Sign in -> 输入[邮箱] -> Submit -> 输入[验证码] -> Connect
# 选择模型（使用免费模型）
#   stepfun/step-3.7-flash:free
# 选择启用工具（全选）
# 选择命令执行环境（本机）
#   Local - run directly on this machine (default)
# 配置消息网关（跳过）
#   Skip — set up later with 'hermes setup gateway'

# 开启对话
hermes

# 关闭对话
/exit
```

### 在 Docker 中安装

**示例环境**

| 软件   | 版本        |
| ------ | ----------- |
| Ubuntu | 24.04.3 LTS |
| Docker | 28.5.1      |

```bash shell
# 安装 Hermes Agent
sudo mkdir /home/hermes-agent
sudo chmod 777 /home/hermes-agent
docker pull nousresearch/hermes-agent:v2026.6.5
docker run --name hermes-agent \
--restart unless-stopped \
-v /home/hermes-agent:/opt/data \
-dit nousresearch/hermes-agent:v2026.6.5

# 配置 Hermes Agent
docker exec -it hermes-agent /bin/bash
hermes setup
# 选择配置模式（使用快速设置，使用 Nous Portal 订阅）
#   Quick Setup (Nous Portal) — free OAuth login, no API keys, model + tools (recommended)
# 授权 Nous Portal 订阅
#   访问指定网页
#   Sign in -> 输入[邮箱] -> Submit -> 输入[验证码] -> Connect
# 选择模型（使用免费模型）
#   stepfun/step-3.7-flash:free
# 选择启用工具（全选）
# 选择命令执行环境（本机）
#   Local - run directly on this machine (default)
# 配置消息网关（跳过）
#   Skip — set up later with 'hermes setup gateway'

# 开启对话
hermes

# 关闭对话
/exit
```

## 配置消息平台

### 微信平台

```bash shell
# 配置消息网关
hermes gateway setup
# 选择微信平台
#   Weixin / WeChat  (not configured)
# 启动二维码登录
#   Start QR login now? [Y/n]: y
# 使用微信扫码授权
#   扫码 -> 连接
# 配置机器人私信策略（需管理员配对）
#   Use DM pairing approval (recommended)
# 配置机器人群聊策略（禁用群聊功能）
#   Disable group chats (recommended)
# 配置管理员微信用户ID
#   Use your Weixin user ID (xxxxxxxxxxxxxxxxxxxx-xxxxxxx@im.wechat) as the home channel? [Y/n]: y
# 完成配置
#   Done

# 启动消息网关（使用 Ctrl + C 退出，不会关闭网关）
hermes gateway run

# 配对用户，通过向机器人发送信息获取配对码
hermes pairing approve weixin <配对码>
```

## 文件存储位置

**Ubuntu**

- 源代码：/usr/local/lib/hermes-agent/
- 命令：/usr/local/bin/hermes
- 数据：~/.hermes/
- 配置：~/.hermes/config.yaml
- 密钥：~/.hermes/.env
- 会话：~/.hermes/state.db

**Docker**

- 源代码：/opt/hermes/
- 命令：/opt/hermes/bin/hermes
- 数据：/opt/data/
- 配置：/opt/data/config.yaml
- 密钥：/opt/data/.env
- 会话：/opt/data/state.db

## 常用命令

```bash shell
# 进入 Hermes Agent 容器
docker exec -it hermes-agent /bin/bash

# 查看会话列表
hermes sessions list
# 启动新会话
hermes
# 使用 TUI 启动
hermes --tui
# 启动指定会话
hermes -r <id|title>
# 启动调试模式
hermes chat -v
# 重命名会话
hermes sessions rename <id> <title>

# 运行设置向导
hermes setup
# 运行网关设置向导
hermes gateway setup
# 启动网关
hermes gateway run
```

```hermes
# 重命名会话
/title <title>

# 后台会话
/background <message>

# 切换模型，添加 --global 参数，则会同时修改 config.yaml 文件
/model <model> --provider <provider>
```
