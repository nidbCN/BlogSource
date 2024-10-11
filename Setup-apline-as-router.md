---
title: '将 alpine 系统配置为软路由'
author: Gaein nidb
categories:
  - 服务器运维
tags:
  - 笔记
  - Linux
date: 2024-10-06 21:22:00
lastmod: 2024-10-06 21:22:00
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

### 网卡 IP

为 LAN 口设置静态地址

为 WAN 口设置 DHCP

编辑 `/etc/network/interfaces`

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
        address 172.26.129.1
        netmask 255.255.255.0
```

### sysctl

开启内核相关参数

#### BBR

`modprobe tcp_bbr`: 无输出

编辑 `/etc/sysctl.conf`

```
net.core.default_qdisc=fq_codel
net.ipv4.tcp_congestion_control=bbr
```

#### IP Forward

```
net.ipv4.ip_forward = 1
```

#### IPv6

暂时没写

### DHCP

`apk add kea`

配置暂时没写

### 分流软件

这里使用：[daeuniverse/dae: eBPF-based Linux high-performance transparent proxy solution.](https://github.com/daeuniverse/dae)

#### 系统要求

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

#### 安装

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