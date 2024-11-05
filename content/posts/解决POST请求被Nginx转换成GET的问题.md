---
title: 解决POST请求被Nginx转换成GET的问题
categories:
  - blog
tags:
  - Nginx
  - POST
  - Web
date: 2024-07-11T23:28:04+08:00
---

前几天把自己的一个项目扔到了服务器上，用nginx+域名反代了一下ip+端口，测试却发现请求失败，日志里的报错信息显示不支持的请求类型

#### waht?

明明本地跑的好好的啊，一番排查之后才找到问题，我这个项目实际上是一个api接口，只接受POST请求，但nginx的配置文件里有一个return 301步骤把http请求都转发给了https，转发之后，POST请求就变成了GET，查了一些资料，发现原来只要把return301 改成307就好了

比方说我原本的nginx.conf是这样的：
```
server {

    listen 80;

    server_name yourip.com;        

    return 301 https://$host$request_uri;

  }



  server {

    listen 443 ssl http2;

    server_name yourip.com;         

    ssl_certificate /etc/nginx/certs/cert.pem;

    ssl_certificate_key /etc/nginx/certs/key.pem;

    location / {

      proxy_pass http://127.0.0.1:11451;

      proxy_set_header Host $host;

      proxy_set_header X-Real-IP $remote_addr;

      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    }

  }
```

只需要改成：
```
server {

    listen 80;

    server_name yourip.com;         # 同理，替换为你的域名

    return 307 https://$host$request_uri;

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
```
就可以了。