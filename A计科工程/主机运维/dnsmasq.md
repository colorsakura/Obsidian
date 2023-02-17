---
id: "dnsmasq.md"
aliases:
  - "基础配置"
tags: []
Date: 1671010440
Tag: "TODO, DNS, Linux, Doc"
---

## 基础配置

要在单台计算机上将 dnsmasq 设置为 DNS 缓存守护程序，请指定 `listen-address` 指令，添加本地主机 IP 地址：
```
listen-address=::1,127.0.0.1
```

## 进阶配置
