---
title: "开启 fake-ip 后内网域名异常：先核对解析路径再做例外"
category: "tutorials"
label: "实用教程"
description: "针对路由器、NAS 和局域网主机名检查 fake-ip、系统 DNS 与 hosts，使用窄范围例外并验证回退。"
date: "2026-10-04"
updated: "2026-10-04"
author: "狗狗加速中文资料编辑"
draft: false
---

## 先确定问题只在局域网资源吗

某个 NAS 域名失效，而公开网页正常时，先记录访问名称与实际内网地址。直接访问设备管理 IP 的结果可以辅助定位，但证书通常绑定域名，出现名称警告时不要输入密码。不要用这种测试结果给公网节点下结论。

## 认识 fake-ip 的用途

mihomo 的 fake-ip 模式会为域名返回映射地址，并在接管流量时还原目标。系统查询出现映射地址不自动等于污染。若应用绕开接管，或依赖局域网发现，实际路径可能与预期不同。还要区分普通 DNS 与 .local 等名称发现机制。

## 沿当前路径逐项确认

先在关闭客户端连接的情况下核对内网设备是否可达，之后重新开启并查看连接记录。检查客户端 DNS 是否读取 hosts、是否有对应 nameserver-policy，以及 fake-ip-filter 的模式。blacklist、whitelist 和 rule 的语义不同，复制一个 filter 列表之前必须先看模式。

## 给已确认域名做最小例外

若证据指向某一内网域名不应得到映射地址，只对准确域名或必要的内网后缀添加例外，使用客户端支持的个人扩展机制。不要用星号覆盖所有域名，也不要把网上整份 DNS 配置导入个人订阅。

修改后按客户端支持方式重载，结束旧连接，再访问同一资源。确认得到预期地址并且客户端路径正确；若没有改善，恢复例外，继续查内网 DNS 与路由。映射例外只解决解析行为，不自动保证直连路由。

相关步骤见[DNS 与超时分层检查](/tutorials/dns-failure-versus-connect-timeout/)。

## 原始资料

[mihomo DNS 文档](https://wiki.metacubex.one/en/config/dns/)定义 enhanced-mode、fake-ip-filter 与 hosts 行为。本文没有访问读者的局域网，也不提供服务质量判断。
