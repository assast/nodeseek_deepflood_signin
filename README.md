# 🎉 NodeSeek&DeepFlood论坛-自动签到Cookie版

📌 用于`NodeSeek`和`DeepFlood`论坛的每日自动签到，支持消息推送。

📌 推荐到论坛手动签到，本项目主要懒人和容易忘记签到的伙伴使用。

📢 目前NodeSeek、DeepFlood论坛加强Cloudflare验证，可能随时会失效，请实时关注Cookie的更新和本项目更新。

## 🔥 功能特性

- 支持随机/固定签到模式
- 查询用户等级、鸡腿数、发帖数等数据
- 支持代理配置，实现本地或国内请求或者部署（支持自动轮换代理列表，避免IP 被阻止请求）
- 钉钉机器人、Telegram机器人、企业微信机器人、邮箱、Server酱、PushDeer等等通知签到结果（调用青龙通知API，具体自行测试）
- 支持青龙面板部署、Docker容器化部署、本地运行（本地支持钉钉和Telegram机器人通知）
- 自动构建多架构Docker镜像（amd64/arm64），推送到GitHub Container Registry

## 🚀 使用指南（青龙面板）

### 1. 订阅管理

在青龙面板 -> 订阅管理 -> 添加订阅 -> 添加以下信息：

```bash
名称：NSDF论坛签到
类型：公开仓库
链接：https://github.com/assast/nodeseek_deepflood_signin.git
定时类型：crontab
定时规则：20 2 * * 0
文件后缀：py
```

### 2. 依赖安装

在青龙面板 -> 依赖管理 -> Python3 ->名称 添加以下Python库（推荐定时更新该Python库）：

```bash
git+https://github.com/VeNoMouS/cloudscraper.git
```

### 3. 配置环境变量

在青龙面板 -> 环境变量 添加以下变量，`NS_COOKIE`必须添加：

| 变量名            | 必填 | 默认值 | 说明                                          |
| ----------------- | ---- | ------ | --------------------------------------------- |
| `NS_COOKIE`     | 是   | 无     | NodeSeek 登录 Cookie，F12 控制台获取          |
| `NS_RANDOM`     | 否   | true   | 签到模式: true(随机鸡腿)/false(固定5个鸡腿)   |
| `NS_MEMBER_ID`  | 否   | 无     | 成员ID，用于查询账户信息(空间页URL中的数字ID) |
| `DF_COOKIE`     | 是   | 无     | NodeSeek 登录 Cookie，F12 控制台获取          |
| `DF_RANDOM`     | 否   | true   | 签到模式: true(随机鸡腿)/false(固定5个鸡腿)   |
| `DF_MEMBER_ID`  | 否   | 无     | 成员ID，用于查询账户信息(空间页URL中的数字ID) |
| `PROXIES`    | 否   | 无     | 代理配置：http/https，多个请用","英文逗号隔开 |
| `DD_BOT_TOKEN`  | 否   | 无     | 钉钉机器人 access_token 的 Token 部分         |
| `DD_BOT_SECRET` | 否   | 无     | 钉钉机器人加签密钥(选择加签安全模式时需要)    |
| `TG_BOT_TOKEN`  | 否   | 无     | Telegram Bot Token（从 @BotFather 获取）      |
| `TG_CHAT_ID`    | 否   | 无     | Telegram Chat ID（从 @userinfobot 获取）      |

## 🐳 Docker 部署指南（推荐）

### 方式一：使用 docker-compose（推荐）

1. **创建配置文件**

在任意目录创建 `.env` 文件：

```bash
# NodeSeek 配置
NS_COOKIE=你的Cookie
NS_MEMBER_ID=你的成员ID
NS_RANDOM=true

# DeepFlood 配置
DF_COOKIE=你的Cookie
DF_MEMBER_ID=你的成员ID
DF_RANDOM=true

# 代理配置（可选）
PROXIES=http://127.0.0.1:7890,https://127.0.0.1:7890

# 钉钉通知配置（可选）
DD_BOT_ENABLE=true
DD_BOT_TOKEN=你的钉钉机器人Token
DD_BOT_SECRET=你的钉钉机器人密钥

# Telegram通知配置（可选）
TG_BOT_ENABLE=true
TG_BOT_TOKEN=你的Bot Token
TG_CHAT_ID=你的Chat ID

# GitHub用户名（用于拉取镜像）
GITHUB_REPOSITORY_OWNER=assast
```

2. **下载 docker-compose.yml**

```bash
wget https://raw.githubusercontent.com/assast/nodeseek_deepflood_signin/main/docker-compose.yml
```

3. **启动容器**

```bash
# 一次性运行
docker-compose up

# 后台运行
docker-compose up -d

# 查看日志
docker-compose logs -f
```

4. **配置定时任务**

使用宿主机的 crontab 定时启动容器：

```bash
# 编辑定时任务
crontab -e

# 添加以下内容（每天凌晨2点20分执行）
20 2 * * * cd /path/to/docker-compose-dir && docker-compose up >> /path/to/logs/signin.log 2>&1
```

### 方式二：直接使用 docker run

```bash
docker run --rm \
  -e NS_COOKIE="你的Cookie" \
  -e NS_MEMBER_ID="你的成员ID" \
  -e DF_COOKIE="你的Cookie" \
  -e DF_MEMBER_ID="你的成员ID" \
  -e TG_BOT_ENABLE=true \
  -e TG_BOT_TOKEN="你的Bot Token" \
  -e TG_CHAT_ID="你的Chat ID" \
  ghcr.io/assast/nodeseek_deepflood_signin:latest
```

### 镜像说明

- **镜像地址**：`ghcr.io/assast/nodeseek_deepflood_signin:latest`
- **支持架构**：amd64、arm64（支持树莓派等ARM设备）
- **自动构建**：每次推送到 main 分支自动构建并推送到 GitHub Container Registry
- **镜像标签**：
  - `latest`：最新版本
  - `main`：main 分支最新提交
  - `v1.x.x`：版本号标签

## 🖥️ 本地运行指南

### 1. 环境准备

确保已安装 Python 3.7 或更高版本：

```bash
python --version

source venv/bin/activate
```

### 2. 安装依赖

```bash
# 安装 cloudscraper 库
pip install git+https://github.com/VeNoMouS/cloudscraper.git
```

### 3. 配置环境变量

**方式一：命令行临时设置（推荐测试使用）**

```bash
# macOS/Linux
export NS_COOKIE="你的NodeSeek Cookie"
export NS_MEMBER_ID="你的成员ID"
export DF_COOKIE="你的DeepFlood Cookie"
export DF_MEMBER_ID="你的成员ID"

# Windows CMD
set NS_COOKIE=你的NodeSeek Cookie
set NS_MEMBER_ID=你的成员ID

# Windows PowerShell
$env:NS_COOKIE="你的NodeSeek Cookie"
$env:NS_MEMBER_ID="你的成员ID"
```

**方式二：创建 .env 文件（推荐长期使用）**

在项目根目录创建 `.env` 文件，添加以下内容：

```bash
# NodeSeek 配置
NS_COOKIE=你的Cookie
NS_MEMBER_ID=你的成员ID
NS_RANDOM=true

# DeepFlood 配置
DF_COOKIE=你的Cookie
DF_MEMBER_ID=你的成员ID
DF_RANDOM=true

# 代理配置（可选，国内服务器建议配置）
PROXIES=http://127.0.0.1:7890,https://127.0.0.1:7890

# 钉钉通知配置（可选）
DD_BOT_ENABLE=true
DD_BOT_TOKEN=你的钉钉机器人Token
DD_BOT_SECRET=你的钉钉机器人密钥

# Telegram通知配置（可选）
TG_BOT_ENABLE=true
TG_BOT_TOKEN=你的Bot Token
TG_CHAT_ID=你的Chat ID
```

然后在运行前加载环境变量：

```bash
# macOS/Linux
export $(cat .env | xargs)

# 或使用 python-dotenv（需先安装：pip install python-dotenv）
# 在 main.py 开头添加：
# from dotenv import load_dotenv
# load_dotenv()
```

### 4. 运行脚本

```bash
python main.py
```

### 5. 定时任务设置（可选）

**macOS/Linux - 使用 crontab**

```bash
# 编辑定时任务
crontab -e

# 添加以下内容（每天凌晨2点20分执行）
20 2 * * * cd /path/to/nodeseek_deepflood_signin && /usr/bin/python3 main.py >> /path/to/logs/signin.log 2>&1
```

**Windows - 使用任务计划程序**

1. 打开"任务计划程序"
2. 创建基本任务
3. 设置触发器为每天凌晨2点20分
4. 操作选择"启动程序"，填入 Python 路径和 main.py 路径

## 📝 注意事项

- 推荐使用海外服务器部署，国内服务器请设置代理，否则可能无法访问导致签到失败。
- **Docker 部署**（推荐）：支持钉钉和 Telegram 机器人通知，配置简单，一键启动。
- **本地运行**：支持钉钉和 Telegram 机器人通知，需设置 `DD_BOT_ENABLE=true` 或 `TG_BOT_ENABLE=true` 并配置相关参数。
- **青龙面板**：开启通知需在青龙面板 -> 系统设置 -> 通知设置 -> 设置通知方式，配置相关选项。
  - 关闭青龙面板通知一言：在青龙面板 -> notify.py和sendNotify.js文件 -> 搜索"HITOKOTO"变量名 -> 把'HITOKOTO'的值：notify.py改为False，sendNotify.js改为false, 即可关闭通知一言。
- Cookie 到期后需重新获取【非常重要】
- **获取 Telegram Bot Token 和 Chat ID**：
  - Bot Token：与 @BotFather 对话，发送 `/newbot` 创建机器人，获取 Token
  - Chat ID：与 @userinfobot 对话，发送任意消息，获取你的 Chat ID
- **获取 Cookie 方法**（百度搜索：获取Cookie方法）：
  - 1、登录后，按下F12
  - 2、点击Network
  - 3、刷新网页查看Network界面的变化
  - 4、点击Network界面下的Headers
  - 5、找到Cookie并复制后面内容

## 📸 效果示例

```shell
# 控制台输出示例
======================= 开始NodeSeek签到流程 =======================
【1/3】正在进行NodeSeek签到...
随机等待 18.37 秒后继续操作...
等待结束，执行下一步
签到信息：今天的签到收益是5个鸡腿

【2/3】正在获取NodeSeek用户信息...
随机等待 15.39 秒后继续操作...
等待结束，执行下一步
用户信息：
【用户】：示例
【等级】：3
【鸡腿数目】：1029
【主题帖数】：1
【评论数】：40

【3/3】正在推送NodeSeek签到结果...
青龙通知推送成功
======================= NodeSeek签到流程结束 =======================


======================= 开始DeepFlood签到流程 =======================
【1/3】正在进行DeepFlood签到...
随机等待 12.79 秒后继续操作...
等待结束，执行下一步
签到信息：今天的签到收益是6个鸡腿

【2/3】正在获取DeepFlood用户信息...
随机等待 17.05 秒后继续操作...
等待结束，执行下一步
用户信息：
【用户】：示例
【等级】：1
【鸡腿数目】：113
【主题帖数】：0
【评论数】：1

【3/3】正在推送DeepFlood签到结果...
青龙通知推送成功
======================= DeepFlood签到流程结束 =======================
```

```shell
# 钉钉机器人推送示例
「NodeSeek签到」

用户信息：
【用户】：示例
【等级】：3
【鸡腿数目】：1029
【主题帖数】：1
【评论数】：40
签到信息：今天的签到收益是5个鸡腿
时间：2025-10-05 00:00:48

「DeepFlood签到」

【DeepFlood】
用户信息：
【用户】：示例
【等级】：1
【鸡腿数目】：113
【主题帖数】：0
【评论数】：1
签到信息：今天的签到收益是6个鸡腿
操作时间：2025-10-05 12:57:58
```

## 📌 免责声

此库所有内容仅用于个人学习交流！！！请勿用于非法用途。请遵守NodeSeek、DeepFlood论坛的相关规定和条款。
