---
title: "DNS 错误与连接超时怎样分开排查：看请求卡在哪一层"
category: "tutorials"
label: "实用教程"
description: "区分域名解析、节点域名、TLS 和 HTTP 阶段，用错误证据决定下一步，避免一次替换全部 DNS。"
date: "2026-10-04"
updated: "2026-10-04"
author: "狗狗加速中文资料编辑"
draft: false
---

## 先保留错误类型

could not resolve host、lookup failed、connection timed out 和 certificate verify failed 指向不同阶段。记录一个失败域名、出现时间和当前网络，先把 URL 的路径与查询参数删掉，保留排查需要的主机信息。日志只选与这次请求相关的几行。

## DNS 查询与真实应用路径不同

系统的 nslookup 结果只能描述它采用的查询路径。客户端可能有独立 DNS、fake-ip 或应用内加密 DNS，因此系统查询成功不能保证应用解析成功。先查看最终生效配置中 DNS 是否启用，再看失败的是目标网站域名还是代理节点的域名。

## 用阶段顺序判断

| 错误阶段 | 应关注的证据 |
| --- | --- |
| 域名解析失败 | 查询服务器、网络、主机名 |
| TCP 连接超时 | 地址、端口、路由与可达性 |
| TLS 校验失败 | 时间、证书名称与信任链 |
| HTTP 错误响应 | 状态码、授权与目标服务 |

若节点域名无法解析，即使目标网站 DNS 配置正确也可能无法建立代理连接。mihomo 为节点域名提供 proxy-server-nameserver 相关配置，但更改前要理解当前配置，不复制网上整块 DNS 覆盖。

## 一次只改变一个条件

先在同一设备与同一配置下切换一种可用网络；若仍失败，再对照客户端日志。只有确定是 DNS 路径问题后才试一个兼容的 DNS 改动，保留原值并重载，观察相同域名。不要以关闭 IPv6、关闭证书检查、打开 TUN 的组合修改来掩盖根因。

持续故障时把错误阶段、版本和脱敏主机名提交给相应维护方，不上传完整订阅。后续阅读[证书错误检查](/tutorials/tls-error-safe-diagnosis/)。

## 原始资料

[mihomo DNS 配置](https://wiki.metacubex.one/en/config/dns/)解释 DNS 路径；[curl 手册](https://curl.se/docs/manpage.html)提供请求错误及诊断选项。本文提供通用定位流程，不声称狗狗加速出现特定故障。
