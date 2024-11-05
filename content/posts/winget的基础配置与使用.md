---
title: winget的基础配置与使用
categories:
  - blog
tags:
  - winget
  - windows实用工具
date: 2024-03-26T20:05:34+08:00
---
# 1.什么是winget

微软公司在 2020 年 5 月 Microsoft Build 开发者大会上发布了用于 Windows 系统的官方支持的软件包管理器 —— Windows 软件包管理器（Windows Package Manager），也就是 WinGet 软件包管理器。经过 3 年的发展，越来越多的软件厂商在这个平台上发布自家软件。WinGet 除了支持 exe、msi 等传统的 Win32 安装包格式外，还支持用于新版 Windows 的 msix/msixbundle 格式所打包的软件。

[Windows软件包管理工具](https://learn.microsoft.com/en-us/windows/package-manager)

<!-- more -->

# 2.获取winget

首先确认系统是否满足需求：WinGet 只可以运行在 Windows10 高于 1709 (Build 16299) 的版本和 Windows11 上。

在安装 WinGet 之前，建议使用微软新推出的终端模拟器 Windows Terminal 代替传统的conhost.exe。

## 2.1 Windows10/11 非 LTS 版本（家庭版、教育版、专业版、专业工作站版、企业版）

Windows Terminal 和 WinGet 都可以通过系统预装的 Microsoft Store 商店搜索安装，也可以转到下面的网页调用 Microsoft Store 安装，也可以去微软托管在 GitHub 的发布页下载安装包到本地安装（较新版本的 Windows11 已经预装，如已预装请略过此章节）

### Windows Terminal

[Windows Terminal](https://apps.microsoft.com/detail/9n0dx20hk701?hl=zh=CN&gl=CN)

### Winget
[Winget](https://apps.microsoft.com/detail/9nblggh4nns1?hl=zh-CN&gl=CN)

## 2.2 设置 Windows Terminal 为系统默认终端模拟器

较新版本的 Windows11 已经默认 Windows Terminal 为系统终端模拟器，如已配置好请略过此章节。

从命令行窗口标题栏右键菜单进入默认设置,在【终端】选项卡下更改“默认终端应用程序”为“Windows 终端”，确认退出.

## 3. 使用 WinGet 管理软件

### 常用命令

- 查看版本、信息
```
winget -v(winget --version)   #显示winget版本
winget info                   #显示常规信息
```

- 替换源：
```
winget source remove winget
winget source add winget https://mirrors.ustc.edu.cn/winget-source
```
- 搜索软件包
`winget search [包名]`
以7zip为例
![winget_search](https://cloud.yuchu.me/f/L1hQ/winget_search.png)

- 查看软件包
`winget show --id 7zip.7zip`
![winget_show](https://cloud.yuchu.me/f/KEf4/winget_show.png)

- 安装软件包
`winget install --id 7zip.7zip`

- 自定义安装
`winget install --id 7zip.7zip -i`

-指定位置安装
`winget install -l,--location [指定路径(如支持)]`

- 更新软件包
`winget upgrade --id 7zip.7zip`

- 卸载软件包
`winget uninstall --id 7zip.7zip`

- 显示已安装的软件包
`winget list`

### 其他命令
- `winget search --id [包名]                                    #按id筛选结果`

- `winget search --name [包名]                                  #按名称筛选`

- `winget search --tag [包名]                                   #按标签筛选`

- `winget search -s,--source [包名]                             #指定源查找`

- `winget search -e,--exact [包名]                              #精确匹配查找`

- `winget search --version [包名]                               #显示软件包可用版本`

- `winget install --id/--name/--tag/-e,--exact/--version [包名] #与search类似`

- `winget install --ignore-security-hash [包名]                 #忽略安装程序哈希检查失败`


# 进阶---使用PowerShell 脚本从网络批量下载来自 WinGet 的软件包

```
# Requires -Version 7.0

#更改字符集为UTF-8
#[System.Console]::OutputEncoding=[System.Text.Encoding]::GetEncoding(65001)

# 更新源
# winget source reset winget
# winget source add winget https://mirrors.ustc.edu.cn/winget-source/

Clear-Host

# 写入安装脚本
$Script:PackageCount = 0
$Script:PackageLocalPath = "PackageInstallers"
$Script:PackageList = "PackageInstallers.log"
$Script:PackageScriptIWR = "Get-PackageInstallersIWR.ps1"
$Script:PackageScriptSBT = "Get-PackageInstallersSBT.ps1"
$Script:PackageScriptWCD = "Get-PackageInstallersWCD.ps1"
$Script:PackageLogIWR = [System.String]::Concat($Script:PackageLocalPath, "\PackageInstallersIWR.log")
$Script:PackageLogSBT = [System.String]::Concat($Script:PackageLocalPath, "\PackageInstallersSBT.log")
$Script:PackageLogWCD = [System.String]::Concat($Script:PackageLocalPath, "\PackageInstallersWCD.log")

New-Item -Name $Script:PackageLocalPath -ItemType Directory -Force | Out-Null
Remove-Item $Script:PackageLocalPath\* -Recurse -Force | Out-Null

Write-Output $null | Out-File $Script:PackageList
Write-Output $null | Out-File $Script:PackageScriptIWR
Write-Output $null | Out-File $Script:PackageScriptSBT
Write-Output $null | Out-File $Script:PackageScriptWCD

[System.String]::Concat("# Updated @ ", (Get-Date -Format "yyyy.MM.dd-HH:mm:ss")) | Tee-Object $Script:PackageScriptIWR -Append | Tee-Object $Script:PackageScriptSBT -Append | Out-File $Script:PackageScriptWCD -Append
Write-Output "# Requires -Version 7.0" | Tee-Object $Script:PackageScriptIWR -Append | Tee-Object $Script:PackageScriptSBT -Append | Out-File $Script:PackageScriptWCD -Append
Write-Output "`$Script:PackageLocalCount = 0" | Tee-Object $Script:PackageScriptIWR -Append | Tee-Object $Script:PackageScriptSBT -Append | Out-File $Script:PackageScriptWCD -Append
Write-Output "Clear-Host" | Tee-Object $Script:PackageScriptIWR -Append | Tee-Object $Script:PackageScriptSBT -Append | Out-File $Script:PackageScriptWCD -Append
Write-Output "New-Item -Name `"$Script:PackageLocalPath`" -ItemType Directory -Force | Out-Null" | Tee-Object $Script:PackageScriptIWR -Append | Tee-Object $Script:PackageScriptSBT -Append | Out-File $Script:PackageScriptWCD -Append
Write-Output "Remove-Item `"$Script:PackageLocalPath\*`" -Recurse -Force | Out-Null" | Tee-Object $Script:PackageScriptIWR -Append | Tee-Object $Script:PackageScriptSBT -Append | Out-File $Script:PackageScriptWCD -Append
Write-Output "Write-Output `$null | Out-File `"$Script:PackageLogIWR`"" | Out-File $Script:PackageScriptIWR -Append
Write-Output "Write-Output `$null | Out-File `"$Script:PackageLogSBT`"" | Out-File $Script:PackageScriptSBT -Append
Write-Output "Write-Output `$null | Out-File `"$Script:PackageLogWCD`"" | Out-File $Script:PackageScriptWCD -Append

# 安装脚本函数
function Get-WinGetPackageURL {
    param($ID, $Name, $Type, $Class)

    $Private:PackageWinGetID = $ID
    $Private:PackageFilename = $Name
    $Private:PackageFiletype = $Type
    $Private:PackageClassification = $Class
    $Private:PackageLocalClassifiedPath = [System.String]::Concat($Script:PackageLocalPath, "\", $Private:PackageClassification)

    $Private:FindURL = "安装程序 URL："
    $Private:FindVER = "版本:"

    # 获取软件包版本和下载地址
    try {
        # System Installer
        $Private:PackageURL = [System.String]::Concat((winget show --scope machine -id $Private:PackageWinGetID | Select-String $Private:FindURL)).Replace($Private:FindURL, "").Replace(" ", "")
        $Private:PackageVersion = [System.String]::Concat((winget show --scope machine -id $Private:PackageWinGetID | Select-String $Private:FindVER)).Replace($Private:FindVER, "").Replace(" ", "")
    }
    catch [ArgumentNullException] {
        # User Installer
        $Private:PackageURL = [System.String]::Concat((winget show --id $Private:PackageWinGetID | Select-String $Private:FindURL)).Replace($Private:FindURL, "").Replace(" ", "")
        $Private:PackageVersion = [System.String]::Concat((winget show --id $Private:PackageWinGetID | Select-String $Private:FindVER)).Replace($Private:FindVER, "").Replace(" ", "")
    }

    $Script:PackageCount++
    $Private:PackageLocalFile = [System.String]::Concat($Private:PackageLocalClassifiedPath, "\", $Private:PackageFilename, "_", $Private:PackageVersion, ".", $Private:PackageFiletype)
    $Private:PackageUpdateTime = Get-Date -Format "yyyy.MM.dd-HH:mm:ss"

    $Private:PackageCommandPath = "New-Item -Name `"$Private:PackageLocalClassifiedPath`" -ItemType Directory -Force | Out-Null"
    $Private:PackageCommandItem = "New-Item -Name `"$Private:PackageLocalFile`" -ItemType File -Force | Out-Null"
    $Private:PackageCommandDNS = "ipconfig.exe /flushdns | Out-Null"
    $Private:PackageCommandIWR = "Invoke-WebRequest -Uri `"$Private:PackageURL`" -OutFile `"$Private:PackageLocalFile`""
    $Private:PackageCommandSBT = "Start-BitsTransfer -Source `"$Private:PackageURL`" -Destination `"$Private:PackageLocalFile`""
    $Private:PackageCommandWCD = "`$PackageWebClient = New-Object System.Net.WebClient; `$PackageWebClient.DownloadFile(`"$Private:PackageURL`", `"$Private:PackageLocalFile`")"
    $Private:PackageCommandLog = "Start-Sleep -Seconds 3; [System.Console]::Out.Flush(); [System.String]::Concat(`"┏━ `", (Get-Date -Format `"yyyy.MM.dd-HH:mm:ss`"), `" @ $Private:PackageWinGetID`", `" [$Private:PackageVersion]``n`", `"┣━ ```"$Private:PackageURL```"``n`", `"┗━ ```"$Private:PackageLocalFile```"``n`") | Write-Output"

    Write-Output "┏━ $Private:PackageUpdateTime @ $Private:PackageWinGetID [$Private:PackageVersion]`n┣━ `"$Private:PackageURL`"`n┗━ `"$Private:PackageLocalFile`"`n" | Tee-Object $Script:PackageList -Append
    Write-Output "$Private:PackageCommandPath; $Private:PackageCommandItem; $Private:PackageCommandDNS; $Private:PackageCommandIWR; if (`$?) { `$Script:PackageLocalCount++; $Private:PackageCommandLog | Tee-Object `"$Script:PackageLogIWR`" -Append; }" | Out-File $Script:PackageScriptIWR -Append
    Write-Output "$Private:PackageCommandPath; $Private:PackageCommandItem; $Private:PackageCommandDNS; $Private:PackageCommandSBT; if (`$?) { `$Script:PackageLocalCount++; $Private:PackageCommandLog | Tee-Object `"$Script:PackageLogSBT`" -Append; }" | Out-File $Script:PackageScriptSBT -Append
    Write-Output "$Private:PackageCommandPath; $Private:PackageCommandItem; $Private:PackageCommandDNS; $Private:PackageCommandWCD; if (`$?) { `$Script:PackageLocalCount++; $Private:PackageCommandLog | Tee-Object `"$Script:PackageLogWCD`" -Append; }" | Out-File $Script:PackageScriptWCD -Append

    New-Item -Name $Private:PackageLocalClassifiedPath -ItemType Directory -Force | Out-Null
    New-Item -Name $Private:PackageLocalFile -ItemType File -Force | Out-Null
}

ipconfig /flushdns | Out-Null

# Components
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2005.x86" -Name "Microsoft_Visual_C++_Redistributable_2005_32bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2005.x64" -Name "Microsoft_Visual_C++_Redistributable_2005_64bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2008.x86" -Name "Microsoft_Visual_C++_Redistributable_2008_32bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2008.x64" -Name "Microsoft_Visual_C++_Redistributable_2008_64bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2010.x86" -Name "Microsoft_Visual_C++_Redistributable_2010_32bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2010.x64" -Name "Microsoft_Visual_C++_Redistributable_2010_64bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2012.x86" -Name "Microsoft_Visual_C++_Redistributable_2012_32bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2012.x64" -Name "Microsoft_Visual_C++_Redistributable_2012_64bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2013.x86" -Name "Microsoft_Visual_C++_Redistributable_2013_32bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2013.x64" -Name "Microsoft_Visual_C++_Redistributable_2013_64bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2015+.x86" -Name "Microsoft_Visual_C++_Redistributable_2015_32bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.VCRedist.2015+.x64" -Name "Microsoft_Visual_C++_Redistributable_2015_64bit" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Microsoft.PowerToys" -Name "Microsoft_PowerToys" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "EclipseAdoptium.Temurin.8.JDK" -Name "EclipseAdoptium_Temurin_JDK_8_Releases" -Type "msi" -Class "Components"
Get-WinGetPackageURL -ID "EclipseAdoptium.Temurin.11.JDK" -Name "EclipseAdoptium_Temurin_JDK_11_Releases" -Type "msi" -Class "Components"
Get-WinGetPackageURL -ID "EclipseAdoptium.Temurin.17.JDK" -Name "EclipseAdoptium_Temurin_JDK_17_Releases" -Type "msi" -Class "Components"
Get-WinGetPackageURL -ID "Python.Python.2" -Name "Python_2_Dev" -Type "msi" -Class "Components"
Get-WinGetPackageURL -ID "Python.Python.3.8" -Name "Python_3.8_Dev" -Type "exe" -Class "Components"
Get-WinGetPackageURL -ID "Python.Python.3.11" -Name "Python_3.11_Dev" -Type "exe" -Class "Components"

# Applications
Get-WinGetPackageURL -ID "7zip.7zip" -Name "7_Zip" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Anaconda.Anaconda3" -Name "Anaconda3" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Apple.iTunes" -Name "iTunes" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Audacity.Audacity" -Name "Audacity" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Bandisoft.Bandizip" -Name "Bandizip" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Bandisoft.Honeyview" -Name "Honeyview" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Bitvise.SSH.Server" -Name "Bitvise_SSH_Server" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "BlenderFoundation.Blender" -Name "Blender" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "Codeblocks.Codeblocks" -Name "Codeblocks" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "darktable.darktable" -Name "darktable" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "den4b.Hasher" -Name "Hasher" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "den4b.ReNamer" -Name "ReNamer" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "DEVCOM.JetBrainsMonoNerdFont" -Name "Nerd_Fonts" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "Geany.Geany" -Name "Geany" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "GeekUninstaller.GeekUninstaller" -Name "Geek_Uninstaller" -Type "zip" -Class "Applications"
Get-WinGetPackageURL -ID "GIMP.GIMP" -Name "GIMP" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Git.Git" -Name "Git" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "GitHub.Atom" -Name "Atom" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "GitHub.GitHubDesktop" -Name "GitHubDesktop" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "GNU.Octave" -Name "Octave" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "gnuplot.gnuplot" -Name "gnuplot" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Google.Chrome" -Name "Google_Chrome" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "HandBrake.HandBrake" -Name "HandBrake" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Harmonoid.Harmonoid" -Name "Harmonoid" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Henry++.MemReduct" -Name "MemReduct" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "JanDeDobbeleer.OhMyPosh" -Name "Oh_My_Posh" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "KDE.Kate" -Name "Kate" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "KDE.Krita" -Name "Krita" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "KDE.Okular" -Name "Okular" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Kitware.CMake" -Name "CMake" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "LLVM.LLVM" -Name "LLVM" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Meltytech.Shotcut" -Name "Shotcut" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "MHNexus.HxD" -Name "HxD" -Type "zip" -Class "Applications"
Get-WinGetPackageURL -ID "Microsoft.VisualStudio.2022.Community" -Name "Microsoft_Visual_Studio_2022_Community" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Microsoft.VisualStudioCode" -Name "Microsoft_Visual_Studio_Code" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Mozilla.Firefox" -Name "Firefox" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "Mozilla.Thunderbird" -Name "Thunderbird" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Musescore.Musescore" -Name "Musescore" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "OBSProject.OBSStudio" -Name "OBSStudio" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Pinta.Pinta" -Name "Pinta" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Piriform.Recuva" -Name "Recuva" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "RealVNC.VNCViewer" -Name "VNCViewer" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Rufus.Rufus" -Name "Rufus" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "ShiningLight.OpenSSL" -Name "OpenSSL" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "SSHFS-Win.SSHFS-Win" -Name "SSHFS" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "Tencent.QQ" -Name "Tencent_QQ" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Tencent.TencentMeeting" -Name "Tencent_Meeting" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "Tencent.WeChat" -Name "Tencent_WeChat" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "TheDocumentFoundation.LibreOffice" -Name "LibreOffice" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "TortoiseGit.TortoiseGit" -Name "TortoiseGit" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "TortoiseHg.TortoiseHg" -Name "TortoiseHg" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "TortoiseSVN.TortoiseSVN" -Name "TortoiseSVN" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "uGetdm.uGet" -Name "uGet" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "VideoLAN.VLC" -Name "VideoLAN_VLC" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "vim.vim" -Name "GVIM" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "voidtools.Everything" -Name "Everything" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "WinFsp.WinFsp" -Name "WinFsp" -Type "msi" -Class "Applications"
Get-WinGetPackageURL -ID "WiresharkFoundation.Wireshark" -Name "Wireshark" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "XnSoft.XnConvert" -Name "XnConvert" -Type "exe" -Class "Applications"
Get-WinGetPackageURL -ID "ZeroTier.ZeroTierOne" -Name "ZeroTier_One" -Type "msi" -Class "Applications"

# 软件包计数：用于对照日志查缺补漏
Write-Output "Total: $Script:PackageCount`n" | Tee-Object $Script:PackageList -Append
Write-Output "Write-Output `"Total: `$Script:PackageLocalCount``n`"" | Out-File $Script:PackageScriptIWR -Append
Write-Output "Write-Output `"Total: `$Script:PackageLocalCount``n`"" | Out-File $Script:PackageScriptSBT -Append
Write-Output "Write-Output `"Total: `$Script:PackageLocalCount``n`"" | Out-File $Script:PackageScriptWCD -Append

# 查看日志文件
explorer "${env:LOCALAPPDATA}\Packages\Microsoft.DesktopAppInstaller_8wekyb3d8bbwe\LocalState\DiagOutputDir"
Remove-Item "${env:LOCALAPPDATA}\Packages\Microsoft.DesktopAppInstaller_8wekyb3d8bbwe\LocalState\DiagOutputDir\*" -Recurse -Force -Confirm
```
这段代码的作用是将指定好的软件包的信息从 WinGet 上爬取下来，并自动生成用于下载这些软件包的脚本。将此段代码复制另存为 Get-PackagesInfo.ps1 ，在 PowerShell 中跳转到脚本所在目录运行脚本，若遇到了权限问题，请用管理员权限打开 PowerShell 环境，并正确指定本地计算机的 PowerShell 执行策略
完成上述步骤，就可以在 PowerShell 环境下执行这个脚本了：
`.\Get-PackagesInfo.ps1`
执行后，脚本会自动从 WinGet 配置的源下载安装包的信息，稍等片刻

运行完毕后，在当前目录会自动生成 3 个新的 PS 脚本，用于将 Get-PackagesInfo.ps1 这个脚本中指定的软件包按分类从 WinGet 源下载到 PackageInstallers 目录下的分类目录中
![winget_ps](https://cloud.yuchu.me/f/GzUA/winget_ps.png)
这三个脚本的功能完全一致，只是用于下载采用的实现不同：

"Get-PackageInstallersIWR.ps1" 使用 cmdlet Invoke-WebRequest 下载

"Get-PackageInstallersSBT.ps1" 使用 cmdlet Start-BitsTransfer 下载

"Get-PackageInstallersWCD.ps1" 创建 System.Net.WebClient 对象下载
任选其一即可

# END