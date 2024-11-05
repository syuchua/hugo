---
title: 3分钟搭建一个ChatGPT镜像站，非api，支持官网账号登录
categories:
  - blog
tags:
  - chatgpt镜像站
date: 2024-04-14T17:30:22+08:00
---

本篇基于github上的开源项目[https://github.com/xyhelper/chatgpt-mirror-server](https://github.com/xyhelper/chatgpt-mirror-server)旨在通过简单的操作搭建一个ChatGPT的个人镜像，绕过cloudflare，提升访问速度，且没有烦人的机器人验证步骤，可本地搭建，也可部署在服务器上，通过登录官网账号的方式，非api模式，提高回答准确性。同时，基于共享账号的普遍存在，可以实现成本远低于api模式的GPT4和GPTs使用。

# 前置条件
 - 一台至少 2 核 2G 内存的服务器,推荐使用香港、新加坡、日本地区的服务器,可以 兼顾国内访问。(本地搭建的话可忽略)
- 安装了 docker 和 docker-compose
- 安装了 git
- 有官网直登账号(不支持谷歌、微软、苹果等第三方登陆账号)
- 一个域名（用于反代，可忽略）

# 准备工作

打开服务器，输入：
```
apt update -y && apt upgrade -y && apt install -y vim wget curl   #出现报错可能是权限问题，用`sudo su`提权至root用户
```
# 自动部署

直接下载执行官方的一键部署脚本
```
curl -sSfL https://raw.githubusercontent.com/xyhelper/chatgpt-mirror-server-deploy/master/quick-install.sh | bash
```

# 手动部署

```
git clone --depth=1 https://github.com/xyhelper/chatgpt-mirror-server-deploy.git chatgpt-mirror                 # 克隆项目地址
cd chatgpt-mirror              #进入根目录
./deploy.sh                    #启动部署脚本，等待拉取docker镜像
```

# 进入管理后台进行基础配置

访问`http://服务器ip地址:8200/xyhelper`

默认用户名`admin`，密码`123456`
![](https://cloud.yuchu.me/f/8QCY/chat-mirror.jpg)

进入后台左边点击工作台-账号管理，添加自己的ChatGPT官网账号勾选启用（plus用户切记勾选plus），再进入用户管理，添加一个用户，过期日期和user-token都可以自定义，保存。复制user-token，返回首页，粘贴。

### 一个小细节

如果你是plus用户，或者使用的是共享账号，想把这个镜像站分享给其他人，又想省去每次都输入token的麻烦，不妨自定义ygusertoken，比方说我设置的是`1145141919810hhhhaaa`，然后把这个链接：`http://yourip:8200/login_token?access_token=1145141919810hhhhaaa`分享出去，这样就·不用·每次都登录了。

# 配置nginx反代

新建nginx配置目录：
```
mkdir -p /home/nginx

touch /home/nginx/nginx.conf

mkdir -p /home/nginx/certs
```

申请证书
```
curl https://get.acme.sh | sh

~/.acme.sh/acme.sh --register-account -m xxxx@gmail.com   #随便一个邮箱就行


~/.acme.sh/acme.sh --issue -d yourip.com --standalone     # yourip.com改为你的域名
```

下载证书到本地
```
~/.acme.sh/acme.sh --installcert -d yourip.com --key-file /home/nginx/certs/key.pem --fullchain-file /home/nginx/certs/cert.pem
```
填写nginx配置文件
`cd /home/nginx/ && vim nginx.conf`

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

      proxy_pass http://127.0.0.1:8200;

      proxy_set_header Host $host;

      proxy_set_header X-Real-IP $remote_addr;

      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    }

  }

}
```

拉取部署nginx容器
```
docker run -d --name nginx -p 80:80 -p 443:443 -v /home/nginx/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/certs:/etc/nginx/certs -v /home/nginx/html:/usr/share/nginx/html nginx:latest
```

之后就可以用域名访问啦！