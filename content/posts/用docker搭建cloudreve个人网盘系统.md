---
title: 用docker搭建cloudreve个人网盘系统
categories:
  - blog
tags:
  - docker
  - 个人网盘
  - cloudreve
abbrlink: cf51
date: 2024-04-06 18:55:04
---

## 成品展示
![](https://cloud.yuchu.me/f/x2ux/cloudreve.jpg)

## 准备工作
   - 一台vps（若只是个人使用的话也可以是任意一台手机或电脑）
   - 安装docker和docker-compose
   - 一个域名（同第一条，仅个人使用可忽略，但你也不想每次登录都先输一大串IP地址吧）

 vps的话，国内推荐阿里云和腾讯云，都有学生优惠，能白嫖几个月，其他诸如雨云等没试过，不做推荐。国外有微软的azure，学生免费一年，亚马逊（容易被封且需要绑信用卡，有被反撸的可能），还有谷歌云，但也是需要信用卡，有被反撸的风险，digitalorcean通过GitHub学生包可以白嫖200美元额度，还有老牌付费vps厂家如vultr，新晋便宜vps商nackhard等，就不一一列举了。

 域名注册国内可以用阿里腾讯，国外namesilo,name.com,cloudflare等都还不错，便宜的域名一年十块左右就能搞定了（tips：纯数字域名，尤其是6位及以上的，一般会比字母域名便宜很多）

<!-- more -->

## 新机到手，更新环境
 
`apt update -y  && apt upgrade -y && apt install -y curl wget sudo socat vim`

## 安装docker和docker-compose

```
curl -fsSL https://get.docker.com | sh      # 下载最新的官方脚本

curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose      #给予对应目录权限
```

# Cloudreve和离线下载构建

## 创建目录，创建文件

```
cd /home/ && mkdir cloud && cd cloud && mkdir -vp cloudreve/{uploads,avatar} && touch cloudreve/conf.ini && touch cloudreve/cloudreve.db && mkdir -p aria2/config && mkdir -p data/aria2 && chmod -R 777 data/aria2
# cloud即根目录，aira2为离线下载工具，可按需求酌情考虑是否创建
```

## 创建compose配置文件

`cd /home/cloud/ && vim docker-compose.yml`

## 在docker-compose中配置Cloudreve代码

```
version: "3.8"

services:

  cloudreve:

    container_name: cloudreve

    image: cloudreve/cloudreve:latest

    restart: unless-stopped

    ports:

      - "5212:5212"

    volumes:

      - temp_data:/data

      - ./cloudreve/uploads:/cloudreve/uploads

      - ./cloudreve/conf.ini:/cloudreve/conf.ini

      - ./cloudreve/cloudreve.db:/cloudreve/cloudreve.db

      - ./cloudreve/avatar:/cloudreve/avatar

    depends_on:

      - aria2

   aria2:

    container_name: aria2

    image: p3terx/aria2-pro

    restart: unless-stopped

    environment:

      - RPC_SECRET= DOWNLOAD

      - RPC_PORT=6800

    volumes:

      - ./aria2/config:/config

      - temp_data:/data 

volumes:

  temp_data:

    driver: local

    driver_opts:

      type: none

      device: $PWD/data

      o: bind
```

## 运行

`cd /home/cloud && docker-compose up -d`

容器正常启动后就可以用服务器IP+端口号5212进入网盘设置界面了

![](https://cloud.yuchu.me/f/w0ty/cloudreve_login.jpg)

## 查询初始登录账号密码

`docker-compose logs cloudreve`

一般初始账号为`admin@cloudreve.org `，密码为`admin`

## 基础配置

点击头像-管理面板，找到用户组/用户,点右边的编辑，可以进行一些基础的配置，包括但不限于分配空间，更改账号密码等。
![](https://cloud.yuchu.me/f/rRi2/cloudreve_edit.jpg)
![](https://cloud.yuchu.me/f/vlsA/cloudreve_user.jpg)

添加存储策略，有详细的文档，这里就不赘述了，个人推荐onedrive，主要是免费。当然如果你的vps空间足够或者不打算存太多东西的话，默认的本机存储也可以。

### 离线下载节点配置
```
RPC 服务器地址 => http://aria2:6800
RPC 授权令牌 => DOWNLOAD
节点下载目录 => /data
```

# NGINX反向代理到域名

配置dns解析：![](https://cloud.yuchu.me/f/qjhX/dns.jpg)

## 创建nginx目录结构

```
mkdir -p /home/nginx

touch /home/nginx/nginx.conf

mkdir -p /home/nginx/certs
```

## 申请证书

```
curl https://get.acme.sh | sh

~/.acme.sh/acme.sh --register-account -m xxxx@gmail.com   #随便一个邮箱就行


~/.acme.sh/acme.sh --issue -d yourip.com --standalone     # yourip.com改为你的域名

```

## 下载证书到本地

```
~/.acme.sh/acme.sh --installcert -d yourip.com --key-file /home/nginx/certs/key.pem --fullchain-file /home/nginx/certs/cert.pem
```

## 进入目录配置nginx

`cd /home/nginx/ && nano nginx.conf`

## 反向代理配置，代理指定IP加端口

```
events {

    worker_connections  1024;

}



http {



  client_max_body_size 1000m;  

  #上传限制参数1G以内文件可上传



  server {

    listen 80;

    server_name yourip.com;         # 同理，替换为你的域名

    return 301 https://$host$request_uri;

  }



  server {

    listen 443 ssl http2;

    server_name yourip.com;         # 替换域名

    ssl_certificate /etc/nginx/certs/cert.pem;

    ssl_certificate_key /etc/nginx/certs/key.pem;

    location / {

      proxy_pass http://127.0.0.1:5212;

      proxy_set_header Host $host;

      proxy_set_header X-Real-IP $remote_addr;

      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    }

  }

}
```
## 下载部署容器

```
docker run -d --name nginx -p 80:80 -p 443:443 -v /home/nginx/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/certs:/etc/nginx/certs -v /home/nginx/html:/usr/share/nginx/html nginx:latest

```

## 查看运行状态

`docker ps -a`
![](https://cloud.yuchu.me/f/1lcq/mirror.jpg)
可以看到容器都是正常运行的

之后就可以用域名访问你的cloudreve网盘了！

