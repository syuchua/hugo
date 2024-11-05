---
title: 通过油猴插件无限白嫖VecMulChat的gpt4o，claude3.5s等最新大模型
categories:
  - blog
tags:
  - gpt4o
  - claude3.5sonnet
  - VecMul Chat
date: 2024-07-29T23:28:18+08:00
---

今天在`Linuxdo`闲逛时发现[一篇帖子](https://linux.do/t/topic/162476)，说是使用某个网站+油猴脚本可以实现`GPT4o`和`claude3.5sonnet`等一众最新先进大模型的无限制白嫖（暂时），如果不依赖脚本，仅正常登录使用，每天也能进行50次的对话，本着好东西应该分享的原则，水一篇博客，帮大家也用上**:laughing:**

首先是网站地址：
https://www.vecmul.com/

进入后不要登录，先安装这个油猴脚本：
https://greasyfork.org/zh-CN/scripts/502074-vecmul%E6%97%A0%E9%99%90%E5%AF%B9%E8%AF%9D

之后点击编辑

![image-20240729234408576](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/image-20240729234408576.png)

把下面的代码替换进去：

```javascript
// ==UserScript==
// @name         将聊天请求计数保持为零
// @namespace    http://tampermonkey.net/
// @version      0.5
// @description  始终将localStorage中chat-request项的计数保持为0
// @match        https://www.vecmul.com/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';

    function keepCountAtZero() {
        let chatRequest = localStorage.getItem('chat-request');
        if (chatRequest) {
            try {
                let data = JSON.parse(chatRequest);
                if (data.count !== 0) {
                    data.count = 0;
                    localStorage.setItem('chat-request', JSON.stringify(data));
                }
            } catch (e) {
                console.error('解析chat-request时出错:', e);
            }
        }
    }

    // 初始执行
    keepCountAtZero();

    // 重写localStorage的setItem方法
    const originalSetItem = Storage.prototype.setItem;
    Storage.prototype.setItem = function(key, value) {
        originalSetItem.call(this, key, value);
        if (key === 'chat-request') {
            keepCountAtZero();
        }
    };

    console.log('聊天请求计数将始终为零。');
})();
```

在返回原来那个网站，就可以实现无限制白嫖gpt4o啦，快去试试吧**:laughing:**