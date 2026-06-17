# DeepSeek

**DeepSeek** 是由 **深度求索** 公司创造的 AI 助手。它是一个 **纯文本模型**，支持阅读链接和处理多种文件格式（如：txt、word、excel、ppt、pdf、图像等），拥有 1M 的上下文窗口。**DeepSeek** 支持联网搜索功能（需要用户手动开启），并提供 APP 端的语音输入服务。**DeepSeek** 目前完全免费使用。
[官方网站 https://deepseek.com](https://deepseek.com)

## 获取 API key

1. 登录 DeepSeek [开放平台 https://platform.deepseek.com](https://platform.deepseek.com)
2. API keys -> 创建 API key -> 输入[名称] -> 创建 -> 复制（妥善保管）

## 私有化部署

### 硬件要求

| 硬件 | 最低要求       |
| ---- | -------------- |
| CPU  | 4C             |
| 内存 | 8G             |
| GPU  | 4G（不是必需） |
| 磁盘 | 20G            |

### 示例环境

| 软件     | 示例版本    |
| -------- | ----------- |
| Ubuntu   | 24.04.3 LTS |
| Docker   | 28.5.1      |
| Ollama   | 0.24.0      |
| DeepSeek | r1:8b       |

> 安装 Ubuntu 详见 [https://yuan-she.cn/#/notes/ubuntu?id=安装-ubuntu](https://yuan-she.cn/#/notes/ubuntu?id=安装-ubuntu)  
> 安装 Docker 详见 [https://yuan-she.cn/#/notes/ubuntu?id=安装-docker](https://yuan-she.cn/#/notes/ubuntu?id=安装-docker)

### 安装 Ollama

**Ollama** 是一个开源的、专为在本地运行和管理大型语言模型而设计的工具。它的核心目标是化繁为简，让用户能在自己的电脑上轻松运行各种开源模型，比如：DeepSeek、Llama、Mistral 等。
[官方网站 https://ollama.com](https://ollama.com)

```bash shell
docker pull ollama/ollama:0.24.0
sudo mkdir /home/ollama
sudo chmod 777 /home/ollama
docker run --name ollama \
--restart unless-stopped \
-v /home/ollama:/root/.ollama \
-p 11434:11434 \
-d ollama/ollama:0.24.0
```

> 如果使用显卡需要添加 --gpus all 参数

### 安装 DeepSeek

```bash shell
docker exec -it ollama /bin/bash
ollama pull deepseek-r1:8b
ollama run deepseek-r1:8b

# 出现如下提示符就可以与 DeepSeek 对话了
# >>> Send a message (/? for help)
# 使用 /bye 命令退出对话
# >>> /bye
```

> Ollama 镜像可在 [https://ollama.com/search](https://ollama.com/search) 搜索
