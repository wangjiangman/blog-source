title: "X- 开头的 Http Headers"
date: 2017-07-26
tags: [Http]
categories: 技术
---

一些常用的 Http Headers 我们都比较熟悉，比如 `Context-Type`, `Cache-Control`, `Access` 等，这些请求头都有明确的定义，且都是由标准委员会制定的，我们可以称他们为标准的请求头。

在某些时候，我们分析 Http 请求的时候，会发现很多 `X-` 开头的请求头，这其实是为了区分标准的请求头，这些 `X-` 开头的请求头多数是由某些企业或者系统自定义的请求头，来达到通过这些请求头来传递某些信息的目的。

比如我们经常见到一下经常使用的 请求头：

- `X-Request-With`: 这是浏览器添加的用来区分 Ajax 请求的请求头
- `X-Forwarded-For`: 这是由转发设备添加的请求头，用来记录转发设备的 IP
- `X-Auth-Token`: 一般用来传递需要认证的 Token 信息