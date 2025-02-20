---
title: '将 alpine 系统配置为软路由'
author: Gaein nidb
categories:
  - 服务器运维
tags:
  - 笔记
  - Linux
date: 2024-10-06 21:22:00
lastmod: 2025-02-11 21:43:00
---

### 基础工具

安装：

* zsh
* oh-my-zsh
* vim
* 7zip
* git
* curl

### 发行版配置

编辑：`/etc/apk/repositories` ，开启社区仓库

### LAN 侧配置

#### IP 地址

编辑 `/etc/network/interfaces`，加入如下内容（以 lan 口为 `eth1` 为例）：

```
auto eth1
iface eth1 inet static
        address 172.26.0.1
        netmask 255.255.255.0
```

#### DHCP

DHCP 使用 kea。ISC DHCP 已经在 2022 年宣布停止维护，kea 为其继任者。

使用 `apk add kea` 来安装。

编辑 `/etc/kea/kea-dhcp4.conf`：

注：以下内容中使用 `eth1` `172.26.0.1` 作为路由器的 LAN 口网卡和 IP 地址示例，使用 `172.26.0.0/24`（子网掩码 `255.255.255.0`）作为 LAN 子网，使用 `172.26.0.100-172.26.0.200` 作为 DHCP 地址池。

```
diff --git a/kea-dhcp4.conf b/kea-dhcp4.conf
index a26ae75..e9c578a 100644
--- a/kea-dhcp4.conf
+++ b/kea-dhcp4.conf
@@ -31,7 +31,7 @@
         // See section 8.2.4 for more details. You probably want to add just
         // interface name (e.g. "eth0" or specific IPv4 address on that
         // interface name (e.g. "eth0/192.0.2.1").
-        "interfaces": [ ]
+        "interfaces": [ "eth1" ]

         // Kea DHCPv4 server by default listens using raw sockets. This ensures
         // all packets, including those sent by directly connected clients
@@ -149,7 +149,7 @@
         // but it's a lot of writing, so it's easier to do this instead:
         {
             "name": "domain-name-servers",
-            "data": "192.0.2.1, 192.0.2.2"
+            "data": "119.29.29.29"
         },

         // Typically people prefer to refer to options by their names, so they
@@ -159,7 +159,7 @@
         // "name": "domain-name" or "code": 15.
         {
             "code": 15,
-            "data": "example.org"
+            "data": "gaein.cn"
         },

         // Domain search is also a popular option. It tells the client to
@@ -168,7 +168,7 @@
         // foo.mydomain.example.com and if it fails, then as foo.example.com
         {
             "name": "domain-search",
-            "data": "mydomain.example.com, example.com"
+            "data": "cn-tvs-alpine-homerouter"
         },

         // String options that have a comma in their values need to have
@@ -217,31 +217,6 @@
     // Based on the class information, you can then allow or reject clients
     // to use certain subnets, add special options for them or change values
     // of some fixed fields.
-    "client-classes": [
-        {
-            // This specifies a name of this class. It's useful if you need to
-            // reference this class.
-            "name": "voip",
-
-            // This is a test. It is an expression that is being evaluated on
-            // each incoming packet. It is supposed to evaluate to either
-            // true or false. If it's true, the packet is added to specified
-            // class. See Section 12 for a list of available expressions. There
-            // are several dozens. Section 8.2.14 for more details for DHCPv4
-            // classification and Section 9.2.19 for DHCPv6.
-            "test": "substring(option[60].hex,0,6) == 'Aastra'",
-
-            // If a client belongs to this class, you can define extra behavior.
-            // For example, certain fields in DHCPv4 packet will be set to
-            // certain values.
-            "next-server": "192.0.2.254",
-            "server-hostname": "hal9000",
-            "boot-file-name": "/dev/null"
-
-            // You can also define option values here if you want devices from
-            // this class to receive special options.
-        }
-    ],

     // Another thing possible here are hooks. Kea supports a powerful mechanism
     // that allows loading external libraries that can extract information and
@@ -294,14 +269,14 @@
             "id": 1,

             // This is mandatory parameter for each subnet.
-            "subnet": "192.0.2.0/24",
+            "subnet": "172.26.66.0/23",

             // Pools define the actual part of your subnet that is governed
             // by Kea. Technically this is optional parameter, but it's
             // almost always needed for DHCP to do its job. If you omit it,
             // clients won't be able to get addresses, unless there are
             // host reservations defined for them.
-            "pools": [ { "pool": "192.0.2.1 - 192.0.2.200" } ],
+            "pools": [ { "pool": "172.26.67.2 - 172.26.67.100" } ],

             // These are options that are subnet specific. In most cases,
             // you need to define at least routers option, as without this
@@ -312,7 +287,7 @@
                     // For each IPv4 subnet you most likely need to specify at
                     // least one router.
                     "name": "routers",
-                    "data": "192.0.2.1"
+                    "data": "172.26.66.1"
                 }
             ],

@@ -335,31 +310,32 @@
                 // It's a rather simple reservation: just an address and nothing
                 // else.
                 {
-                    "hw-address": "1a:1b:1c:1d:1e:1f",
-                    "ip-address": "192.0.2.201"
+                    "hw-address": "DC:65:55:8A:F6:27",
+                    "ip-address": "172.26.67.1",
+                   "hostname": "h3c-ap"
                 },

                 // This is a reservation for a specific client-id. It also shows
                 // the this client will get a reserved hostname. A hostname can
                 // be defined for any identifier type, not just client-id.
-                {
-                    "client-id": "01:11:22:33:44:55:66",
-                    "ip-address": "192.0.2.202",
-                    "hostname": "special-snowflake"
-                },
+                // {
+                //     "client-id": "01:11:22:33:44:55:66",
+                //     "ip-address": "192.0.2.202",
+                //     "hostname": "special-snowflake"
+                // },

                 // The third reservation is based on DUID. This reservation defines
                 // a special option values for this particular client. If the
                 // domain-name-servers option would have been defined on a global,
                 // subnet or class level, the host specific values take preference.
-                {
-                    "duid": "01:02:03:04:05",
-                    "ip-address": "192.0.2.203",
-                    "option-data": [ {
-                        "name": "domain-name-servers",
-                        "data": "10.1.1.202, 10.1.1.203"
-                    } ]
-                },
+                // {
+                //     "duid": "01:02:03:04:05",
+                //     "ip-address": "192.0.2.203",
+                //     "option-data": [ {
+                //         "name": "domain-name-servers",
+                //         "data": "10.1.1.202, 10.1.1.203"
+                //     } ]
+                // },

                 // The fourth reservation is based on circuit-id. This is an option
                 // inserted by the relay agent that forwards the packet from client
@@ -370,31 +346,31 @@
                 // reservations-global, reservations-in-subnet,
                 // reservations-out-of-pool (subnet specific parameters)
                 // and host-reservation-identifiers (global parameter).
-                {
-                    "client-id": "01:12:23:34:45:56:67",
-                    "ip-address": "192.0.2.204",
-                    "option-data": [
-                        {
-                            "name": "vivso-suboptions",
-                            "data": "4491"
-                        },
-                        {
-                            "name": "tftp-servers",
-                            "space": "vendor-4491",
-                            "data": "10.1.1.202, 10.1.1.203"
-                        }
-                    ]
-                },
+                // {
+                //     "client-id": "01:12:23:34:45:56:67",
+                //     "ip-address": "192.0.2.204",
+                //     "option-data": [
+                //         {
+                //             "name": "vivso-suboptions",
+                //             "data": "4491"
+                //         },
+                //         {
+                //             "name": "tftp-servers",
+                //             "space": "vendor-4491",
+                //             "data": "10.1.1.202, 10.1.1.203"
+                //         }
+                //     ]
+                // },
                 // This reservation is for a client that needs specific DHCPv4
                 // fields to be set. Three supported fields are next-server,
                 // server-hostname and boot-file-name
-                {
-                    "client-id": "01:0a:0b:0c:0d:0e:0f",
-                    "ip-address": "192.0.2.205",
-                    "next-server": "192.0.2.1",
-                    "server-hostname": "hal9000",
-                    "boot-file-name": "/dev/null"
-                },
+                // {
+                //     "client-id": "01:0a:0b:0c:0d:0e:0f",
+                //     "ip-address": "192.0.2.205",
+                //     "next-server": "192.0.2.1",
+                //     "server-hostname": "hal9000",
+                //     "boot-file-name": "/dev/null"
+                // },
                 // This reservation is using flexible identifier. Instead of
                 // relying on specific field, sysadmin can define an expression
                 // similar to what is used for client classification,
@@ -405,10 +381,10 @@
                 //
                 // Note: flexible identifier requires flex_id hook library to be
                 // loaded to work.
-                {
-                    "flex-id": "'s0mEVaLue'",
-                    "ip-address": "192.0.2.206"
-                }
+                // {
+                //     "flex-id": "'s0mEVaLue'",
+                //     "ip-address": "192.0.2.206"
+                // }
                 // You can add more reservations here.
             ]
             // You can add more subnets there.
```

#### DNS

待补充

### WAN 侧配置

#### IP 地址以及上网方式

##### 使用 DHCP

编辑 `/etc/network/interfaces`，加入如下内容（以 wan 口为 `eth0` 为例）：

```
auto eth0
iface eth0 inet dhcp
```

##### 使用 PPPoE

使用 `apk add ppp-pppoe` 安装包。

编辑 `/etc/ppp/peers/isp`，写入如下内容：

```
noipdefault
defaultroute
replacedefaultroute
hide-password
#lcp-echo-interval 30
#lcp-echo-failure 4
noauth
persist
#mtu 1492
#maxfail 0
#holdoff 20
plugin pppoe.so eth0 # WAN 侧网卡
user "ip1**********" # PPPoE 用户名
#usepeerdns          # 使用运营商 DNS
```

编辑 `/etc/ppp/chap-secrets`，写入以下内容：

```
# <PPPoE 用户名> <服务器> <PPPoE 密码>
"ip1**********" * "********"
```

编辑完后可使用 `pon isp` 和 `poff` 来连接和断开 PPPoE。

如果需要自动建立连接，使用 `apk add ifupdown-ng-ppp` 添加包，并编写 `/etc/network/interfaces` 后加入如下内容：

```
auto ppp0
iface ppp0 inet ppp
    provider isp
```

本小节中 `isp` 为 `/etc/ppp/peers/` 下的文件名，可以自行更改。

**注意：** 由于 PPPoE 是将 PPP 协议 “抬旗” 到了 “二层之上、三层之下” 的位置，因此，原本 1500 Mtu 的 Ethernet 内又塞了 PPP，其协议占据了 8 字节，因此对于三层协议来说，Mtu 由 1500 降低到了 1492（典型情况）。此时，由 lan 传入的（Mtu 1500）的超过 PPPoE 网卡的 Mtu 包将无法正常发送，造成 “能 ping 通、能解析却不能打开部分网页” 的现象。**具体解决方案参考防火墙配置中的 PPPoE 小节。**顺便一提，这一做法在 openwrt 的界面中对应的是 “MSS 钳制”。

### NAT、防护墙等中间处理配置

#### sysctl

开启内核相关参数

##### BBR

输入 `modprobe tcp_bbr` ，该命令无输出

编辑 `/etc/sysctl.d/90-tcp-bbr.conf`，写入如下内容：

```
net.core.default_qdisc=fq_codel
net.ipv4.tcp_congestion_control=bbr
```

##### IP Forward

编辑 `/etc/sysctl.d/90-IPv4-forwarding.conf`，写入如下内容：

```
net.ipv4.ip_forward=1
```

完成后使用 `sysctl -p` 生效。

#### 防火墙&NAT

使用 `apk add nftables` 安装防火墙。

编辑 `/etc/nftables.d/90-nat.nft`，写入如下内容：

```nft
#!/usr/sbin/nft -f

table inet nat {
    chain prerouting {
        type nat hook prerouting priority dstnat; policy accept;
    }

    chain postrouting {
        type nat hook postrouting priority srcnat; policy accept;
        oifname "ppp0" masquerade
    }
}
```

编辑 `/etc/nftables.d/90-filter.nft`，写入如下内容：

```nft
#!/usr/sbin/nft -f

table inet filter {
    chain input {
        iifname "eth1" accept
    }

    chain forward {
        type filter hook forward priority filter; policy accept;

        tcp flags syn tcp option maxseg size set rt mtu
    }

    chain output {
        type filter hook output priority filter; policy accept;
    }
}
```

其他的如 ICMP 等内容在 `/etc/nftables.nft` 中已经默认配好，如果喜欢可以移至此文件方便管理。

##### PPPoE 或两侧 Mtu 不同

编辑 `/etc/nftables.d/90-filter.nft`，在 `chain forward` 中加入如下内容：

```nft
tcp flags syn tcp option maxseg size set rt mtu
```

当然，你可能发现了，这一条是对 tcp 协议设置的，不仅需要解协议而且不对其他协议生效。可以将两个网卡的 MTU 设置为相同值，只会在内网损失少量的利用率。

#### 全锥形 NAT

参考：[Chion82/netfilter-full-cone-nat: A kernel module to turn MASQUERADE into full cone SNAT](https://github.com/Chion82/netfilter-full-cone-nat)

#### 其它软件

这里使用：daeuniverse/dae

##### 系统要求

`uname -r` 确保大于 `5.8`

执行：`(zcat /proc/config.gz || cat /boot/config-virt) | grep -E 'CONFIG_(DEBUG_INFO|DEBUG_INFO_BTF|KPROBES|KPROBE_EVENTS|BPF|BPF_SYSCALL|BPF_JIT|BPF_STREAM_PARSER|NET_CLS_ACT|NET_SCH_INGRESS|NET_INGRESS|NET_EGRESS|NET_CLS_BPF|BPF_EVENTS|CGROUPS)=|# CONFIG_DEBUG_INFO_REDUCED is not set'`

```
CONFIG_BPF=y
CONFIG_BPF_SYSCALL=y
CONFIG_BPF_JIT=y
CONFIG_CGROUPS=y
CONFIG_KPROBES=y
CONFIG_NET_INGRESS=y
CONFIG_NET_EGRESS=y
CONFIG_NET_SCH_INGRESS=m
CONFIG_NET_CLS_BPF=m
CONFIG_NET_CLS_ACT=y
CONFIG_BPF_STREAM_PARSER=y
CONFIG_DEBUG_INFO=y
# CONFIG_DEBUG_INFO_REDUCED is not set
CONFIG_DEBUG_INFO_BTF=y
CONFIG_KPROBE_EVENTS=y
CONFIG_BPF_EVENTS=y
```

我使用的 alpine-3.19 满足

参考：[Run on Alpine Linux](https://github.com/daeuniverse/dae/blob/main/docs/en/tutorials/run-on-alpine.md)

`/etc/rc.conf` `rc_cgroup_mode="unified"`

`rc-update add cgroups boot`

`/etc/init.d/sysfs #mount_misc`

##### 安装

`wget https://github.com/daeuniverse/dae-installer/raw/main/installer.sh`

`chmod +x installer.sh`

`mkdir /etc/dae`

```
# load all dae files placed in ./config.d/
include {
    config.d/*.dae
}

global {
    ##### Software options.

    # tproxy port to listen on. It is NOT a HTTP/SOCKS port, and is just used by eBPF program.
    # In normal case, you do not need to use it.
    tproxy_port: 12345

    # Set it true to protect tproxy port from unsolicited traffic. Set it false to allow users to use self-managed
    # iptables tproxy rules.
    tproxy_port_protect: true

    # Set non-zero value to enable pprof.
    pprof_port: 0

    # If not zero, traffic sent from dae will be set SO_MARK. It is useful to avoid traffic loop with iptables tproxy
    # rules.
    so_mark_from_dae: 0

    # Log level: error, warn, info, debug, trace.
    log_level: info

    # Disable waiting for network before pulling subscriptions.
    disable_waiting_network: false

    # Enable fast redirect for local TCP connections. There is a known kernel issue that breaks certain clients/proxies, such as nadoo/glider. Users may enable this experimental option at their own risks.
    enable_local_tcp_fast_redirect: false

    ##### Interface and kernel options.

    # The LAN interface to bind. Use it if you want to proxy LAN.
    # Multiple interfaces split by ",".
    lan_interface: eth1

    # The WAN interface to bind. Use it if you want to proxy localhost.
    # Multiple interfaces split by ",". Use "auto" to auto detect.
    wan_interface: auto

    # Automatically configure Linux kernel parameters like ip_forward and send_redirects. Check out
    # https://github.com/daeuniverse/dae/blob/main/docs/en/user-guide/kernel-parameters.md to see what will dae do.
    auto_config_kernel_parameter: true

    ##### Node connectivity check.

    # Host of URL should have both IPv4 and IPv6 if you have double stack in local.
    # First is URL, others are IP addresses if given.
    # Considering traffic consumption, it is recommended to choose a site with anycast IP and less response.
    #tcp_check_url: 'http://cp.cloudflare.com'
    tcp_check_url: 'http://cp.cloudflare.com,1.1.1.1,2606:4700:4700::1111'

    # The HTTP request method to `tcp_check_url`. Use 'HEAD' by default because some server implementations bypass
    # accounting for this kind of traffic.
    tcp_check_http_method: HEAD

    # This DNS will be used to check UDP connectivity of nodes. And if dns_upstream below contains tcp, it also be used to check
    # TCP DNS connectivity of nodes.
    # First is URL, others are IP addresses if given.
    # This DNS should have both IPv4 and IPv6 if you have double stack in local.
    #udp_check_dns: 'dns.google.com:53'
    udp_check_dns: 'dns.google.com:53,8.8.8.8,2001:4860:4860::8888'

    check_interval: 120s

    # G/oup will switch node only when new_latency <= old_latency - tolerance.
    check_tolerance: 50ms


    ##### Connecting options.

    # Optional values of dial_mode are:
    # 1. "ip". Dial proxy using the IP from DNS directly. This allows your ipv4, ipv6 to choose the optimal path
    #       respectively, and makes the IP version requested by the application meet expectations. For example, if you
    #       use curl -4 ip.sb, you will request IPv4 via proxy and get a IPv4 echo. And curl -6 ip.sb will request IPv6.
    #       This may solve some wierd full-cone problem if your are be your node support that. Sniffing will be disabled
    #       in this mode.
    # 2. "domain". Dial proxy using the domain from sniffing. This will relieve DNS pollution problem to a great extent
    #       if have impure DNS environment. Generally, this mode brings faster proxy response time because proxy will
    #       re-resolve the domain in remote, thus get better IP result to connect. This policy does not impact routing.
    #       That is to say, domain rewrite will be after traffic split of routing and dae will not re-route it.
    # 3. "domain+". Based on domain mode but do not check the reality of sniffed domain. It is useful for users whose
    #       DNS requests do not go through dae but want faster proxy response time. Notice that, if DNS requests do not
    #       go through dae, dae cannot split traffic by domain.
    # 4. "domain++". Based on domain+ mode but force to re-route traffic using sniffed domain to partially recover
    #       domain based traffic split ability. It doesn't work for direct traffic and consumes more CPU resources.
    dial_mode: domain+

    # Timeout to waiting for first data sending for sniffing. It is always 0 if dial_mode is ip. Set it higher is useful
    # in high latency LAN network.
    sniffing_timeout: 100ms

    # TLS implementation. tls is to use Go's crypto/tls. utls is to use uTLS, which can imitate browser's Client Hello.
    tls_implementation: tls

    # The Client Hello ID for uTLS to imitate. This takes effect only if tls_implementation is utls.
    # See more: https://github.com/daeuniverse/dae/blob/331fa23c16/component/outbound/transport/tls/utls.go#L17
    utls_imitate: chrome_auto
}

# dns sniff see:        /etc/dae/config.d/dns.dae
# node and group: see   /etc/dae/config.d/nodes.dae
# routing: see          /etc/dae/config.d/route.dae
```