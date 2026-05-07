# OpenProject

OpenProject 是一款免费且开源的项目管理软件，适用于传统项目管理和敏捷项目管理，可在整个项目生命周期中为团队提供支持。OpenProject 支持 30 多种语言。
[官方网站](https://www.openproject.org)

## 安装 OpenProject

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

## 管理人员指南

## 项目经理指南

## 项目成员指南
