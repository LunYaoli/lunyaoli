---
title: Springboot项目打包部署到服务器
description: 将本地Springboot项目打包、构建成docker镜像并部署到服务器
date: 2024-06-22 15:00:00 +0800
categories: [部署, Springboot]
tags: [Springboot, Docker, Linux]
---

> ## 一、将Springboot项目打包成jar

通过 IntelliJ IDEA Maven 插件package直接打包SpringBoot项目

> 确保生成的jar在本地能够正常执行
{: .prompt-info }

> 如果jar执行出现`xxx.jar中没有主清单属性`时解决方案：
{: .prompt-danger }

在项目**pom.xml**中添加**spring-boot-maven-plugin**并重新package

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

> ## 二、将jar上传至服务器

- <b><span  style="font-size: 20px; "> xshell </span></b>
- <b><span  style="font-size: 20px; "> xftp </span></b>

> ## 三、构建Docker镜像

1. 编写dockerfile
```bash
# 指定 openjdk:17 为基础镜像
FROM openjdk:17
# WORKDIR指令用于指定容器的一个目录， 容器启动时执行的命令会在该目录下执行
WORKDIR /
# 将当前jar复制到容器根目录下
ADD xxx.jar xxx.jar 
# 暴露端口
EXPOSE 8080
# 启动容器运行命令
CMD java -jar xxx.jar
```
2. 通过docker build -t构建镜像
```bash
docker build -t xxx:1.0.0 -f dockerfile .
```

> ## 四、部署到服务器

1. 运行 Docker 容器
```bash
docker run -itd -p 3000:8080 xxx:1.0.0
```
2. 检查服务器端口是否放开
   1. 检查Nginx配置：[Nginx配置详解](https://soupk.github.io/posts/nginx-peizhi-xiangjie/)
   2. 查看服务器安全组是否放开对应端口
   3. 检查服务器防火墙是否放开对应端口
   ```bash
   # 查看ufw状态和端口状态 numbered为显示序号
   sudo ufw status numbered
   # 允许特定端口
   sudo ufw allow 端口号/tcp
   # 拒绝特定端口
   sudo ufw deny 端口号/tcp
   # 删除对应序号的规则
   sudo ufw delete 序号
   ```
   
