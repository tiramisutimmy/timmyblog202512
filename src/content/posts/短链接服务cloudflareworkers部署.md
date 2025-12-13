---
title: 短链接服务cloudflareworkers部署
published: 2025-12-14
description: ''
image: ''
tags: 
  - workers
category: ''
draft: false 
lang: ''
---
之前用的docker跑的 https://github.com/helloxz/zurl
新发现的这个
https://github.com/miantiao-me/Sink

部署注意部分
[Why can't I create a link?](https://github.com/miantiao-me/Sink/blob/master/docs/faqs.md#1-why-cant-i-create-a-link)
Please check the Cloudflare KV bindings, the KV environment variable name should be all uppercase letters.

测试 https://w5c.me/b