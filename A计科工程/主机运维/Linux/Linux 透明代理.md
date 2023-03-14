---
date: 2023-02-21 20:14
tags: TODO, clash, nftables, tproxy
---

# Linux 透明代理

使用 Arch 已经有一段时间了，浏览器使用 pac 来控制代理还是有诸多不便，不同软件配置代理的方式不尽相同。
于是就花点时间配置一下透明代理吧。

主要使用的软件：clash-meta, nftables

## clash-meta 配置

```shell
# 安装
sudo pacman -S clash-meta

# 启动服务
sudo systemctl enable --now clash-meta

# 编辑配置
sudo vim /etc/clash-meta/config.yaml
```

启用透明代理所需要的配置

```conf
...

routing-mark: 1
sniffer:
    enable: true
    sniffing:
        - tls
        - http
tproxy-port: 7893
dns:
    enable: true
    listen: '0.0.0.0:1053'
    enhanced-mode: redir-host
    default-nameserver:
        - 8.8.8.8
    nameserver:
        -
    fallback:
        -
    fallback-filter:
        geoip: true
        geoip-code: CN
        ipcidr:
            - 240.0.0.0/4
...
```

## nftables 配置

```conf
table inet clash
delete table inet clash

table inet clash {
    set ipv4_addr {
        type ipv4_addr
        flags interval
        elements = {
            10.0.0.0/8,
            127.0.0.0/8,
            169.254.0.0/16,
            172.16.0.0/12,
            192.168.0.0/16,
            240.0.0.0/4
        }
    }

    set ipv6_addr {
        type ipv6_addr
        flags interval
        elements = {
            ::ffff:0.0.0.0/96,
            64:ff9b::/96,
            100::/64,
            2001::/32,
            2001:10::/28,
            2001:20::/28,
            2001:db8::/32,
            2002::/16,
            fc00::/7,
            fe80::/10
        }
    }

    chain clash-tproxy {
        fib daddr type { unspec, local, anycast, multicast } return
        ip daddr @ipv4_addr return
        ip6 daddr @ipv6_addr return
        # 不代理 NTP 服务器的端口，
        # 不加上这条规则会导致局域网内的设备无法通过 NTP 服务器同步时间。
        udp dport { 123 } return
        meta l4proto { tcp, udp } meta mark set 1 tproxy to :7893 accept
    }

    chain clash-mark {
        fib daddr type { unspec, local, anycast, multicast } return
        ip daddr @ipv4_addr return
        ip6 daddr @ipv6_addr return
        udp dport { 123 } return
        meta mark set 1
    }

    chain mangle-output {
        type route hook output priority mangle; policy accept;
        meta l4proto { tcp, udp } mark != 1 ct direction original jump clash-mark
    }

    chain mangle-prerouting {
        type filter hook prerouting priority mangle; policy accept;
        iifname { lo } meta l4proto { tcp, udp } ct direction original jump clash-tproxy
    }
}
```
