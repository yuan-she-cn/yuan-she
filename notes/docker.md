# Docker

以下示例均在 Docker version 28.5.1 测试通过

## 创建桥接网络

```bash shell
docker network create network_1
```

> 容器启动时使用 **--network** 参数加入网络，例如：--network network_1  
> 同一网络中可使用 **容器名** 访问容器

## 安装 PostgreSQL

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

## 安装 SQLServer

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

Umami 是一款开源、轻量的网站数据收集和分析平台

```bash shell
docker pull docker.umami.is/umami-software/umami:postgresql-3.0.0
docker run --name umami \
--restart unless-stopped \
--network network_1 \
-e "APP_SECRET=aITsO5HUSdbgTGK3" \
-e "DATABASE_TYPE=postgresql" \
-e "DATABASE_URL=postgresql://postgres:x5T9T7tJwAtSrZIr@postgres:5432/umami" \
-p 3000:3000 \
-d docker.umami.is/umami-software/umami:postgresql-3.0.0
```

> APP_SECRET 为随机字符串

### 使用 Umami

#### 设置语言

<地球图标> --> <中文>

#### 添加网站

<网站> --> <添加网站> --> <填写表单> --> <保存>

#### 收集数据

<网站> --> <编辑图标> --> <复制跟踪代码> --> <粘贴到网站的 head 标签下>

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
```
