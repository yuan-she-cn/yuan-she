# Ubuntu

以下示例均在 Ubuntu 24.04.3 LTS 测试通过。

## 安装 OpenSSH

OpenSSH 是使用 SSH 协议进行远程登录的首选连接工具。它对所有流量进行加密，以消除窃听、连接劫持和其他攻击。此外，OpenSSH 还提供了一整套安全隧道功能、多种身份验证方法和复杂的配置选项。

```bash shell
# 安装 OpenSSH
sudo apt install -y openssh-server

# 查看 OpenSSH 启动状态
sudo systemctl status ssh
# 停止 OpenSSH
sudo systemctl stop ssh
# 启动 OpenSSH
sudo systemctl start ssh
# 重启 OpenSSH
sudo systemctl restart ssh
# 禁用 OpenSSH 开机启动
sudo systemctl disable ssh
# 启用 OpenSSH 开机启动
sudo systemctl enable ssh
```

## 安装 Docker

Docker 是一款用于开发、发布和运行应用程序的开放平台。Docker 使您能够将应用程序与基础设施分开，以便快速交付软件。使用 Docker，您可以像管理应用程序一样管理基础设施。通过利用 Docker 的交付、测试和部署代码的方法，您可以显著减少编写代码和在生产中运行代码之间的延迟。

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
# 禁用 Docker 开机启动
sudo systemctl disable docker
# 启用 Docker 开机启动
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

## 安装 skopeo

skopeo 是一款命令行实用程序，可以对容器映像和映像存储库执行各种操作。

```bash shell
# 安装
sudo apt install -y skopeo

# 查看镜像标签(版本)
skopeo list-tags docker://hub.rat.dev/postgres >> postgresql.tag.txt
skopeo list-tags docker://mcr.microsoft.com/mssql/server >> sqlserver.tag.txt
```

## 安装 Git

Git 是一款免费开源的分布式版本控制系统，旨在快速高效地处理从小型到大型的所有项目。

```bash shell
# 安装 Git
sudo apt install -y git

# 查看 Git 版本
git -v
```

## 安装 pnpm

pnpm 是一款快速、节省磁盘空间的包管理器。

```bash shell
# 安装 pnpm
sudo wget -qO- https://get.pnpm.io/install.sh | env PNPM_VERSION=10.24.0 sh -
source /home/ubuntu/.bashrc

# 查看 pnpm 版本
pnpm -v
# 安装并使用指定 Node.js 版本
pnpm env use --global 22.19.0
```

!> 使用 source 命令时，需要更换为相应的用户目录
