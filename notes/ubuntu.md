# Ubuntu

以下示例均在 Ubuntu 24.04.3 LTS 测试通过

## 安装 SSH

```bash shell
# 安装 SSH
sudo apt install -y openssh-server

# 查看 SSH 启动状态
sudo systemctl status ssh
# 停止 SSH
sudo systemctl stop ssh
# 启动 SSH
sudo systemctl start ssh
# 重启 SSH
sudo systemctl restart ssh
# 设置 SSH 禁止开机启动
sudo systemctl disable ssh
# 设置 SSH 开机启动
sudo systemctl enable ssh
```

## 安装 Docker

```bash shell
# 下载安装包
wget https://download.docker.com/linux/ubuntu/dists/noble/pool/stable/amd64/containerd.io_1.7.28-1~ubuntu.24.04~noble_amd64.deb
wget https://download.docker.com/linux/ubuntu/dists/noble/pool/stable/amd64/docker-ce_28.5.1-1~ubuntu.24.04~noble_amd64.deb
wget https://download.docker.com/linux/ubuntu/dists/noble/pool/stable/amd64/docker-ce-cli_28.5.1-1~ubuntu.24.04~noble_amd64.deb
wget https://download.docker.com/linux/ubuntu/dists/noble/pool/stable/amd64/docker-buildx-plugin_0.29.1-1~ubuntu.24.04~noble_amd64.deb
wget https://download.docker.com/linux/ubuntu/dists/noble/pool/stable/amd64/docker-compose-plugin_2.40.3-1~ubuntu.24.04~noble_amd64.deb
# 安装依赖包
sudo apt install -y iptables
sudo apt --fix-broken install -y
# 安装 Docker
sudo dpkg -i ./containerd.io_1.7.28-1~ubuntu.24.04~noble_amd64.deb ./docker-ce_28.5.1-1~ubuntu.24.04~noble_amd64.deb ./docker-ce-cli_28.5.1-1~ubuntu.24.04~noble_amd64.deb ./docker-buildx-plugin_0.29.1-1~ubuntu.24.04~noble_amd64.deb ./docker-compose-plugin_2.40.3-1~ubuntu.24.04~noble_amd64.deb

# 查看 Docker 启动状态
sudo systemctl status docker
# 停止 Docker
sudo systemctl stop docker
# 启动 Docker
sudo systemctl start docker
# 重启 Docker
sudo systemctl restart docker
# 设置 Docker 禁止开机启动
sudo systemctl disable docker
# 设置 Docker 开机启动
sudo systemctl enable docker

# 将当前用户加入 Docker 用户组
sudo groupadd docker 2>/dev/null
sudo usermod -aG docker $USER
newgrp docker

# 配置镜像加速器
sudo apt install -y vim
sudo vim /etc/docker/daemon.json
#{
#  "registry-mirrors": ["https://hub.rat.dev"]
#}
sudo systemctl daemon-reload
sudo systemctl restart docker
```

?> 镜像加速器地址由 [**王旭阳个人博客**](https://www.wxy97.com/archives/b5b225b6-7741-4560-be2f-2e6a4f671d9b) 提供

## 安装 Skopeo

Skopeo 是一款用于操作容器镜像和镜像仓库的命令行工具

```bash shell
# 安装
sudo apt install -y skopeo

# 查看镜像标签(版本)
skopeo list-tags docker://hub.rat.dev/postgres >> postgresql.tag.txt
skopeo list-tags docker://mcr.microsoft.com/mssql/server >> sqlserver.tag.txt
```
