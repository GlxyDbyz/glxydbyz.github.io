---
layout:     post
title:      Docker常用命令速记
subtitle:   Docker简单例子
date:       2018-03-12
author:     壹芝
header-img: img/post-bg-coffee.jpg
catalog: true
tags:
    - Docker
    - Linux
---

## Docker常用命令速记

###### 整理自本人的有道历史云笔记

### 安装并启动 Docker

>  ```curl -fsSL https://get.docker.com | sh```

> sudo systemctl start docker

### 常用命令

##### docker 镜像相关操作
> docker search tomcat

> docker pull tomcat:8.5

> docker images

> docker rmi tomcat [-f]

##### docker 容器相关操作
> docker run -p 80:80 -d --name nginx -v /usr/data/nginx/html/:/usr/share/nginx/html nginx 

> docker ps [-a]

> docker stop nginx

> docker rm nginx

##### 进入容器并安装 busybox
> docker exec -it nginx /bin/bash

> apt-get update / install **procps** / **busybox**

##### 容器导出和导入操作

> **export container:**  docker export mysql57 > mysql57.tar

> **import container:** docker import mysql57.tar mysql57new

> **run import container:**  docker run --name mysql57-new -p 33080:3306 -e MYSQL_ROOT_PASSWORD=123456 -d mysql57new:latest  **/entrypoint.sh mysqld**  

> **save image to file:** docker save -o mysql57image.tar mysql:5.7

> **load image file:** docker load --input mysql57image.tar

##### 容器和宿主机之间文件互导

> **cp file from docker:** docker cp mytomcat:/usr/local/tomcat/conf /root/conf

> **cp file to docker:** docker cp /root/conf mytomcat:/usr/local/tomcat/conf 

##### 默认的 Images 路劲
> /var/lib/docker/

##### 查看 docker 容器资源使用统计信息
> docker stats

> docker top nginx

### 常用镜像脚本

##### Nginx
> docker run -p 80:80 -d --name mynginx -v /usr/data/nginx/html/:/usr/share/nginx/html nginx 

##### Maven
> docker run -it --rm -v /root/code:/usr/src/mymaven -v /root/.m2:/root/.m2  -w /usr/src/mymaven maven mvn clean -DskipTests=true package -P prod

##### Tomcat
> docker run -p 8080:8080 -d --name mytomcat -v /usr/data/tomcat/webapps/:/usr/local/tomcat/webapps/ -v  /usr/data/tomcat/logs:/usr/local/tomcat/logs -v /usr/data/tomcat/conf:/usr/local/tomcat/conf tomcat:8.5

> docker run -p 80:8080 -d --name mytomcat -v /usr/data/tomcat/webapps/:/usr/local/tomcat/webapps/ -v /usr/data/tomcat/logs:/usr/local/tomcat/logs -v /usr/data/tomcat/conf:/usr/local/tomcat/conf -v /usr/share/zoneinfo/Asia/Shanghai:/etc/localtime tomcat:8.5

##### Mysql
> docker run --name mysql57 -p 33060:3306 **-e MYSQL_ROOT_PASSWORD=123456** -d mysql:5.7 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci

> docker exec -it mysql57 /bin/bash

> /usr/bin/mysql -uroot -p123456

> CREATE USER 'fangzheng'@'%' IDENTIFIED BY 'fangzheng';

> create database fangzheng;

> GRANT all privileges ON fangzheng.*  TO 'fangzheng'@'%';

> flush privileges;

##### Redis
> docker run -p 6379:6379 -v /usr/data/redis:/data  -d redis:5.0 redis-server --appendonly yes
4

##### RabbitMQ
> docker run -d --hostname my-rabbit --name rabbit -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 -p 25672:25672 rabbitmq:management

##### zookeeper
> docker run --name myzk -p 2181:2181 -d zookeeper:3.4

##### prometheus
> docker run --name prometheus -d -p 9090:9090 prom/prometheus
 
