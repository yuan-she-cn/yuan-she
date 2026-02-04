# Ubuntu

以下示例均在 Ubuntu 24.04.3 LTS 测试通过。

## 配置静态 IP

配置静态 IP 可为你的设备在网络中设置一个永久、固定的地址，让其他设备能稳定可靠地找到并访问它。

```bash shell
sudo vi /etc/netplan/<数字>-<字符串>.yaml
#network:
#  ethernets:
#    <网络接口>:
#      dhcp4: false
#      addresses:
#        - 192.168.1.100/24
#      routes:
#        - to: default
#          via: 192.168.1.1
#      nameservers:
#        addresses: [8.8.8.8, 114.114.114.114]
#  version: 2
sudo netplan apply
```

> <数字> 越大，优先级越高  
> <字符串> 为配置描述  
> <网络接口> 可以使用 ip addr 命令查询

## 安装 OpenSSH

OpenSSH 是使用 SSH 协议进行远程登录的首选连接工具。它对所有流量进行加密，以消除窃听、连接劫持和其他攻击。此外，OpenSSH 还提供了一整套安全隧道功能、多种身份验证方法和复杂的配置选项。
[官方网站](https://www.openssh.org)

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
[官方网站](https://www.docker.com)

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

## 安装 Skopeo

Skopeo 是一款命令行实用程序，可以对容器映像和映像存储库执行各种操作。
[代码仓库](https://github.com/containers/skopeo)

```bash shell
# 安装
sudo apt install -y skopeo

# 查看镜像标签(版本)
skopeo list-tags docker://hub.rat.dev/postgres >> postgresql.tag.txt
skopeo list-tags docker://mcr.microsoft.com/mssql/server >> sqlserver.tag.txt
```

## 安装 Git

Git 是一款免费开源的分布式版本控制系统，旨在快速高效地处理从小型到大型的所有项目。
[官方网站](https://git-scm.com)

```bash shell
# 安装 Git
sudo apt install -y git

# 查看 Git 版本
git -v
```

## 安装 Java

```bash shell
# 安装 Java
wget https://download.oracle.com/java/17/archive/jdk-17.0.11_linux-x64_bin.tar.gz
sudo tar -zxvf jdk-17.0.11_linux-x64_bin.tar.gz -C /usr/local/lib/
vim ~/.profile
## 文件追加
#JAVA_HOME=/usr/local/lib/jdk-17.0.11
#PATH=$PATH:$JAVA_HOME/bin
#CLASSPATH=.:$JAVA_HOME/lib
#export JAVA_HOME PATH CLASSPATH
source ~/.profile

# 查看 Java 版本
java -version
```

## 安装 Maven

```bash shell
# 安装 Maven
wget https://dlcdn.apache.org/maven/maven-3/3.9.12/binaries/apache-maven-3.9.12-bin.tar.gz
sudo tar -zxvf apache-maven-3.9.12-bin.tar.gz -C /usr/local/lib/
sudo mkdir -p /var/local/maven/repo
sudo chmod -R 777 /var/local/maven
sudo vim /usr/local/lib/apache-maven-3.9.12/conf/settings.xml
## settings 标签加入
#  <localRepository>/var/local/maven/repo</localRepository>
## mirrors 标签加入
#    <mirror>
#      <id>nexus-aliyun</id>
#      <mirrorOf>central</mirrorOf>
#      <name>Nexus aliyun</name>
#      <url>http://maven.aliyun.com/nexus/content/groups/public</url>
#    </mirror>
vim ~/.profile
## 文件追加
#PATH=$PATH:/usr/local/lib/apache-maven-3.9.12/bin
#export PATH
source ~/.profile

# 查看 Maven 版本
mvn -v
```

## 安装 pnpm

pnpm 是一款快速、节省磁盘空间的包管理器。
[官方网站](https://pnpm.io)

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

## 定时任务

```bash shell
sudo vim /usr/local/bin/clean-logs.sh
##!/bin/bash
## 删除前一周文件
#find /var/log -name "*.log" -mtime +6 -exec rm -f {} \;
sudo chmod +x /usr/local/bin/clean-logs.sh
sudo crontab -e
## 每天 00:00 执行
#0 0 * * * /usr/local/bin/clean-logs.sh
## 每 5 分钟执行
#0-59/5 * * * * /usr/local/bin/clean-logs.sh
```

## 常用命令

```bash shell
# 查看磁盘使用情况
df -hT
# 查看目录使用情况
du -h / -d 1 | sort -hr
```

## 常见问题

### Dependency is not satisfiable: libgconf-2-4

```bash shell
wget http://archive.ubuntu.com/ubuntu/pool/universe/g/gconf/gconf2-common_3.2.6-7ubuntu2_all.deb
wget http://archive.ubuntu.com/ubuntu/pool/universe/g/gconf/libgconf-2-4_3.2.6-7ubuntu2_amd64.deb
sudo dpkg -i gconf2-common_3.2.6-7ubuntu2_all.deb
sudo dpkg -i libgconf-2-4_3.2.6-7ubuntu2_amd64.deb
```
