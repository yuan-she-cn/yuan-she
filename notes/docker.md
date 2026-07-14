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

## 配置集群

```bash shell
# 初始化集群，在管理节点执行
docker swarm init --advertise-addr <管理节点IP>
# 查看 token
docker swarm join-token worker

# 加入集群，在工作节点执行
docker swarm join --token <Token> <管理节点IP:2377>

# 查看节点信息，在管理节点执行
docker node ls

# 创建跨主机网络
docker network create --driver overlay --attachable <网络名>

# 现有容器加入网络
docker network connect <网络名> <容器名>

# 现有容器移除网络
docker network disconnect <网络名> <容器名>
# 查看网络使用情况
docker network inspect <网络名>
# 查看现有容器网络
docker inspect <容器名> --format="{{.NetworkSettings.Networks}}"
# 删除网络
docker network rm <网络名>
```

> 管理节点需要开启端口：UDP 7946 4789，TCP 7946 2377  
> 工作节点需要开启端口：UDP 7846 4789，TCP 7946

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

#### 常用查询

```sql
# 查询所有角色
SELECT rolname FROM pg_roles;
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

### 使用 MySQL

```bash shell
docker exec -it mysql /bin/bash
mysql -u root -p
CREATE DATABASE IF NOT EXISTS `<数据库名>` CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER IF NOT EXISTS '<用户>'@'%' IDENTIFIED BY '<密码>';
GRANT ALL PRIVILEGES ON `<数据库名>`.* TO '<用户>'@'%';
```

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

> 主配置文件在容器的 /etc/nginx/nginx.conf

### 常用配置

```conf
server {
  listen 80;
  server_name yuan-she.cn;
  rewrite ^(.*)$ https://$host$1 permanent;
}

server {
  listen 443 ssl;
  server_name yuan-she.cn;
  client_max_body_size 1024M;

  ssl_certificate /etc/nginx/cert/yuan-she.cn.crt;
  ssl_certificate_key /etc/nginx/cert/yuan-she.cn.key;
  ssl_session_timeout 5m;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
  ssl_prefer_server_ciphers on;

  location / {
    root /usr/nginx/html/yuan-she;
    try_files $uri $uri/ /index.html;
    index index.html index.htm;
  }

  location /server/ {
    proxy_pass http://localhost:8080/;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```

### 安装前置 ModSecurity（WAF）

安装 ModSecurity

```bash shell
docker pull owasp/modsecurity:3.0.8
sudo mkdir -p /home/modsec/conf
sudo mkdir -p /home/modsec/logs
sudo mkdir -p /home/modsec/cert
sudo mkdir -p /home/modsec/rule
sudo vim /home/modsec/conf/default.conf.template
#server {
#  listen 80;
#  server_name _;
#
#  modsecurity on;
#  modsecurity_rules_file /etc/modsecurity/modsec.conf;
#
#  location / {
#    proxy_pass http://nginx;
#    proxy_set_header Host $host;
#    proxy_set_header X-Real-IP $remote_addr;
#    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#    proxy_set_header X-Forwarded-Proto $scheme;
#  }
#}
#
#server {
#  listen 443 ssl;
#  server_name yuan-she.cn www.yuan-she.cn;
#
#  modsecurity on;
#  modsecurity_rules_file /etc/modsecurity/modsec.conf;
#
#  ssl_certificate /etc/nginx/cert/yuan-she.cn.crt;
#  ssl_certificate_key /etc/nginx/cert/yuan-she.cn.key;
#  ssl_session_timeout 5m;
#  ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
#  ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
#  ssl_prefer_server_ciphers on;
#
#  location / {
#    proxy_pass http://nginx;
#    proxy_set_header Host $host;
#    proxy_set_header X-Real-IP $remote_addr;
#    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
#    proxy_set_header X-Forwarded-Proto $scheme;
#  }
#}
cd /home/modsec/rule
sudo wget https://raw.githubusercontent.com/SpiderLabs/ModSecurity/v3/master/modsecurity.conf-recommended
sudo mv modsecurity.conf-recommended modsec.conf
# 加载 CRS 规则，追加
sudo vim modsec.conf
#Include /etc/modsecurity/crs-setup.conf
#Include /etc/modsecurity/rules/*.conf
sudo wget https://raw.githubusercontent.com/SpiderLabs/ModSecurity/v3/master/unicode.mapping
sudo wget https://github.com/coreruleset/coreruleset/archive/refs/tags/v3.3.4.tar.gz
sudo tar -zxvf v3.3.4.tar.gz
sudo cp -r coreruleset-3.3.4/rules .
sudo cp coreruleset-3.3.4/crs-setup.conf.example crs-setup.conf
# 修改 CRS 拦截模式，修改
sudo vim crs-setup.conf
#SecDefaultAction "phase:1,log,auditlog,deny"
#SecDefaultAction "phase:2,log,auditlog,deny"
#SecAction \
#  "id:900000,\
#  phase:1,\
#  nolog,\
#  pass,\
#  t:none,\
#  setvar:tx.paranoia_level=2"
sudo rm -rf coreruleset-3.3.4
sudo rm -rf v3.3.4.tar.gz
docker run --name modsec \
--restart unless-stopped \
--network network_1 \
-v /home/modsec/conf:/etc/nginx/templates/conf.d \
-v /home/modsec/logs:/var/log/modsecurity \
-v /home/modsec/cert:/etc/nginx/cert \
-v /home/modsec/rule:/etc/modsecurity \
-p 80:80 \
-p 443:443 \
-d owasp/modsecurity:3.0.8

docker exec -it modsec /bin/bash
# 第一次开启 ModSecurity
sed -i 's/SecRuleEngine DetectionOnly/SecRuleEngine On/' /etc/modsecurity/modsec.conf
# 关闭 ModSecurity
sed -i 's/SecRuleEngine On/SecRuleEngine Off/' /etc/modsecurity/modsec.conf
# 开启 ModSecurity
sed -i 's/SecRuleEngine Off/SecRuleEngine On/' /etc/modsecurity/modsec.conf
nginx -s reload
```

测试页面

```html
<html>
  <body>
    Nginx 1.28.0
    <script>
      const param = new URLSearchParams(window.location.search).get("param");
      const code = "console.log('" + param + "')";
      eval(code);
    </script>
  </body>
</html>
```

> 请求加入 ?param=%27);alert(1);// 参数访问

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

OpenProject 是一款免费且开源的项目管理软件，适用于传统项目管理和敏捷项目管理，可在整个项目生命周期中为团队提供支持。OpenProject 支持 30 多种语言。
[官方网站](https://www.openproject.org)

```bash shell
docker pull openproject/openproject:17.3.1-slim
sudo mkdir -p /home/openproject/assets
sudo chmod 777 /home/openproject/assets
# 初始化数据库
docker run --rm \
-e "SECRET_KEY_BASE=aITsO5HUSdbgTGK3" \
-e "DATABASE_URL=postgresql://postgres:x5T9T7tJwAtSrZIr@postgres:5432/openproject" \
-e "OPENPROJECT_DEFAULT__LANGUAGE=zh-CN" \
openproject/openproject:17.3.1-slim \
bundle exec rails db:migrate db:seed RAILS_ENV=production
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
> 默认用户名和密码：admin/admin

## 安装 禅道

```bash shell
docker pull hub.zentao.net/app/zentao:22.2-20260526
sudo mkdir -p /home/zentao/data
sudo chmod 777 /home/zentao/data
docker run --name zentao \
--restart unless-stopped \
-e "ZT_MYSQL_HOST=mysql" \
-e "ZT_MYSQL_PORT=3306" \
-e "ZT_MYSQL_DB=zentao" \
-e "ZT_MYSQL_USER=zentao" \
-e "ZT_MYSQL_PASSWORD=x5T9T7tJwAtSrZIr" \
-v /home/zentao/data:/data \
-p 80:80 \
-d hub.zentao.net/app/zentao:22.2-20260526
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

### 修复绕过权限漏洞

**复现漏洞**

```html
<html>
  <head>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/crypto-js/4.1.1/crypto-js.min.js"></script>
    <script>
      const baseUrl = "https://<host>:8080";
      const timestamp = Math.floor(Date.now() / 1000);
      const authKey = CryptoJS.MD5(String(timestamp)).toString();
      console.log(`${baseUrl}/index/index?timestamp=${timestamp}&auth_key=${authKey}`);
    </script>
  </head>
</html>
```

!> 将 baseUrl 更换为管理地址，如：<协议>://<主机>:<端口>  
浏览器打印的地址即可绕过权限

**修复漏洞**

1. 修改 /home/nps/conf/nps.conf 文件
2. 去掉 auth_key 注释，并赋值 16位随机字符串
3. 赋值 auth_crypt_key 为 16位随机字符串
4. 重启 nps 服务

### 安装客户端

- Linux

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

- Windows（需要管理员权限）

```管理员：PowerShell
New-Item -Path "D:\services\npc-0.26.10" -ItemType Directory
cd D:\services\npc-0.26.10
Invoke-WebRequest -Uri "https://github.com/ehang-io/nps/releases/download/v0.26.10/windows_amd64_client.tar.gz" -OutFile "windows_amd64_client.tar.gz"
tar -zxvf windows_amd64_client.tar.gz -C D:\services\npc-0.26.10
./npc.exe install -server="<主机>:<端口>" -vkey="<密钥>"
./npc.exe start

# 卸载
./npc.exe uninstall
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
