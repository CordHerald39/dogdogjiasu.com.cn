---
title: "客户端提示 address already in use：安全查找本地端口冲突"
category: "tutorials"
label: "实用教程"
description: "只读检查监听进程，区分代理端口与控制器端口，处理启动冲突而不随意结束陌生进程。"
date: "2026-10-04"
updated: "2026-10-04"
author: "狗狗加速中文资料编辑"
draft: false
---

## 错误发生在本机还是远端

address already in use 通常与本机绑定端口有关。先看错误记录中的地址和端口，是代理入站、DNS 监听还是外部控制器。远端节点的端口与本机监听端口是两回事，不要为解决本地冲突修改服务端节点参数。

## 只读查出占用者

Windows 上可使用下面的查询。示例端口需要替换，且不包含终止进程命令。

~~~powershell
Get-NetTCPConnection -LocalPort 7890 -State Listen |
  Select-Object LocalAddress,LocalPort,OwningProcess
Get-Process -Id 1234
~~~

第二行 PID 1234 也是示例，应填第一行实际返回值。查询失败可能是权限或端口不是 TCP；DNS 还可能监听 UDP。不要把“没有 TCP 结果”当作绝无占用。

## 对照三种常见情况

旧客户端仍在后台时，先从它自己的退出菜单结束运行，再启动需要的客户端。占用者是当前客户端自身时，检查是否重复启动、服务实例和桌面实例同时工作。占用者是其他正常软件时，给代理客户端选一个空闲端口，然后同步修改系统代理或应用设置。

控制器端口只用于管理 API；把系统代理指向控制器不会形成有效网络代理。设置中不同端口名称要分别核对。

## 确认修改真的生效

重载后再次查询监听，核对进程与地址。用一个普通公开网页进行连接测试，并检查旧端口是否仍被其他程序占用。若绑定了所有网卡，还要检查是否无意开放局域网访问；个人使用优先保持本地回环监听。

不要按网络教程批量 taskkill，也不要因冲突删除防火墙规则。无法识别的进程先由系统或软件管理员确认。更多排查见[连接检查](/tutorials/connection-check/)。

## 依据

[mihomo 代理端口](https://wiki.metacubex.one/config/inbound/port/)区分入站类型；[全局配置](https://wiki.metacubex.one/config/general/)解释控制器。[Microsoft Get-NetTCPConnection](https://learn.microsoft.com/en-us/powershell/module/nettcpip/get-nettcpconnection)为查询来源。本文没有检查读者设备。
