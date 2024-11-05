---
title: 'oh-my-posh:基于WindowsTerminal的powershell美化教程'
categories:
  - blog
tags:
  - oh-my-posh
  - Windows Terminal
  - powershell
  - 终端美化
date: 2024-05-07T22:05:04+08:00
---
今天来更一篇关于Windows终端美化的教程，先放个成品：

![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/terminal.jpg)

实现的效果主要有终端半透明、亚克力材质、背景图片以及终端主题。


# 安装PowerShell 7

相比于 Windows 默认提供的 Powershell，Powershell 7 拥有以下几个优势：

  1. 支持跨平台使用，增强了其灵活性和适用范围。
  2. 提供了更优越的性能表现。
  3. 处于持续更新状态，保证了功能的不断完善和安全性的提升。
  4. 简而言之，Powershell 7 是 Powershell 的升级版。

  官网地址：

    PowerShell 文档 - PowerShell

  Github 地址：

    https://github.com/PowerShell/PowerShell

  下载地址：

    https://github.com/PowerShell/PowerShell/releases/
    https://learn.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell-on-windows

  选择合适自己的安装即可。

# 安装Windows Terminal

直接去微软商店下载即可：https://apps.microsoft.com/detail/9n0dx20hk701?hl=zh=CN&gl=CN

# 安装winget

一般在Windows10 高于 1709 (Build 16299) 的版本和 Windows11上以及预装了winget，可以通过在命令行输入
```
winget --version
```
确认，如果没有的话可以在微软商店安装：https://apps.microsoft.com/detail/9nblggh4nns1?hl=zh-CN&gl=CN


# 修改透明度、亚克力材质和背景图片

旧版的Windows Terminal需要修改json的配置文件，但新版可以直接在设置界面可视化开启
点击设置-外观
![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/terminal_set.png)

默认值-外观-背景图像路径，可以自定义图片作为终端背景，再往下拉一点，设置图像不透明度，个人喜欢色调偏暗的，就设定为20%了。
![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240507223022.png)

再往下拉一点，就是设定终端界面的不透明度和亚克力材质了。
![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240507223442.png)

如果想要差异化的，还可以单独设定不同终端的外观。
![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240507223621.png)

# 安装oh-my-posh

直接winget就可以安装

```
winget search oh-my-posh
```

如果出现两个结果，一个msstore的一个winget的，随便选一个就行。

## 设置oh-my-posh主题

在PowerShell中输入 
```
code $profile    # 没有vscode的话也可以输入notepad $profile 使用记事本打开
```

在打开的文件中输入这行命令：
```
oh-my-posh init pwsh --config $env:POSH_THEMES_PATH\peru.omp.json | Invoke-Expression
```

其中的peru.omp.json即主题配置，可以自行修改，前往`C:\Users\<用户名>\AppData\Local\Programs\oh-my-posh\themes`目录下即可查看不同配置，可以依次试用，选一个你喜欢的。

# END