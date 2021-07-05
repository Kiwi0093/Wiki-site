---
title: Dummynet & IPFW
tags: [FreeBSD]
---

# 前言

FreeBSD Gateway可以利用ipfw來達成流量限制的功能,想要使用這個除了/etc/rc.conf需要設定以外,還需要讓kernel的dummynet啟動才會正常啟動

# Dummynet

## 動態載入

由於現在都是使用Generic的Kernel(減少compile Kernel的動作)所以使用kldload來load dummynet.ko

手動載入

```bash
kldload dummynet
```

想要確認有沒有跑起來請用

```bash
kldstat
```

這個指令會列出所有動態載入的.ko

## 開機自動載入

```
# /boot/loader.conf
dummynet_load="YES"
```

之後重開機的時候就會自動載入了

# ipfw

## /etc/rc.conf

```bash
firewall_enable="YES"
firewall_script="/etc/ipfw.conf"
natd_enable="NO"
```

上述設定的意思就是啟動ipfw,設定檔為/etc/ipfw.conf,並且不啟動natd

## /etc/ipfw.conf

```bash
#定義介面
EXT_IF="ext0"
INT_IF="int0"

#定義網路
DMZ_NET="192.168.0.0/24"

#定義特定IP與名稱
RDP_PROXY="192.168.0.13"

#定義類型與速度
P2P_UP="100KBytes/s"
VPN_UP="500KBytes/s"
RDP_P2P="500KBytes/s"
RDP_BAND="100KBytes/s"
SERV_UP="350KBytes/s"
GUEST_BAND="30KBytes/s"

#流量限制完整設定
#清除規則,並加上全部通行的規則
ipfw -f flush
ipfw -q add 10 allow all from any to any

#使用ipfw pipe來定義每一項流量限制與編號
ipfw pipe 1 config bw $VPN_UP
ipfw pipe 2 config bw $P2P_UP
ipfw pipe 3 config bw $RDP_P2P
ipfw pipe 4 config bw $SERV_UP
ipfw pipe 5 config bw $GUEST_BAND

#利用pipe定義從哪裡到哪裡的流量
ipfw add 20 pipe 1 ip from any to $VPN_NET
ipfw add 25 pipe 3 ip from $P2P1_IP to any 3389 in recv $INT_IF
ipfw add 30 pipe 2 ip from $P2P1_IP to any in recv $INT_IF
ipfw add 40 pipe 2 ip from $P2P2_IP to any in recv $INT_IF
ipfw add 110 pipe 5 ip from $GUEST_NET to any
ipfw add 120 pipe 5 ip from any to $GUEST_NET
ipfw add 210 pipe 4 ip from $DMZ_NET to any via ext0
```

這個規則基本上依照ipfw add 後的數字定義優先權數字越少的規則優先權越高