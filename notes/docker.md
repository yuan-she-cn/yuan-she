# Docker

以下示例均在 Docker version 28.5.1 测试通过

## 创建桥接网络

```bash shell
docker network create network_1
```

> 容器启动时使用 **--network** 参数加入网络，例如：--network network_1  
> 同一网络中可使用 **容器名** 访问容器

## 安装 SQLServer

```bash shell
docker pull mcr.microsoft.com/mssql/server:2017-latest
sudo mkdir -p /home/sqlserver
docker run --name sqlserver2017 \
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
docker run --name nginx1.28.0 \
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
