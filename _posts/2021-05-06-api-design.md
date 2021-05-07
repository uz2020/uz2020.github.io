---
layout: post
title:  "api设计"
---

[Best practices for REST API design](https://stackoverflow.blog/2020/03/02/best-practices-for-rest-api-design/)

要点:
1. 路径由名词组成
2. 支持过滤、排序、分页
3. 可以在响应的json中返回路径，让有需要的客户端再发起http请求，避免路径名过长
4. api版本化
