# Hermes Agent

Hermes Agent 是一个由 Nous Research 团队开发的开源 AI 智能体框架，它被定位为一个“与你一同成长的智能体”，核心特点是拥有持久记忆和自主进化能力。
[官方网站 https://hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com)

## 在 Docker 中安装

```bash shell
docker pull nousresearch/hermes-agent:v2026.5.16
sudo mkdir /home/hermes-agent
sudo chmod 777 /home/hermes-agent
docker run -it --rm \
-v /home/hermes-agent:/opt/data \
nousresearch/hermes-agent:v2026.5.16 setup

# 依次选择
# Quick setup - provider, model & messaging (recommended)
# Ollama Cloud (cloud-hosted open models - ollama.com)
# 无需提供 OLLAMA_API_KEY
# Keep current (local)
# Skip - set up later with 'hermes setup gateway'

docker run --name hermes-agent \
--restart unless-stopped \
-e "API_SERVER_ENABLED=true" \
-e "API_SERVER_HOST=0.0.0.0" \
-e "API_SERVER_CORS_ORIGINS=*" \
-e "API_SERVER_KEY=aITsO5HUSdbgTGK3" \
-e "HERMES_DASHBOARD=true" \
-v /home/hermes-agent:/opt/data \
-p 8642:8642 \
-p 9119:9119 \
-d nousresearch/hermes-agent:v2026.5.16 gateway run
```

## 连接 Ollama 模型

```bash shell
docker network create hermes-network
docker network connect hermes-network hermes-agent
docker network connect hermes-network ollama
```
