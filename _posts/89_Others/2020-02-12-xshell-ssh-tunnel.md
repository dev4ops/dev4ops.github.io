---
title: "Xshell配置SSH隧道"
subtitle: "通过SSH隧道采用Xshell工具将服务器所在的内网的端口通过隧道转发到本地"
layout: post
author: "dev4ops"
header-style: text
tags:
  - SSH
  - Xshell
  - 隧道
  - tunnel
---

# SSH 隧道简介
SSH的功能非常强大，日常除了用于命令行远程登录服务器。它还具有神奇的隧道（tunnel）功能（也被称为SSH代理），可用于加密访问本地或远程主机的服务。
更多见：
* [SSH隧道技术](https://www.jianshu.com/p/1ddab825956c)
* [SSH隧道简介](https://www.malike.net.cn/blog/2014/10/27/ssh-tunnel-tutorial/)
  
# Xshell隧道映射
## 添加Xshell的SSH代理服务器
![添加Xshell的SSH代理服务器](/img/in-post/2020-02/sshTunnel/1.png)

## 配置Xshell的SSH代理服务器
![配置SSH代理服务器](/img/in-post/2020-02/sshTunnel/2.png)

## 映射并连接
![映射并连接](/img/in-post/2020-02/sshTunnel/3.png)
