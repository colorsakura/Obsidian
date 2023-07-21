---
date: 2023-02-21 20:14
tags: TODO, clash, nftables, tproxy
---

# Linux 透明代理

使用 Arch 已经有一段时间了，浏览器使用 `Pac` 来控制代理确实简单方便，但是有很多软件没办法方便地设置代理。所以需要花点时间配置一下透明代理。

主要使用的软件：`clash-meta`, `nftables`

## clash-meta 配置

```shell
# 安装
sudo pacman -S clash-meta

# 启动服务
sudo systemctl enable --now clash-meta

# 编辑配置
sudo vim /etc/clash-meta/config.yaml
```

## 系统路由

```
ip rule add fwmark 1 table 100
ip route add local 0.0.0.0/0 dev lo table 100
```

启用透明代理 clash 所需要的配置

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

### chnroute

```shell
curl 'http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | awk -F\| '/CN\|ipv4/ { printf("%s/%d\n", $4, 32-log($5)/log(2)) }'| sed ':label;N;s/\n/, /;b label'|sed 's/$/& }/g'|sed 's/^/define chnroute_ipv4 = { &/g' > ipv4-chnroute.nft
```

```shell
#!/usr/bin/nft -f

flush ruleset

include "/etc/nftables/ipv4-whitelist.nft"
include "/etc/nftables/ipv4-private.nft"
include "/etc/nftables/ipv4-chnroute.nft"

define DIRECT = {
    $whitelist_ipv4,
    $private_ipv4,
    $chnroute_ipv4,
}

table inet firewall {
  chain incoming {
    type filter hook input priority 0; policy drop;

    # established/related connections
    ct state established,related accept

    # loopback interface
    iifname lo accept

    # icmp: disable ping
    icmp type echo-request drop

    # open tcp ports: sshd (22), httpd (80)
    tcp dport {ssh} accept
  }
}

table inet clash
delete table inet clash

table inet clash {
    chain clash-tproxy {
        # debug
        meta l4proto {tcp, udp} meta nftrace set 1

        meta l4proto { tcp, udp } meta mark set 1 tproxy to :7893 accept
    }

    chain clash-mark {
        meta mark set 1
    }

    chain mangle-output {
        type route hook output priority mangle; policy accept;
        fib daddr type { unspec, local, anycast, multicast } accept
        ip daddr $DIRECT accept
        meta l4proto { tcp, udp } mark != 1 ct direction original jump clash-mark
    }

    chain mangle-prerouting {
        type filter hook prerouting priority mangle; policy accept;
        meta l4proto { tcp, udp } th dport 53 accept
        fib daddr type { unspec, local, anycast, multicast } accept
        ip daddr $DIRECT accept
        iifname { lo } meta l4proto { tcp, udp } ct direction original jump clash-tproxy
    }
}

```


```nft
#!/usr/bin/nft -f

flush ruleset

include "/etc/nftables/ipv4-whitelist.nft"
include "/etc/nftables/ipv4-private.nft"
include "/etc/nftables/ipv4-chnroute.nft"

define DIRECT = {
    $whitelist_ipv4,
    $private_ipv4,
    $chnroute_ipv4,
}

table inet filter {
        chain input {
                 type filter hook input priority 0; policy drop;

                 # accept any localhost traffic
                 iif lo accept

                 # accept traffic originated from us
                 ct state established,related accept

                 # accept neighbour discovery otherwise IPv6 connectivity breaks
                 icmpv6 type { nd-neighbor-solicit, nd-router-advert, nd-neighbor-advert } accept

        }
}

table inet clash
delete table inet clash

table inet clash {
    chain clash-tproxy {
        # Debug
        meta l4proto {tcp, udp} meta nftrace set 1

        fib daddr type { unspec, local, anycast, multicast } return
        ip daddr $DIRECT return
        meta l4proto { tcp, udp } meta mark set 1 tproxy to :7893 accept
    }

    chain clash-mark {
        fib daddr type { unspec, local, anycast, multicast } return
        ip daddr $DIRECT return
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

`sudo nft monitor trace`

```nft
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

```nft
include "/etc/nftables/ipv4-whitelist.nft"
include "/etc/nftables/ipv4-private.nft"
include "/etc/nftables/ipv4-chnroute.nft"

define DIRECT-IPV4 = {
    $whitelist_ipv4,
    $private_ipv4,
    $chnroute_ipv4,
}

table ip v2ray {
    chain PREROUTING {
        type filter hook prerouting priority filter; policy accept;
        ip daddr $DIRECT-IPV4 return

        meta l4proto { tcp, udp } ip daddr 10.12.0.0-10.12.0.255 return
        meta mark 0x000000ff return
        meta l4proto { tcp, udp } meta mark set 0x00000001 tproxy to 127.0.0.1:12345 accept
    }

    chain OUTPUT {
        type route hook output priority filter; policy accept;
        ip daddr $DIRECT-IPV4 return

        meta l4proto { tcp, udp } ip daddr 10.12.0.0-10.12.0.255 return
        meta mark 0x000000ff return
        meta l4proto { tcp, udp } meta mark set 0x00000001 accept
    }
}

table ip v2ray-filter {
    chain DIVERT {
        type filter hook prerouting priority mangle; policy accept;
        meta l4proto tcp socket transparent 1 meta mark set 0x00000001 accept
    }
}
```
