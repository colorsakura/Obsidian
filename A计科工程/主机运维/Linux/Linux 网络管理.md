---
layout: note
date: 2023-02-19 14:43
tags: doc, network, systemd, linux, dns, smartdns
---

# 网络管理

[[systemd]] 目前正在统一 Linux, 并且多数发行版已采用 `systemd` 替代以前的 `init` ；
当前的思路是采用最少的软件，最小依赖，并且官方支持的方案。
Linux 将使用 `systemd-networkd` 管理网络，无线网配合 `iwd` 使用，本地配置 `smartdns` 作 DNS 缓存。

> [!warning]
> 这里主要介绍配置有线网络，以及无线网络的过程。在开始配置前，需要把正在使用的网络管理服务停止掉，以避免出现冲突。

---

## 有线网络

有线网络主要配置时，考虑到可能使用手机 usb 连接共享收集的网络，为了保持命名的一致性，
通过 bridge 来归一化所有有线连接。配置如下：

-. 将所有有线连接都作为 `br0` 的底层设备

```plain
# /etc/systemd/network/20-ethernet.network
[Match]
Name=en*
Name=eth*

[Network]
Bridge=br0
```

> [!note] > `en*` 匹配以 en 开头的所有设备。

-. 定义 `br0` 设备

```plain
# /etc/systemd/network/20-br0.netdev
[NetDev]
Name=br0
Kind=bridge
```

-. 配置 `br0` 的网络
这里采用了 DHCP 的方式获取 ip，由于我采用了独立的 DNS 解析，这里配置不使用 DNS.

```plain
# /etc/systemd/network/20-br0.network
[Match]
Name=br0

[Network]
# Address=192.168.0.2/24
# Gateway=192.168.0.1
# DNS=192.168.0.1
DHCP=yes
IPForward=yes

[DHCPv4]
RouteMetric=100

```

以上是有线网络的所有配置。

## 无线网络

接下来是无线网络的配置，无线网络采用 [[iwd]] 来管理 wifi。

-. 安装 iwd

```shell
# archlinux
sudo pacman -S iwd
```

-. 启动 iwd 服务

```shell
systemctl enable --now iwd.service
```

-. 设置无线网络的配置

```conf
# /etc/systemd/network/25-wireless.network
[Match]
Name=wl*

[Network]
DHCP=yes
IPForward=yes
IPv6AcceptRA=yes
IPv6PrivacyExtensions=no

[DHCPv4]
UseDNS=false
RouteMetric=400

[DHCPv6]
UseDNS=false
RouteMetric=400

[Route]
Gateway=_dhcp4

```

> [!note]
> 有线网络和无线网络的 `RouteMetric` 的值是不同的，应该优先使用有线网络。

## 启动服务

使用 `systemctl start systemd-networkd` 启动服务，看配置是否生效，网络是否正常。
在网络正常后，通过下面命令设置开机自启动，完成所有配置。

```shell
systemctl enable --now iwd.service
systemctl enable --now systemd-networkd.service
```

> [!note]
> 在 `/usr/lib/systemd/system/systemd-networkd-wait-online.service` 中 `ExecStart` 后面
> 加上 `--any`。避免因为多个网络接口，导致其他服务延迟。

## DNS

### [[smartdns]]

-. 安装

```shell
# archlinux
sudo pacman -S smartdns
```

-. 简单配置

```conf
# 依赖 smartdns-china-list-git 包
# （可选）引入额外的规则列表，用绝对路径
conf-file /etc/smartdns/anti-ad-smartdns.conf
conf-file /etc/smartdns/accelerated-domains.china.smartdns.conf
conf-file /etc/smartdns/apple.china.smartdns.conf
conf-file /etc/smartdns/google.china.smartdns.conf

# 本地监听端口
bind [::]:53

# 启用测速
speed-check-mode ping,tcp:80,tcp:443

# 完全禁用IPv6
force-AAAA-SOA yes
# 启用双栈优选
#dualstack-ip-selection yes
#dualstack-ip-selection-threshold 15

# log
log-level info
log-size 512M

# 缓存大小
# cache-size 4096
# 持久化缓存
cache-persist yes
# 缓存文件存放位置
cache-file /var/cache/smartdns.cache
# 缓存预获取
prefetch-domain yes
# 过期缓存
serve-expired yes

# server
# 阿里
server-tls dns.alidns.com -group china
# 腾讯
server-tls dot.pub -group china
# 360
server-tls dot.360.cn -group china
# Google
server-tls dns.google
```

-. 启动

```shell
sudo systemctl enable --now smartdns.service
```

## ISSUE

## References

[使用 systemd-networkd 管理网络](https://lisongmin.github.io/os-systemd-networkd/)
<https://linux.cn/lfs/LFS-BOOK-7.7-systemd/chapter07/network.html>
<https://wiki.archlinux.org/title/Dnsmasq>
<https://wiki.archlinux.org/title/Systemd-resolved>
<https://wiki.archlinux.org/title/Systemd-networkd>
