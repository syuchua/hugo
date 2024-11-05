---
title: 基于hexo和GitHub page搭建个人博客
tags:
  - hexo
  - github page
  - 个人博客
categories:
  - blog
date: 2024-03-28T21:17:07+08:00
---

### 成品展示
![成品展示](https://cloud.yuchu.me/f/JWcK/my_blog.png)

## 使用工具：
  - git
  - github
  - hexo
  - vscode( 任意具备markdown编辑能力的工具均可)

## git安装

[git官网下载](https://git-scm.com/download/win)
选择电脑对应版本安装即可（一般为64位）

<!-- more -->

## node、npm安装

[node.js官网](https://nodejs.org/en/download/)


## 当然，你也可以直接用winget安装，省的去官网下载
关于winget的使用，可以看完另一篇博客：[winget的基础配置与使用](https://blog.yuchu.me/posts/8eff/)
```
winget install Git.Git
winget install OpenJS.NodeJS.LTS
```

# hexo创建个人博客

## 安装hexo

### 1.在任意目录下新建hexo文件夹
  - 打开该文件夹，空白处单击鼠标右键:Git Bash Here
  - 在打开的git bash 窗口中依次输入以下命令：
  ```
  npm install hexo-cli -g
  hexo init blog
  cd blog
  npm install
  hexo server
  ```
全部执行完成后，hexo目录下会出现一个新的blog文件夹，里面就是存放博客的地方
 - 然后输入这两条命令
 ```
 hexo g
 hexo s
 ```
完成后若显示诸如`http://localhost:4000`的内容，则说明配置成功。
打开浏览器，地址栏输入上面的地址，即可进入初始博客的预览界面，它长这样：

![](https://cloud.yuchu.me/f/EGTp/hexo_test.png)

为什么说是预览界面呢，因为她现在仅仅只是部署在你电脑本地的一个离线版本，只能在你自己的电脑上看见，想要正式发布公开，你需要一台拥有公网IP的服务器，但还有一个更简单的方法，即借助GitHub page的ssh功能实现对外访问。

## GitHub创建仓库

  - 首先创建一个GitHub账号，然后在个人主页新建仓库，仓库名为username.github.io,username即你的用户名。
  - 回到gitbash窗口，按`Ctrl+C` 关闭4000端口的预览，输入以下命令：
  ```
  git config --global user.name "yourname"    # yourname改为GitHub用户名
  git config --global user.email "youremail"  #youremail改为注册GitHub所用的邮箱
  ssh-keygen -t rsa -C "youremail"            #创建ssh连接
  ```
  - 之后会提示你已经完成ssh创建，接着找到该路径下的这两个文件：

  ![](https://cloud.yuchu.me/f/B1I8/git_config.png)

  把id_rsa.pub里的内容复制下来，打开GitHub的设置界面（点击头像-setting），找到SSH and GPG keys,x新建一个ssh key，粘贴进key中，title随便填一个就行，保存。

  ![](https://cloud.yuchu.me/f/DJS3/github_ssh.png)

## hexo部署到GitHub

  - 在blog文件夹下找到_config.yml,这是博客的配置文件,在这里可以修改博客的一些基本信息，打开它，找到下面这段内容,在最底下：
  ```
  # Deployment
  ## Docs: https://hexo.io/docs/one-command-deployment
  deploy:
    type: ''
  ```
  修改deploy部分：
  ```
  deploy:
    type: git
    repo: https://github.com/username/username.github.io.git   #username改为用户名
    branch: master
  ```
  - 另外找到第十六行，修改url为 `https://username.github.io`
 
  - 完成后保存文件并退出，在gitbash中输入：`npm install hexo-deployer-git --save`,安装git部署工具。
  - 最后输入以下三行命令：
  ```
  hexo clean           #清除缓存
  hexo g               #生产静态文章（博客）
  hexo d               #部署
  ```
  - 当然，如果仅是在本地查看效果，第三行命令也可以换成hexo s
  部署过程中可能会出现warning警告，但不要紧，只要最后呈现这样的内容，就表示成功了：
  ![](https://cloud.yuchu.me/f/AQHE/hexo-d.png)
  之后，你就可以 `username.github.io`访问到你的博客了，且可以被任何人访问到。

## 常用命令

在根目录打开gitbash：

```
hexo new "博客名称"  #新建博客
hexo clean          #清除缓存和静态文件
hexo g              #生产静态文件
hexo s              #生成离线版本
hexo d              #部署
```

## 添加插件

### 主题
我使用的是next，一款简洁美观的主题，功能强大，可拓展性强，在gitbash中下载：
`git clone https://github.com/next-theme/hexo-theme-next themes/next`
下载完成后，在blog下找到themes文件夹-next,打开主题的_config.yml文件，找到如下部分，大概在100行左右：
```
# Schemes
#scheme: Muse
#scheme: Mist
#scheme: Pisces
#scheme: Gemini
```
这代表了四种不同风格的next主题，任选一个取消注释即可。
之后回到根目录的_config.yml文件，找到theme选项，输入next
![](https://cloud.yuchu.me/f/znF4/hexo_theme.png)

## 博客基本信息
在blog下找到_config.yml并打开，配置站点信息
![](https://cloud.yuchu.me/f/yXCL/hexo_site.png)
注意，语言要改成中文：zh-CN,其他的可以按个人喜好更改。

# END