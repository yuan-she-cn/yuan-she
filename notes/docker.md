# Docker

以下示例均在 Docker version 28.5.1 测试通过。

## 在 Windows 安装

以下示例在 Windows Server 2019 测试通过

1. [下载安装包](https://download.docker.com/win/static/stable/x86_64/docker-28.5.1.zip)
2. 解压 docker-28.5.1.zip 到 C:\Program Files
3. PATH 变量添加 C:\Program Files\docker
4. 创建配置文件 C:\ProgramData\docker\config\daemon.json

```daemon.json
{
  "registry-mirrors": ["https://hub.rat.dev"]
}
```

5. 注册服务（需要管理员权限）

```管理员：PowerShell
dockerd.exe --register-service
```

6. 开启容器支持（需要管理员权限）

```管理员：PowerShell
Install-WindowsFeature -Name Containers
Restart-Computer -Force
```

7. 启动服务（需要管理员权限）

```管理员：PowerShell
Start-Service docker
```

## 创建桥接网络

Docker 桥接网络是 Docker 创建的虚拟网络接口，它在宿主机内部创建一个虚拟交换机，让容器可以通过这个交换机相互通信。

```bash shell
docker network create network_1
```

> 容器启动时使用 **--network** 参数加入网络，例如：--network network_1  
> 同一网络中可使用 **容器名** 访问容器

## 安装 PostgreSQL

PostgreSQL 是一款强大的开源对象关系数据库系统，经过 35 年的积极开发，在可靠性、功能健壮性和性能方面赢得了良好的声誉。
[官方网站](https://www.postgresql.org)

```bash shell
docker pull postgres:18.0
sudo mkdir -p /home/postgres/data
sudo mkdir -p /home/postgres/logs
sudo chmod 777 /home/postgres/logs
docker run --name postgres \
--restart unless-stopped \
-e "POSTGRES_PASSWORD=x5T9T7tJwAtSrZIr" \
-e "TZ=Asia/Shanghai" \
-v /home/postgres/data:/var/lib/postgresql/18/docker \
-v /home/postgres/logs:/var/log/postgresql \
-p 5432:5432 \
-d postgres:18.0
docker exec -it postgres /bin/bash
  echo "logging_collector = on" >> /var/lib/postgresql/18/docker/postgresql.conf
  echo "log_directory = '/var/log/postgresql'" >> /var/lib/postgresql/18/docker/postgresql.conf
  echo "log_filename = '%Y-%m-%d.log'" >> /var/lib/postgresql/18/docker/postgresql.conf
  echo "log_statement = 'mod'" >> /var/lib/postgresql/18/docker/postgresql.conf
  echo "log_min_duration_statement = 5000" >> /var/lib/postgresql/18/docker/postgresql.conf
  exit
docker restart postgres
```

> 可以通过设置 **POSTGRES_PASSWORD** 参数修改密码

### 使用 PostgreSQL

#### 修改密码

```bash shell
# 修改认证方法为 trust
docker exec -it postgres /bin/bash
apt update
apt install -y vim
vim /var/lib/postgresql/18/docker/pg_hba.conf
## 将认证方法 md5 或 scram-sha-256 改为 trust
#local all all   trust
exit
docker restart postgres
# 修改密码
docker exec -it postgres /bin/bash
psql -U postgres
ALTER USER postgres WITH PASSWORD 'hncz1W4lJr0UOyZb';
EXIT;
vim /var/lib/postgresql/18/docker/pg_hba.conf
## 将认证方法 trust 改回 md5 或 scram-sha-256
# local all all   scram-sha-256
exit
docker restart postgres
```

#### 备份数据库

```bash shell
docker exec -it postgres /bin/bash
# 备份数据库，语法：pg_dump -U 用户 -h 主机地址 -p 端口 数据库名 > 脚本文件.sql
pg_dump -U postgres <数据库名> > <脚本文件>.sql
exit
docker cp postgres:<脚本文件>.sql <脚本文件>.sql
```

#### 还原数据库

```bash shell
docker cp <脚本文件>.sql postgres:<脚本文件>.sql
docker exec -it postgres /bin/bash
# 创建用户与数据库
psql -U postgres
CREATE USER <用户> WITH PASSWORD '<密码>' LOGIN;
CREATE DATABASE <数据库名> OWNER = <用户> ENCODING = 'UTF8' LC_COLLATE = 'en_US.UTF-8' LC_CTYPE = 'en_US.UTF-8' TEMPLATE = template0 IS_TEMPLATE = false;
EXIT;
# 还原数据库，语法：psql -U 用户 -h 主机地址 -p 端口 -d 数据库名 -f 脚本文件.sql
psql -U postgres -d <数据库名> -f <脚本文件>.sql
```

## 安装 MySQL

MySQL 是最流行的开源 SQL 数据库管理系统，由 Oracle 公司开发、分发和支持。
[官方网站](https://www.mysql.com)

```bash shell
docker pull mysql:9.5.0
sudo mkdir -p /home/mysql/data
sudo mkdir -p /home/mysql/conf
sudo mkdir -p /home/mysql/logs
sudo chmod 777 /home/mysql/logs
sudo vim /home/mysql/conf/mysql.cnf
#[mysqld]
#log-error = /var/log/mysql/error.log
docker run --name mysql \
--restart unless-stopped \
-e "MYSQL_ROOT_PASSWORD=x5T9T7tJwAtSrZIr" \
-v /home/mysql/data:/var/lib/mysql \
-v /home/mysql/conf:/etc/mysql/conf.d \
-v /home/mysql/logs:/var/log/mysql \
-p 3306:3306 \
-d mysql:9.5.0
```

> 可以通过设置 **MYSQL_ROOT_PASSWORD** 参数修改密码

## 安装 SQL Server

SQL Server 是一款关系数据库管理系统。应用程序和工具连接到 SQL Server 实例或数据库，并使用 Transact-SQL 进行通信。
[官方网站](https://www.microsoft.com/zh-cn/sql-server)

```bash shell
docker pull mcr.microsoft.com/mssql/server:2017-latest
sudo mkdir -p /home/sqlserver
docker run --name sqlserver \
--restart unless-stopped \
-e "ACCEPT_EULA=Y" \
-e "MSSQL_SA_PASSWORD=x5T9T7tJwAtSrZIr" \
-v /home/sqlserver:/var/opt/mssql \
-p 1433:1433 \
-d mcr.microsoft.com/mssql/server:2017-latest
```

> 可以通过设置 **MSSQL_SA_PASSWORD** 参数修改密码

## 安装 Dameng

100% 自主研发的大型通用关系型数据库，支持单机/主备、共享集群、分布式等多种部署方式，覆盖 OLAP/OLTP/HTAP 交易和分析混合负载场景，为用户提供具备极致兼容性、高可用性、高可靠性、高安全性及易用性的企业级数据库产品与服务。
[官方网站](https://www.dameng.com)

```bash shell
sudo apt install -y p7zip-full
wget https://download.dameng.com/eco/adapter/DM8/202604/dm8_20260401_x86_Ubuntu22_64.zip
7z x ./dm8_20260401_x86_Ubuntu22_64.zip -o./dm8_20260401_x86_Ubuntu22_64
7z x ./dm8_20260401_x86_Ubuntu22_64/dm8_20260401_x86_Ubuntu22_64.iso -o./dm8_20260401_x86_Ubuntu22_64
vim ./dm8_20260401_x86_Ubuntu22_64/conf.xml
#<?xml version="1.0"?>
#<DATABASE>
#  <LANGUAGE>en</LANGUAGE>
#  <INSTALL_PATH>/home/dameng/dm8</INSTALL_PATH>
#  <INIT_DB>y</INIT_DB>
#  <DB_PARAMS>
#    <PATH>/home/dameng/data</PATH>
#    <SYSDBA_PWD>x5T9T7tJwAtSrZIr</SYSDBA_PWD>
#    <SYSAUDITOR_PWD>x5T9T7tJwAtSrZIr</SYSAUDITOR_PWD>
#  </DB_PARAMS>
#  <CREATE_DB_SERVICE>n</CREATE_DB_SERVICE>
#</DATABASE>
vim ./Dockerfile
#FROM ubuntu:22.04
#WORKDIR /home/dameng
#RUN groupadd dameng -g 1000
#RUN useradd -g dameng -m -d /home/dameng -s /bin/bash -u 1000 dameng
#RUN passwd -d dameng
#RUN echo "dameng soft nice 0" >> /etc/security/limits.conf
#RUN echo "dameng hard nice 0" >> /etc/security/limits.conf
#RUN echo "dameng soft as unlimited" >> /etc/security/limits.conf
#RUN echo "dameng hard as unlimited" >> /etc/security/limits.conf
#RUN echo "dameng soft fsize unlimited" >> /etc/security/limits.conf
#RUN echo "dameng hard fsize unlimited" >> /etc/security/limits.conf
#RUN echo "dameng soft nproc 65536" >> /etc/security/limits.conf
#RUN echo "dameng hard nproc 65536" >> /etc/security/limits.conf
#RUN echo "dameng soft nofile 65536" >> /etc/security/limits.conf
#RUN echo "dameng hard nofile 65536" >> /etc/security/limits.conf
#RUN echo "dameng soft core unlimited" >> /etc/security/limits.conf
#RUN echo "dameng hard core unlimited" >> /etc/security/limits.conf
#RUN echo "dameng soft data unlimited" >> /etc/security/limits.conf
#RUN echo "dameng hard data unlimited" >> /etc/security/limits.conf
#COPY ./dm8_20260401_x86_Ubuntu22_64/DMInstall.bin /home/dameng/DMInstall.bin
#COPY ./dm8_20260401_x86_Ubuntu22_64/conf.xml /home/dameng/conf.xml
#RUN mkdir -p /home/dameng/dm8
#RUN mkdir -p /home/dameng/data
#RUN chown -R dameng:dameng /home/dameng
#RUN chmod -R 755 /home/dameng
#USER dameng
#RUN /home/dameng/DMInstall.bin -q /home/dameng/conf.xml
#USER root
#RUN /home/dameng/dm8/script/root/root_installer.sh
#RUN echo "export PATH=\$PATH:\$DM_HOME/bin:\$DM_HOME/tool" >> /home/dameng/.bash_profile
#USER dameng
#RUN . /home/dameng/.bash_profile
#CMD ["/home/dameng/dm8/bin/dmserver", "/home/dameng/data/DAMENG/dm.ini"]
docker build -t dameng:8-20260401 .
docker run --name dameng \
--restart unless-stopped \
-p 5236:5236 \
-d dameng:8-20260401
```

## 安装 MongoDB

MongoDB是一种面向文档的可操作数据库，是作为现代应用程序关系数据库的替代方案而全新构建的。与关系数据库不同， MongoDB允许开发者存储丰富的类似JSON的文档，这些文档自然映射到他们在代码中使用的对象。
[官方网站](https://www.mongodb.com)

```bash shell
docker pull mongodb/mongodb-community-server:8.2.6-ubuntu2204
sudo mkdir -p /home/mongodb/data
sudo chmod 777 /home/mongodb/data
docker run --name mongodb \
--restart unless-stopped \
-e "MONGODB_INITDB_ROOT_USERNAME=root" \
-e "MONGODB_INITDB_ROOT_PASSWORD=x5T9T7tJwAtSrZIr" \
-v /home/mongodb/data:/data/db \
-p 27017:27017 \
-d mongodb/mongodb-community-server:8.2.6-ubuntu2204
```

> 可以通过设置 **MONGODB_INITDB_ROOT_USERNAME** 和 **MONGODB_INITDB_ROOT_PASSWORD** 参数修改用户和密码

## 安装 Redis

Redis 可用作数据库、缓存、流式处理引擎、消息代理等。
[官方网站](https://redis.io)

```bash shell
docker pull redis:8.4.0
docker run --name redis \
--restart unless-stopped \
-p 6379:6379 \
-d redis:8.4.0 \
redis-server --requirepass "x5T9T7tJwAtSrZIr"
```

> 可以通过设置 **requirepass** 参数修改密码

## 安装 Nginx

Nginx 是一款 HTTP web 服务器、反向代理、内容缓存、负载均衡器、TCP/UDP 代理服务器和邮件代理服务器。最初由 Igor Sysoev 编写，并以 2-clause BSD 许可证分发。
[官方网站](https://nginx.org)

```bash shell
docker pull nginx:1.28.0
sudo mkdir -p /home/nginx/conf
sudo mkdir -p /home/nginx/logs
sudo mkdir -p /home/nginx/html
sudo mkdir -p /home/nginx/cert
sudo vim /home/nginx/conf/server.conf
#server {
#  listen 80;
#  server_name _;
#
#  location / {
#    root /usr/nginx/html;
#    index index.html;
#  }
#}
sudo vim /home/nginx/html/index.html
#<html>
#  <body>
#    Nginx 1.28.0
#  </body>
#</html>
docker run --name nginx \
--restart unless-stopped \
-v /home/nginx/conf:/etc/nginx/conf.d \
-v /home/nginx/logs:/var/log/nginx \
-v /home/nginx/html:/usr/nginx/html \
-v /home/nginx/cert:/etc/nginx/cert \
-p 80:80 \
-p 443:443 \
-d nginx:1.28.0
```

## 安装 GitLab

```bash shell
# 安装 GitLab
docker pull gitlab/gitlab-ce:18.8.2-ce.0
sudo mkdir -p /home/gitlab/data
sudo mkdir -p /home/gitlab/conf
sudo mkdir -p /home/gitlab/logs
docker run --name gitlab \
--restart unless-stopped \
--hostname 127.0.0.1 \
-e "GITLAB_OMNIBUS_CONFIG=external_url 'http://127.0.0.1/gitlab'" \
-v /home/gitlab/data:/var/opt/gitlab \
-v /home/gitlab/conf:/etc/gitlab \
-v /home/gitlab/logs:/var/log/gitlab \
-p 80:80 \
-p 443:443 \
-p 2222:22 \
-d gitlab/gitlab-ce:18.8.2-ce.0

# 查看 root 密码，24 小时自动删除
docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
```

> 需要根据实际情况设置 **hostname** 参数，修改容器的主机名  
> 需要根据实际情况设置 **external_url** 参数，修改公开访问地址

!> SSH 尚未配置

## 安装 Gitea

Gitea 是一个轻量级的 DevOps 平台软件。从开发计划到产品成型的整个软件生命周期，他都能够高效而轻松的帮助团队和开发者。包括 Git 托管、代码审查、团队协作、软件包注册和 CI/CD。
[官方网站](https://about.gitea.com)

```bash shell
docker pull docker.gitea.com/gitea:1.25.5
sudo mkdir -p /home/gitea/data
sudo chmod 777 /home/gitea/data
docker run --name gitea \
--restart unless-stopped \
-e "TZ=Asia/Shanghai" \
-v /home/gitea/data:/data \
-p 3000:3000 \
-d docker.gitea.com/gitea:1.25.5
```

> 配置文件：/data/gitea/conf/app.ini  
> 可以根据实际情况设置 **DISABLE_REGISTRATION** 参数，禁用注册

## 安装 OpenProject

```bash shell
docker pull openproject/openproject:17.3.1-slim
sudo mkdir -p /home/openproject/assets
sudo chmod 777 /home/openproject/assets
# 创建数据表
docker run --rm \
-e "SECRET_KEY_BASE=aITsO5HUSdbgTGK3" \
-e "DATABASE_URL=postgresql://postgres:x5T9T7tJwAtSrZIr@postgres:5432/openproject" \
-e "OPENPROJECT_DEFAULT__LANGUAGE=zh-CN" \
openproject/openproject:17.3.1-slim \
bundle exec rails db:migrate RAILS_ENV=production
# 创建管理员
docker run --rm \
-e "SECRET_KEY_BASE=aITsO5HUSdbgTGK3" \
-e "DATABASE_URL=postgresql://postgres:x5T9T7tJwAtSrZIr@postgres:5432/openproject" \
-e "OPENPROJECT_DEFAULT__LANGUAGE=zh-CN" \
openproject/openproject:17.3.1-slim \
bundle exec rails runner "user = User.create(login: 'admin', password: 'x5T9T7tJwAtSrZIr', password_confirmation: 'x5T9T7tJwAtSrZIr', mail: 'admin@163.com', firstname: 'xingping', lastname: 'wu'); user.admin = true; user.save!" RAILS_ENV=production
# 启动 OpenProject
docker run --name openproject \
--restart unless-stopped \
-e "SECRET_KEY_BASE=aITsO5HUSdbgTGK3" \
-e "DATABASE_URL=postgresql://postgres:x5T9T7tJwAtSrZIr@postgres:5432/openproject" \
-e "OPENPROJECT_DEFAULT__LANGUAGE=zh-CN" \
-e "OPENPROJECT_HTTPS=false" \
-e "OPENPROJECT_HOST__NAME=127.0.0.1:8080" \
-v /home/openproject/assets:/var/openproject/assets \
-p 8080:8080 \
-d openproject/openproject:17.3.1-slim
```

> -e 的参数值需要根据实据情况修改

## 安装 Umami

Umami 是一款开源的、以隐私为重点的网络分析工具，可作为 Google Analytics 的替代品。它提供了对网站流量、用户行为和性能的重要要素，同时优先考虑数据隐私。
[官方网站](https://umami.is)

### 使用源码构建安装

```bash shell
git clone https://github.com/umami-software/umami.git
cd umami
git checkout v3.0.0
docker build --build-arg BASE_PATH=/umami -t umami:3.0.0 .
docker run --name umami \
--restart unless-stopped \
-e "APP_SECRET=aITsO5HUSdbgTGK3" \
-e "DATABASE_URL=postgresql://postgres:x5T9T7tJwAtSrZIr@postgres:5432/umami" \
-p 3000:3000 \
-d umami:3.0.0
```

### 使用官方镜像安装

```bash shell
docker pull docker.umami.is/umami-software/umami:postgresql-3.0.0
docker run --name umami \
--restart unless-stopped \
-e "APP_SECRET=aITsO5HUSdbgTGK3" \
-e "DATABASE_URL=postgresql://postgres:x5T9T7tJwAtSrZIr@postgres:5432/umami" \
-p 3000:3000 \
-d docker.umami.is/umami-software/umami:postgresql-3.0.0
```

> BASE_PATH 为 URL 前缀，只在构建时生效  
> APP_SECRET 为随机字符串  
> DATABASE_URL 为数据库连接，唯一必须的变量

### 使用 Umami

#### 默认用户

用户：admin 密码：umami

#### 设置语言

<地球图标> --> <中文>

#### 添加网站

<网站> --> <添加网站> --> <填写表单> --> <保存>

#### 收集数据

<网站> --> <编辑图标> --> <复制跟踪代码> --> <粘贴到网站的 head 标签下>

#### 排除访问记录

F12 --> Console --> localStorage.setItem("umami.disabled", 1);

## 安装 Pritunl Zero

Pritunl Zero 是一个零信任系统，它无需使用 VPN 即可从不受信任的网络,安全地经过身份验证访问内部服务。
[官方网站](https://pritunl.com)

```bash shell
docker pull pritunl/pritunl-zero:1.0.3779.61
docker run --name pritunl-zero \
--restart unless-stopped \
-e "NODE_ID=64e8f29c0a7d1b5e2f4680ac" \
-e "MONGO_URI=mongodb://root:x5T9T7tJwAtSrZIr@mongodb:27017/pritunl-zero?authSource=admin" \
-p 80:80 \
-p 443:443 \
-d pritunl/pritunl-zero:1.0.3779.61
```

> 参数 **NODE_ID** 必须是 24 位十六进制字符  
> 默认用户在表 **users** 中查找

## 安装 NPS

NPS 是一款轻量级、高性能、功能强大的内网穿透代理服务器
[官方网站](https://ehang-io.github.io/nps)

### 安装服务端

```bash shell
wget https://github.com/ehang-io/nps/releases/download/v0.26.10/linux_amd64_server.tar.gz
sudo mkdir -p /home/nps/conf
sudo mkdir -p /home/nps/logs
sudo touch /home/nps/logs/nps.log
vim start.sh
##! /bin/bash
#if [ ! "$(ls -A /etc/nps/conf)"]; then
#  cp -a /app/conf_default/* /etc/nps/conf/
#fi
#nps start
#tail -f /dev/null
vim Dockerfile
#FROM ubuntu:22.04
#WORKDIR /app
#ADD ./linux_amd64_server.tar.gz /app
#COPY ./start.sh /app/start.sh
#RUN chmod +x /app/start.sh
#RUN ./nps install
#RUN cp -a /etc/nps/conf /app/conf_default
#CMD ["/app/start.sh"]
docker build -t nps:0.26 .
docker run --name nps \
--restart unless-stopped \
-v /home/nps/conf:/etc/nps/conf \
-v /home/nps/logs/nps.log:/var/log/nps.log \
-p 8080:8080 \
-p 8024:8024 \
-d nps:0.26
```

> **8080** 为管理端口，**8024** 为通信端口  
> 默认用户名和密码：admin/123，可在 **/home/nps/conf/nps.conf** 文件中修改 **web_username** 和 **web_password** 属性

!> 容器重启时，nps 服务可能不能正常启动，可执行 **docker exec nps nps start** 手动启动。

### 安装客户端

```bash shell
wget https://github.com/ehang-io/nps/releases/download/v0.26.10/linux_amd64_client.tar.gz
sudo mkdir /services/npc-0.26.10
sudo tar -zxvf linux_amd64_client.tar.gz -C /services/npc-0.26.10/
cd /services/npc-0.26.10
sudo ./npc install -server=<主机>:<端口> -vkey=<密钥>
sudo npc start

# 卸载
sudo ./npc uninstall
```

## 安装迅雷（NAS版）

```bash shell
docker pull cnk3x/xunlei:latest
sudo mkdir -p /home/xunlei/data
sudo chmod 777 /home/xunlei/data
docker run --name xunlei \
--restart unless-stopped \
--cap-add=SYS_ADMIN \
--security-opt apparmor:unconfined \
-v /home/xunlei/data:/xunlei/downloads \
-p 2345:2345 \
-d cnk3x/xunlei:latest
```

> 邀请码：迅雷牛通

## 常用命令

```bash shell
# 导出镜像
docker save -o <文件名> <镜像名>
# 导入镜像
docker load -i <文件名>
# 删除镜像
docker rmi <ID/镜像名>

# 重启运行的容器
docker restart <ID/容器名>
# 删除运行的容器
docker rm -f <ID/容器名>
# 进入运行的容器，使用 exit 退出
docker exec -it <ID/容器名> /bin/bash

# 构建镜像
docker build -t <镜像名> <构建目录>

# 从容器中复制文件
docker cp <ID/容器名>:<文件名> <文件名>

# 查看容器占用资源情况
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}\t{{.BlockIO}}"
# 限制容器占用资源
docker update --cpus="1.0" --memory="256m" --memory-swap="512m" <ID/容器名>
```

## Dockerfile 指令

| 指令    | 作用                 | 示例                  |
| ------- | -------------------- | --------------------- |
| FROM    | 设置基础镜像         | FROM ubuntu:22.04     |
| WORKDIR | 设置工作目录         | WORKDIR /app          |
| COPY    | 复制文件到镜像       | COPY ./\* /app        |
| ADD     | 类似 COPY，但可解压  | ADD ./\*.tar.gz /app  |
| RUN     | 构建镜像时执行的命令 | RUN apt update        |
| CMD     | 容器启动时执行的命令 | CMD ["apt", "update"] |

## 常见问题

### 修改容器时区

```bash shell
# 进入容器
docker exec -it <ID/容器名> /bin/bash
# 或
docker exec -it -u root <ID/容器名> /bin/sh

# 检查是否存在时区文件
ls -la /usr/share/zoneinfo/Asia/Shanghai
# 如果不存在时区文件
cat /etc/os-release
# 安装 tzdata (Alpine Linux)
apk add --no-cache tzdata
# 或 (Debian/Ubuntu)
apt update && apt install -y tzdata

# 修改时区
date
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
echo "Asia/Shanghai" > /etc/timezone
date
# 中国标准时间 SCT，例如：Thu Feb 26 14:56:16 CST 2026
```
