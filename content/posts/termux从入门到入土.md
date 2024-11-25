---
title: 'Termux从入门到入土'
categories:
    - blog
tags:
    - termux
date: 2024-11-25T20:12:52+08:00
---

# Termux 是什么

Termux 是一个运行在 Android 系统上的终端模拟器和 Linux 环境应用。它允许你在不需要 root 权限的情况下，在手机或平板上运行熟悉的 Linux 命令和工具。通过 Termux，你可以在移动设备上进行开发、编译、运行服务器等操作，极大地扩展了 Android 设备的功能。

# 下载与安装

Termux 可以通过以下方式下载和安装：

- [Google Play](https://play.google.com/store/apps/details?id=com.termux)

  如果你可以访问 Google Play 商店，可以直接搜索 Termux 进行下载安装。但需要注意的是，Google Play 上的版本可能不是最新的。

- [GitHub](https://github.com/termux/termux-app/releases)

  前往 Termux 的 GitHub 仓库，进入 Releases 页面，下载最新的 APK 文件进行安装。

- [F-Droid](https://f-droid.org/en/packages/com.termux/)

  F-Droid 是一个开源应用程序商店，你可以在上面找到 Termux 的最新版。

# 基础配置

## 基本权限、换源、更新软件包和常用工具的下载

### 获取存储权限

安装完成后，首次运行 Termux，会自动安装基本环境。为了访问设备的存储，需要授予存储权限：

```bash
termux-setup-storage
```

执行后，会在 `~/storage` 目录下创建符号链接，方便访问内部存储。

### 更换国内源

为了提升软件包的下载速度，建议更换为国内源。以下以清华大学的镜像源为例：

编辑 `sources.list` 文件：

```bash
echo "deb https://mirrors.tuna.tsinghua.edu.cn/termux/termux-packages-24 stable main" > $PREFIX/etc/apt/sources.list
```

当然，还有一个更简单的方法，直接输入`termux-change-repo`，通过一个简单的GUI界面切换源（Google play的最新版似乎已不再支持）

更新软件包列表：

```bash
pkg update
```

### 升级软件包并安装常用工具

升级已安装的软件包：

```bash
pkg upgrade
```

安装常用工具：

```bash
pkg install vim git curl wget zsh
```

## 美化

为了提升终端的美观度和使用体验，可以进行以下美化操作。

### 安装 Oh My Zsh

1. 安装 Zsh：

   ```bash
   pkg install zsh
   ```

2. 设置 Zsh 为默认 shell：

   ```bash
   chsh -s zsh
   ```

3. 安装 Oh My Zsh：

   ```bash
   sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
   ```

### 安装主题和插件

#### 安装 Powerlevel10k 主题

```bash
git clone --depth=1 https://gitee.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
```

编辑 `~/.zshrc` 文件，设置主题：

```bash
ZSH_THEME="powerlevel10k/powerlevel10k"
```

#### 安装常用插件

```bash
# 自动补全
git clone https://gitee.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions

# 语法高亮
git clone https://gitee.com/zsh-users/zsh-syntax-highlighting $ZSH_CUSTOM/plugins/zsh-syntax-highlighting
```

编辑 `~/.zshrc` 文件，添加插件：

```bash
plugins=(git zsh-autosuggestions zsh-syntax-highlighting)
```

重新加载配置：

```bash
source ~/.zshrc
```

之后正常跟着引导走完就行

### 修改启动问候语

不喜欢默认的启动问候语可以换成自定义的

```bash
vim $PREFIX/etc/motd
```

比方说我就换成了一个ASCII艺术字

```bash
 _____                              
|_   _|__ _ __ _ __ ___  _   ___  __
  | |/ _ \ '__| '_ ` _ \| | | \ \/ /
  | |  __/ |  | | | | | | |_| |>  < 
  |_|\___|_|  |_| |_| |_|\__,_/_/\_\
                                    
```



## 开发

Termux 支持多种编程语言，可以在移动设备上搭建开发环境。

### 安装 Python 开发环境

```bash
pkg install python

# 升级 pip
pip install --upgrade pip

# 安装常用库
pip install requests numpy pandas
```

### 安装 Node.js 开发环境

```bash
pkg install nodejs

# 安装 npm
npm install -g npm

# 安装常用包
npm install -g express
```

### 安装编译器和构建工具

```bash
pkg install gcc clang make cmake
```

### 使用 ADB

Termux 支持使用 ADB（Android Debug Bridge）工具，通过无线调试对设备进行管理和调试。借助 ADB 权限，您可以访问在 Android 11 及以上版本中被限制访问的 `Android/data` 目录。

#### 安装 ADB 工具

首先，在 Termux 中安装 ADB 工具：

```bash
pkg install android-tools
```

#### 启用无线调试

在 Android 设备上：

1. **启用开发者选项**：
   - 进入 **设置** -> **关于手机**，连续点击 **版本号** 七次，直到出现提示信息。

2. **开启无线调试**：
   - 返回 **设置** -> **系统**（某些设备可能直接在设置中）-> **开发者选项**。
   - 向下滚动，找到并启用 **无线调试**。

3. **获取连接信息**：
   - 点击 **无线调试**，选择 **配对设备** 或 **配对使用配对码**，获取设备的 IP 地址和端口号。

#### 连接到设备

在 Termux 中，使用 ADB 连接到设备：

```bash
adb connect <设备IP地址>:<端口号>
```

例如：

```bash
adb connect 192.168.1.100:5555
```

连接成功后，您可以查看已连接的设备：

```bash
adb devices
```

输出应包含您的设备，状态为 `device`。

#### 访问 `Android/data` 目录

连接成功后，使用 ADB Shell 进入设备：

```bash
adb shell
```

在 ADB Shell 中，切换到 `Android/data` 目录：

```bash
cd /sdcard/Android/data
```

您现在可以执行诸如查看文件、复制文件等操作。例如，列出目录内容：

```bash
ls
```

#### 退出 ADB Shell

完成操作后，输入 `exit` 退出 ADB Shell：

```bash
exit
```

#### 断开连接

如果您需要断开与设备的连接：

```bash
adb disconnect
```

#### 注意事项

- **安全风险**：访问系统受限目录可能会带来安全风险，请谨慎操作，避免删除或修改关键文件。

- **权限限制**：某些设备和操作系统版本可能对 ADB 的功能有限制，实际效果可能有所差异。

- **同一网络**：确保您的 Android 设备和 Termux 处于同一局域网络下，以便无线连接正常工作。


## 进阶之 proot 和 proot-distro

通过 proot，可以在 Termux 中模拟完整的 Linux 发行版环境，运行更多的软件,而proot-distro则支持直接安装不同的linux发行版

### 安装 proot 和 proot-distro

```bash
pkg install proot proot-distro
```

使用proot模拟linux文件系统：

```bash
termux-chroot
```
输入这行命令后会卡一两秒，然后就可以像linux一样查看本地的 /etc, /var, /proc等目录以及目录下的内容了。当然，修改还是需要root权限，如果你的手机已经root了，可以用`tsu`，你可以理解为它是一个termux版的`su`

```bash
pkg install tsu
```

### 安装 Linux 发行版

可以先查看一下可用的发行版有哪些：

```bash
proot-distro list
```

![alt text](https://img.yuchu.me/file/1732545561357_1000036132.jpg)

例如，安装 Ubuntu：

```bash
proot-distro install ubuntu
```

### 进入发行版环境

```bash
proot-distro login ubuntu
```

在这个环境中，你可以像在真正的 Ubuntu 系统中一样操作。

### 退出发行版环境

```bash
exit
```

### 安装 ARM64 架构的 Debian 包

Termux 还支持安装适用于 ARM64 架构的 Debian 包，这使得你可以在 Termux 中安装更多的软件，进一步扩展其功能。

#### 安装 `dpkg` 工具

首先，确保已经安装了 `dpkg` 工具：

```bash
pkg install dpkg
```

#### 下载 `.deb` 包

你可以从可信赖的来源下载适用于 ARM64 架构的 `.deb` 包。例如，从官方 Debian 仓库或其他第三方提供的下载链接。

#### 安装 `.deb` 包

使用 `dpkg` 命令安装 `.deb` 包：

```bash
dpkg -i package_name.deb
```

如果安装过程中遇到依赖关系问题，可以使用以下命令来修复：

```bash
apt install -f
```

#### 注意事项

- **兼容性**：并非所有的 Debian 包都能在 Termux 中正常运行，因为 Termux 的环境与标准的 Linux 发行版有所不同。
- **依赖关系**：由于 Termux 的库和路径可能与软件包的预期环境不一致，可能会导致依赖关系无法满足。
- **安全性**：请从可信赖的来源获取 `.deb` 包，避免安装存在安全风险的软件。
- **权限问题**：某些软件可能需要特定的系统权限，在 Termux 中可能无法实现。

#### 示例

例如，安装 `htop` 工具：

```bash
# 下载 htop 的 ARM64 架构版本
wget http://ftp.us.debian.org/debian/pool/main/h/htop/htop_2.2.0-1_arm64.deb

# 安装 htop
dpkg -i htop_2.2.0-1_arm64.deb

# 修复依赖关系
apt install -f
```

#### 卸载软件包

如果需要卸载已安装的 `.deb` 包，可以使用以下命令：

```bash
dpkg -r package_name
```

### 使用 Wine 和 MoBox 模拟 Windows 环境

除了使用 proot 容器运行 Linux 系统外，Termux 还可以通过 Wine 或者封装度更高的 MoBox 来模拟 Windows 环境，甚至能够运行部分 Windows 应用程序和游戏。

#### 安装 Wine

**Wine** 是一个兼容层，允许在类 Unix 系统上运行 Windows 应用程序。以下是在 Termux 中安装和配置 Wine 的步骤：

1. **安装必要的仓库和软件包**

   首先，添加 `x11-repo` 和 `unstable-repo` 仓库，然后安装 Wine 和相关工具：

   ```bash
   pkg install x11-repo
   pkg install unstable-repo
   pkg install wine
   pkg install tigervnc
   pkg install mesa
   ```

2. **配置显示环境**

   设置显示变量，以便 Wine 使用正确的显示输出：

   ```bash
   export DISPLAY=:1
   ```

3. **启动 VNC 服务**

   因为 Wine 需要图形界面，我们需要使用 VNC 来创建一个虚拟的图形桌面环境：

   ```bash
   vncserver :1
   ```

   这将启动一个 VNC 服务在 `:1` 屏幕上。

4. **连接到 VNC 服务器**

   在你的设备上安装一个 VNC 客户端（例如 **VNC Viewer**），然后连接到 `localhost:1`。

5. **运行 Wine**

   在 VNC 会话中，你可以测试运行 Windows 应用程序，例如记事本：

   ```bash
   wine notepad
   ```

   如果一切正常，Windows 记事本应用程序将出现在 VNC 窗口中。

#### 安装 MoBox

**MoBox** 是一个集成了 Wine 和图形界面的工具，简化了在 Termux 中运行 Windows 应用程序的步骤。

1. **下载安装脚本**

   ```bash
   curl -s -o ~/x https://raw.githubusercontent.com/olegos2/mobox/main/install && . ~/x
   ```

开始安装前会询问安装box86+wine还是wow64,建议选wow64(选项2)

2. **启动 MoBox**

   安装完成后，可以使用以下命令启动 MoBox：

   ```bash
   mobox
   ```

   这将打开一个图形界面，你可以在其中安装和运行 Windows 应用程序。

#### 运行 Windows 应用和游戏

借助 Wine 和 MoBox，你可以在 Termux 上运行部分 Windows 应用程序，甚至是一些轻量级的游戏。

**示例：安装并运行 Windows 应用程序**

1. **下载应用程序的安装程序**

   使用 `wget` 或通过浏览器下载你想要安装的 Windows 应用程序的 `.exe` 文件。

2. **使用 Wine 安装应用程序**

   ```bash
   wine setup.exe
   ```

   按照安装向导完成安装。

3. **运行已安装的应用程序**

   找到应用程序的可执行文件，使用 Wine 运行：

   ```bash
   wine "C:/Program Files/AppName/app.exe"
   ```

使用mobox玩游戏可以看一下极客湾的这个视频：

https://www.bilibili.com/video/BV1jr421h7in



### 在 Termux 中运行图形界面环境

除了使用 Windows 容器和 proot 容器，Termux 本身也支持运行如 Xfce 等的图形化桌面环境。这可以通过安装 **termux-x11** 或使用 **VNC Server** 来实现。

#### 使用 termux-x11 实现图形界面

**termux-x11** 是 Termux 的一个 X11 服务器，实现了对图形界面的支持。

##### 安装 termux-x11

1. **安装 termux-x11**

   首先，安装 Termux X11 应用程序。由于该应用未在主流应用商店发布，需要从官方 GitHub 仓库下载 APK 并手动安装。

   - 前往 [termux-x11 官方仓库](https://github.com/termux/termux-x11/releases)，下载最新的 APK，并在设备上安装。

2. **安装必要的包**

   在 Termux 中，添加 `x11-repo` 仓库并安装相关包：

   ```bash
   pkg install x11-repo
   pkg install termux-x11-nightly
   ```

3. **安装图形界面环境**

   以安装 Xfce 桌面环境为例：

   ```bash
   pkg install xfce4
   ```

4. **启动 termux-x11**

   在 Termux 中，设置 `DISPLAY` 变量并启动图形界面：

   ```bash
   export DISPLAY=:0
   termux-x11 :0 &
   ```

5. **启动图形桌面环境**

   启动 Xfce 桌面：

   ```bash
   startxfce4 &
   ```

6. **查看图形界面**

   打开 termux-x11 应用，你将看到熟悉的 Xfce 桌面环境，可以在其中运行图形化的应用程序。

#### 使用 VNC Server 实现图形界面

另一种方法是使用 VNC Server，通过 VNC 客户端连接并查看图形界面。

##### 安装 VNC Server 和桌面环境

1. **安装必要的包**

   ```bash
   pkg install tigervnc
   pkg install xfce4
   ```

2. **配置 VNC 密码**

   首次启动 VNC Server 时，需要设置访问密码：

   ```bash
   vncserver -localhost
   ```

   系统会提示你创建一个访问密码。

3. **配置启动文件**

   编辑 `~/.vnc/xstartup`，添加以下内容：

   ```bash
   #!/data/data/com.termux/files/usr/bin/sh
   startxfce4 &
   ```

   确保脚本具有执行权限：

   ```bash
   chmod +x ~/.vnc/xstartup
   ```

4. **启动 VNC Server**

   ```bash
   vncserver :1
   ```

   这将启动一个 VNC 服务在 `:1` 屏幕上。

##### 使用 VNC Viewer 连接

1. **安装 VNC 客户端**

   在你的设备上安装一个 VNC 客户端应用，例如 **[VNC Viewer](https://cloud.yuchu.me/s/VMfL)**。

2. **连接到 VNC 服务器**

   在 VNC 客户端中，连接到 `localhost:1`。

3. **查看图形界面**

   成功连接后，你将看到 Xfce 桌面环境。

#### 注意事项

- **性能限制**：由于设备性能和网络延迟，通过 VNC 可能会有一定的卡顿，体验可能不如直接使用 termux-x11 流畅。

- **存储空间**：安装图形界面环境和相关应用程序可能需要较多的存储空间，请确保设备有足够的可用空间。

- **耗电量**：运行图形界面可能会增加设备的耗电量，注意电池电量的使用。


# 总结

Termux 为 Android 设备带来了强大的功能扩展，使得在移动设备上运行 Linux 环境成为可能。通过合理的配置和使用，你可以在手机上完成开发、学习、服务器搭建等多种任务。如果你对终端和 Linux 世界感兴趣，那么 Termux 将是一个不可错过的工具。
