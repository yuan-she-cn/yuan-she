# Docker

以下示例均在 Docker version 28.5.1 测试通过。

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
--network network_1 \
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
--network network_1 \
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
--network network_1 \
-e "ACCEPT_EULA=Y" \
-e "MSSQL_SA_PASSWORD=x5T9T7tJwAtSrZIr" \
-v /home/sqlserver:/var/opt/mssql \
-p 1433:1433 \
-d mcr.microsoft.com/mssql/server:2017-latest
```

> 可以通过设置 **MSSQL_SA_PASSWORD** 参数修改密码

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
--network network_1 \
-v /home/nginx/conf:/etc/nginx/conf.d \
-v /home/nginx/logs:/var/log/nginx \
-v /home/nginx/html:/usr/nginx/html \
-v /home/nginx/cert:/etc/nginx/cert \
-p 80:80 \
-p 443:443 \
-d nginx:1.28.0
```

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
--network network_1 \
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
--network network_1 \
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

## 安装 NPS

NPS 是一款轻量级、高性能、功能强大的内网穿透代理服务器
[官方网站](https://ehang-io.github.io/nps)

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
--network network_1 \
-v /home/nps/conf:/etc/nps/conf \
-v /home/nps/logs/nps.log:/var/log/nps.log \
-p 8080:8080 \
-p 8024:8024 \
-d nps:0.26
```

> 默认用户名和密码：admin/123

!> 容器重启时，nps 服务可能不能正常启动，可执行 **docker exec nps nps start** 手动启动。

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
