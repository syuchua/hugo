---
title: 教你搭建一个自己的芙宁娜QQ机器人，支持AI聊天、语音、绘画和发送涩图及翻唱
categories:
  - blog
tags:
  - QQ机器人
  - 机器人框架
  - 芙宁娜
  - AI
date: 2024-08-08T15:26:06+08:00
---

今天来分享一个我自己写的芙宁娜QQ机器人后端，拥有的能力包括但不限于AI聊天、绘画、发送涩图、AI语音、翻唱歌曲等，还兼具上下文功能，支持自定义system_prompt，适配http和反向ws连接。先来一波展示：

## 食用方法
- 直接对话即可
    ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240616001408.png)
  - 发送`发一张`，`来一张`+关键词即可自定义发送涩图，比方说`发一张卡芙卡`
    ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240616001141.png)
  - 发送`来份涩图`，`来份色图`，`再来一张` 即可发送随机涩图
    ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240616001208.png)
  - 发送`画一张`，`生成一张` 即可发送AI绘画（如果config.json里有GPT有关的配置，则关键词会调用dalle进行绘画，除dalle外，还支持白嫖bing的AI绘画，需提前将cookie的值填入cookies.json中，只有一个账号的cookie就按字典格式填一个，多个的话可以放进列表里）
    ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240616001253.png)
  - 发送`语音说`，``语音回复` +`要用语音说的话`让机器人发送语音，或者再提示词里提示机器人通过把`#voice`标签放在回复的开头，实现更生动地语音回复。
    ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240720233521.png)
  - 发送`点歌`+歌曲名进行点歌，支持模糊匹配。
    ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240805154117.png)
  - R-18?
    该接口的涩图数量足有十几万，其中r18占27.8%，建议公共场合尽量设置为0，2的话，还是不要太相信自己的运气了(问就是惨痛的教训)
    ![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/20240616002550.png)
    
    
## 命令功能

机器人支持以下命令：

- `/help`：显示帮助信息。
- `/reset`：重置当前会话。
- `/character`：输出`config.json`中的`character`值，也即当前的人设。
- `/history`: 输出之前的条消息记录，默认十条，也可以接空格+数字指定。
- `/clear`:清除消息记录，默认十条，可接空格+数字指定。
- `/music_list`: 获取歌曲列表
- `/r18 [0, 1, 2]`切换涩图接口r18模式，0为关闭，1为开启，2随机
- `/model [new_model]`切换模型，新模型需先在model.json中配置好

语音功能需要本地搭建gpt-sovits，可以参考视频https://b23.tv/9dOdMo6

这里放上本地搭建语音整合包
[GPT-SoVITS-Inference](https://cloud.yuchu.me/s/J2um)

## 部署

接下来是机器人项目的部署，对接QQ使用的是支持onebot协议的[llonebot](https://github.com/LLOneBot/LLOneBot)，所以其他类似项目如[Lagrange](https://github.com/LagrangeDev/Lagrange.Core)和[NapCat](https://github.com/NapNeko/NapCatQQ)也是可以的，这里仅演示llonebot的配置，默认使用反向ws连接

[包含NTQQ，llonebot插件，MongoDB安装包和项目源码的整合包](https://cloud.yuchu.me/s/MAH0)下载整合包并解压

先双击`install_windows.exe`安装NTQQ，然后把llonebot.zip复制到NTQQ的插件目录下解压，重启QQ即可在设置里看到有关配置，然后就是填写反向ws监听地址

```
ws://127.0.0.1:8011/ws
```

然后是双击MongoDB的安装文件，没什么好讲的，安装完成后进入bin目录，打开cmd，输入
```
 mongod --dbpath D:\MongoDB\data --logpath D:\MongoDB\log\mongodb.log --logappend   #替换成你自己的对应目录
```

关闭终端，填写机器人项目的config，config.json里是基础配置
  - `openai_api_key`: 你的openai_api_key
  - `model`: 使用的模型，默认为gpt-3.5-turbo
  - `self_id`：机器人的QQ号。
  - `admin_id`：管理员的QQ号。
  - `block_id`: 要屏蔽的QQ号。
  - `nicknames`：机器人的昵称列表。
  - `system_message`：系统消息配置，最重要的是`character`，相当于机器人的人格。
  - `connection_type`: 连接类型，可选http或ws_reverse
  - `report_secret`: http事件上传密钥。仅使用http连接时需要填写，默认的123456也可以不管
  - `enable_time`: 设置定时开启时间
  - `disable_time`: 设置定时关闭时间
  - `proxy_api_base`: openai_api_key请求地址，默认为https://api.openai.com/v1
  - `system_message`：`character`里填写人设
  - `reply_probability`: 当收到的消息中没有nickname时的回复频率，1为每一条都回复，0为仅回复带有nickname的消息，默认0.5
  - `r18`: 0为关闭r18，1开启r18，2为随机发送(慎选)
  - `audio_save_path`: 语音文件保存位置
  - `voice_service_url`: 语音接口地址
  - `cha_name`：语音接口指定角色
dialogues.json里主要是配置一些固定回答，一般没什么用，除非有什么恶趣味
model.json里主要是非gpt的其他模型的配置，如果填了，会覆盖config.json里GPT的配置需要为OneAPI格式，`model`里填写的模型名需要现在`aviliable_models`里填上。`vision`代表是否支持识图，如果你的模型可以识图就填true(如果使用gpt系列模型，gpt4系列默认允许识图)

### 视频教程
【芙宁娜QQ机器人搭建教程】 https://www.bilibili.com/video/BV1BBYcekEUs/?share_source=copy_web&vd_source=6f08734cb3a294b6a1e634a3e5b481ca

<iframe src="//player.bilibili.com/player.html?bvid=BV1BBYcekEUs&p=1&poster=1&danmaku=0&autoplay=0" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>

### 获取bing的cookie用于非gpt系列的AI绘图
[如果视频无法渲染可以点击链接下载](https://cloud.yuchu.me/f/qxjsX/2024-08-05%2021-41-49.mp4)
<video src="https://cloud.yuchu.me/f/qxjsX/2024-08-05%2021-41-49.mp4"></video>

### 项目开源仓库
[syuchua/MY_QBOT: 一个简单的QQ机器人后端框架 (github.com)](https://github.com/syuchua/MY_QBOT)

### 一些跟芙芙聊天的日常
【谁家傻芙芙连9.8和9.11哪个大都分不清】 https://www.bilibili.com/video/BV1TivWeFEot/?share_source=copy_web&vd_source=6f08734cb3a294b6a1e634a3e5b481ca