---
title: "Windows 浏览器能打开但软件不通：分清系统代理与 WinHTTP"
category: "tutorials"
label: "实用教程"
description: "通过只读代理状态、显式代理请求和连接记录定位软件不走代理，避免全局重置系统设置。"
date: "2026-10-04"
updated: "2026-10-04"
author: "狗狗加速中文资料编辑"
draft: false
---

## 把“软件不通”缩小到一条请求

同一台电脑上，浏览器可访问并不意味着每个软件采用相同代理方式。先记录软件名称、版本、失败时间及错误类型，用相同网络打开一个普通公开网页作对照。不要把登录失败、证书失败和连接超时混在一起处理。

## 看软件采用哪一套设置

有些软件使用系统代理，有些有独立代理选项，基于 WinHTTP 的组件又有自己的配置。管理员终端里常见的 winhttp reset proxy 是修改命令，初步调查不要运行它。下面只读取 WinHTTP 状态：

~~~powershell
netsh winhttp show proxy
~~~

结果只表示 WinHTTP 的代理配置，不是所有 Windows 应用的网络状态。公司管理的设备应由管理员解释策略，不能绕过企业配置。

## 用显式代理作一次对照

如果客户端开启 HTTP 或 mixed 入站，先确认它实际监听的本地端口。用不带登录凭据的公开测试网址进行一次请求：

~~~powershell
# 7890 只是示例，换成客户端实际端口
curl.exe --proxy http://127.0.0.1:7890 --connect-timeout 10 --max-time 20 --head https://example.org/
~~~

若显式代理成功而目标软件失败，重点检查软件的独立代理选项；若没有监听或连接被拒绝，先解决客户端入站。某些端口仅提供 SOCKS，不能把协议名随意改成 HTTP。

## 最后才考虑 TUN

无法配置代理的软件可能需要其他接管方式，但 TUN 会改变路由与 DNS，先按客户端官方说明确认服务权限和兼容性。切换后重新发起请求，查看软件连接是否进入客户端。企业 VPN、防火墙和游戏反作弊还可能有独立限制，出现冲突时恢复原设置，不连续叠加工具。

保存“软件设置、监听端口、实际连接规则”的对应关系。狗狗加速服务账户能否连接仍需单独验证，本文不代表专用客户端测试。基础步骤见[连接检查](/tutorials/connection-check/)。

## 原始资料

[Microsoft netsh winhttp](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/netsh-winhttp)解释查询命令；[curl 手册](https://curl.se/docs/manpage.html)解释显式代理；[mihomo TUN](https://wiki.metacubex.one/config/inbound/tun/)说明路由接管。
