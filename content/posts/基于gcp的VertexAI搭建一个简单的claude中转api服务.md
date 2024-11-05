---
title: 基于gcp的VertexAI搭建一个简单的claude中转api服务
categories:
  - blog
tags:
  - gcp
  - Google Cloud Platform
  - Claude
  - 中转api
date: 2024-07-11T23:24:24+08:00
---
最近写一个QQ机器人的后端项目，用于对接AI大模型以及一些图片、语音接口，难免要跟各类大模型的api打交道，听说claude更适合角色扮演，而且新出的Claude3.5-sonnet能力甚至超越了GPT-4o,遂一直想要体验一下，但没有国外的实体手机号，所以没有机会。机缘巧合下得知gcp的VertexAI可以白嫖最新的Gemini和Claude，立马来了兴趣，研究后却发现是通过sdk的方式调用的，没有api，但这不是什么大问题，能用就行，实在不行自己写一个本地api服务不就行了吗，说干就干，直接放上仓库地址，如果能帮到您的话不妨给个star鼓励一下：

https://github.com/syuchua/gcp_claude_transfer

配置1说明都在readme里了，如果有什么不明白的话，欢迎提issue。