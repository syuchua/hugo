---
title: 基于github+jsdelivr实现免费图床
categories:
  - blog
tags:
  - GitHub图床
  - jsdelivr CDN加速
  - 免费图床
date: 2024-04-20T16:16:35+08:00
---

图床想必大家都不陌生了，无论是个人搭建博客或是其他站点、APP、小程序，还是企业级的应用，想要加速网站的访问，都离不开图床，但一款稳定好用的图床又往往要么是收费的，要么需要实名注册等各种流程，那有没有什么免费且方便的代替方案呢？

答案是显而易见的，使用GitHub的仓库作为图床，再经由jsdelivr的免费cdn加速，一款稳定方便的图床就诞生了，下面开始本期教程。

# 新建GitHub仓库

![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/%E6%96%B0%E5%BB%BA%E4%BB%93%E5%BA%93.jpg)

![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/%E4%BB%93%E5%BA%93.jpg)

# 生成一个token

  - 点击右上角用户头像-> 设置
  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/token.jpg)

  - 点击开发者设置
  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/develop.jpg)

  - 点击Personal acess tokens -> Generate new token
  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/newtoken.jpg)
  填写token描述，勾选repo即可，过期时间可以选择永不

  - 获取token秘钥
  注意，该秘钥仅显示一次，建议保存到一个找得到的地方，或者云盘，后面还会用到。

# 配置PicGo并使用jsdelivr的免费cdn加速

[下载PicGo](https://picgo-release.molunerfinn.com/2.4.0-beta.6/PicGo-Setup-2.4.0-beta.6-x64.exe)，安装好后开始对接图床

  - 设定仓库名：username/repositoies(username为github用户名，repositoies为仓库名，比方说用户名zhangsan，仓库名为jsdelivr，就填写zhangsan/jsdelivr)

  - 设定分支名：main

  - 设定token：刚刚复制的token

  - 指定存储路径：填写打算存放图片的位置，比方说img/，代表在该仓库下创建一个铭文img的文件夹，图片将保存在这个文件夹中。

  - 设定自定义域名：它的的作用是，在图片上传后，PicGo 会按照自定义域名+上传的图片名的方式生成访问链接，放到粘贴板上，因为我们要使用 jsDelivr 加速访问，所以可以设置为https://cdn.jsdelivr.net/gh/用户名/图床仓库名@分支 #默认是main

  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/picgo.jpg)

接下来就可以愉快的使用免费图床啦！

# END


