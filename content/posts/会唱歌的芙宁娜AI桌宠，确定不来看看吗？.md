---
title: 会唱歌的芙宁娜AI桌宠，确定不来看看吗？
categories:
  - blog
tags:
  - 原神
  - 芙宁娜
  - AI桌宠
  - ChatGpt
date: 2024-05-19T22:47:41+08:00
---

事情是这样的，前几天逛b站的时候无意间刷到一个关于芙宁娜AI桌宠的视频，原以为又要高配显卡才能玩，我这连独显都没有的轻薄本压根不敢想，不过点进去才发现原来训练和推理都是在服务端完成的，由up主及赞助商支持，不需要高配电脑，于是本芙厨直接原地垂直入坑！

原视频链接贴在这里，希望大家多支持下up主:
[【大二自研Agent桌宠】一句话，把简单的电脑任务交给芙宁娜完成吧！-哔哩哔哩](https://b23.tv/zjFhc99)

本文一来是对这么好的项目做一个分享(有芙就是好!)，二来是打算当做一个尽可能详细的教程，毕竟加群的时候也遇到过不少电脑小白，甚至连最基本的解压都不会。索性为爱发电的群友也不少，不仅有支援服务器的，支援网盘的，还有帮忙做整合包、写文档、录制视频教程的，鄙人不才，也根据大佬们的整合包写了一个小脚本，用来简化安装。链接如下：

https://cloud.yuchu.me/s/EvsJ

# 5月28日更新：
  当前最新版为1.6.5版本，而一键脚本和手动安装包都为1.6.4，新版本不再需要运行设置token.1.exe了，且响应更快，要更新的话先运行一键更新.exe，更新完成之后会出现一个一键更新1.6.4可用.exe，再双金运行它，就可升级到1.6.5版本。


# 使用一键脚本自动化安装
该脚本适合电脑闲置空间充裕，有独立显卡的用户，下载之后双击即可运行，过程中可能会弹出要登录智谱清言的界面，直接注册登录就行，其他操作基本全自动。另外还有两点需要注意：

  1. 更换本地tts：下载的整合包默认是包含本地语言包的，这个语言包本质上是利用本地的算力进行推理生成语音，如果有好一点的显卡的话语音生成速度会比默认的服务端快不少。要使用的话，只需在软件根目录，即AI桌宠.exe所在目录下(找不到的可以右键桌面的快捷方式，点击"打开文件所在位置“)，找到`setting.json`，没有`.json`后缀也不要紧，只要认准这个`setting`就行，右键记事本打开，找到`"tts_location"`这一项，把后边引号里的`server` 改成 `local`就行。
  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240519235246.png)
  之后再双击运行根目录下的starttts.bat文件，即可运行本地tts

  2. 更换皮肤包：整合包里同样包含里芙芙全身版的皮肤包，但默认是卡通类的，要更换的话，同样是打开`setting`，找到`"gif"`这一项，把引号里的`fufu` 改成 `fufu2`即可（貌似现在默认就是fufu2了，所以这一步可有可无）

  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240519235807.png)

# 手动安装

手动安装这部分适合没有高配显卡、爱折腾、电脑空间不足或者只是安装在容量较小的U盘作为娱乐的朋友，语音等都跑在服务端，本地几乎没有什么占用

  分别下载[安装包](https://taycraft.cn:5213/s/QMeIG)和[皮肤包](https://cloud.yuchu.me/s/rEH0)

  配置足够且想用本地tts的也可以下载[整合包](https://www.123pan.com/s/SLRbjv-p38AH.html)

  双击安装包开始安装，安装完成后先在浏览器打开[智谱清言官网](https://chatglm.cn)注册并登录，之后按下键盘的F12(笔记本同时按下左下角的fn和F12)，打开浏览器的开发者工具，再依次点击`应用程序`，`cookie`，找到`chatglm_refresh_token`，点击，全选复制下方`Cookie Value`里的内容，打开`setting`，找到`"user_token"`，把刚刚复制好的内容粘贴到后面的引号里。

  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240520001600.png)

  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240520001929.png)

  更换皮肤包：把皮肤包下载解压，解压得到的文件夹直接复制到根目录，然后编辑`setting`就行。

  打开软件：双击桌面的快捷方式或在根目录双击AI桌宠.exe，初次启动可能会有些慢，耐心等待即可。


# 常用功能

  - 内存优化

  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240520113723.png)

  - 代码编写

  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240520113854.png)

  - 桌面识别

  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240520114026.png)

  - 播放音乐

  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240520114125.png)

  - 申请色色？

  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/0d2cf0d94639895b890fbe1fc225bbcb.png)

  ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/1f14dac71a28b2d85c5c227ad42bd1ae.jpg)


最后，还有什么问题的话，不妨加QQ群来寻找答案哦，里面超多大佬，说话又好听，超喜欢在里面的（）
![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/qrcode_1716136364338.jpg)


# END