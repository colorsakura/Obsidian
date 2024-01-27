---
date: 2023-02-21 20:01
tags: TODO, DNS, Linux, Doc
lang: zh-CN
layout: note
---

# Dnsmasq

## 1 基础配置

要在单台计算机上将 dnsmasq 设置为 DNS 缓存守护程序，请指定 `listen-address` 指令，添加本地主机 IP 地址：

```plain
listen-address=::1,127.0.0.1
```

## 2 进阶配置
