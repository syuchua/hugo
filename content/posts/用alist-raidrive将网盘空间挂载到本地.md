---
title: 用alist+raidrive将网盘空间挂载到本地
categories:
  - blog
tags:
  - alist
  - raidrive
  - webdav
abbrlink: 48fd
date: 2024-04-10 22:59:40
---
## 成品展示
![](https://cloud.yuchu.me/f/2Bf8/alist1.jpg)
![](https://cloud.yuchu.me/f/38h2/alist2.jpg)

### 原理
通过alist挂载网盘，然后用RaiDrive的webdav功能将网盘内容映射到本地，可以直接对网盘内容进行增删改查，实现近似本地词牌的功能。

## alist下载安装

1. [alist官方下载](https://github.com/alist-org/alist/releases/download/v3.32.0/alist-windows-amd64.zip)
GitHub访问较慢的也可以从该链接下载:

[alist下载](https://cloud.yuchu.me/s/jRfM)

2. 将下载的压缩包解压到指定的目录下
3. 此目录下运行cmd，输入 `.\alist.exe server`开始安装程序
4. 当看到输出`start server@0.0.0.0:5244`且后续无报错时，说明安装完成。初始会指定alist用户名为admin，并随机一个密码，该密码会在安装时展示，也可以使用`.\alist.exe admin`查询
5. 安装成功后，浏览器打开 `127.0.0.1:5244`即可访问alist管理界面。输入刚刚的用户名和密码，点击`管理`，可以进入管理界面修改用户名和密码。
6. 关闭该进程可以在cmd界面按下`Crtl+C`，之后重新启动以及更改密码可以在根目录使用如下命令
```
alist start
alist admin set your_passwd   #your_passwd改为你要更改的密码
```

## alist挂载网盘
*点击管理界面的存储-添加-驱动，即可添加指定网盘，目前已支持几十种*
这部分内容在alist的官方文档里已经足够详细，这里以阿里云盘为例，其他的不再赘述，附上官方文档链接，供大家参考
[alist官方文档/添加存储](https://alist.nn.ci/zh/guide/drivers/)

 **驱动** 选择`阿里云盘open`
 
**挂载路径** 可自定义，比方说`/aliyun`

**获取刷新令牌**
刷新令牌 根据文档里的说明进行获取，访问下面地址，按下面图片操作

[地址](https://alist.nn.ci/tool/aliyundrive/request)
![](https://cloud.yuchu.me/f/5Ds6/alist_aliyun.png)

复制 token，回到到刷新令牌处粘贴，点击保存

![](https://cloud.yuchu.me/f/65ty/alist-aliyun.png)

## 配置alist开机自启

在alist根目录中新建文版文档，填入以下内容:
```
Set ws = CreateObject("Wscript.Shell")
ws.run "alist.exe server",vbhide
```
保存，更改后缀名为vbs，右键-创建快捷方式，将快捷方式移动到开机自启的目录`C：\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp`下。该目录可以使用`Crtl+R`，输入`shell:Common Startup`回车后打开。

## raidrive安装与配置

直接访问官网下载对应版本，
[raidrive官网下载](https://app.raidrive.com/download/raidrive/release/RaiDrive_2023.9.90_x64.msi)

之后一步步安装即可

### 网盘映射
raidrive界面-添加-NAS-WebDAV

**虚拟驱动器** 第一行选择喜欢的盘符字母，默认为Z

**地址** 取消勾选 ，第一行第一格IP输入 127.0.0.1(或localhost) 第二格端口输入 5244

第二行输入`/dav`即可，这样alist挂载的所有网盘都会被映射到本地，也可以跟上特定后缀，比方说你的阿里云盘被挂载在alist的`/aliyun`目录下，那第二行就可以改成`/dav/aliyun`，这样，就只有阿里云盘被映射到本地了

第三行输入账户密码，点击连接即可。
![](https://cloud.yuchu.me/f/74uZ/raidrive.png)

最后如果是这样的就代表没问题了，可以打开`我的电脑`查看是否有一个绿色的webdav出现。
![](https://cloud.yuchu.me/f/4Oil/raidrive.jpg)

# END