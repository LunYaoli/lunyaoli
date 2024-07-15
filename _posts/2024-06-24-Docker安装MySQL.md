---
title: Docker安装MySQL
description: Docker拉取、运行MySQL镜像
date: 2024-06-24 14:00:00 +0800
categories: [部署, Docker]
tags: [Docker, MySQL]
---
> ## 搜索MySQL镜像

```bash
docker search mysql
```
> ## 一、获取MySQL镜像版本

1. 通过[hub.docker.com](https://hub.docker.com/)获取MySQL镜像版本
    > 国内无法访问此页面
    {: .prompt-danger }
2. 通过github官方版本或者官网版本加版本号进行尝试

> ## 二、拉取MySQL镜像

```bash
docker pull mysql:latest
```

> ## 查看本地镜像

```bash
docker images
```

> ## 运行MySQL容器

```bash
docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=1234 mysql:latest
```

**参数说明**
- -d 后台运行
- --name 容器名称
- -p 端口映射（主机端口:Docker容器端口）
- -e 环境变量（MYSQL_ROOT_PASSWORD=123456 设置root用户密码）

> ## 访问MySQL容器实例

- docker exec
```bash
docker exec -it mysql mysql -u root -p
```
- tcp链接
```bash
mysql -h 127.0.0.1 -u root -p
```
  > 不能在主机侧通过`mysql -h localhost -u root -p`访问，没有相关的sock文件
  {: .prompt-warning }

> ## 数据持久化（推荐）

使用 Docker 卷来持久化数据避免因删除容器导致数据丢失
```bash
docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=1234 -v /my/own/datadir:/var/lib/mysql mysql:latest
```
这里 `-v /my/own/datadir:/var/lib/mysql` 将主机上的 `/my/own/datadir` 目录挂载到容器的 `/var/lib/mysql` 目录，用于存储MySQL数据

> ## 自定义配置文件（可选）

如果你需要使用自定义的 MySQL 配置，你可以创建一个 MySQL 配置文件，并通过挂载卷的方式使用它
```bash
docker run -d --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=1234 -v /my/custom/config-file:/etc/mysql/conf.d mysql:latest
```
这里 `-v /my/custom/config-file:/etc/mysql/conf.d` 将主机上的自定义配置文件目录挂载到容器的 `/etc/mysql/conf.d` 目录
