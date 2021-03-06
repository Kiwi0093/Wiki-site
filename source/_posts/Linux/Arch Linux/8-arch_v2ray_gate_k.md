---
title: 整合型Archlinux安裝Script - 8.arch_v2ray_gate_k.sh說明
date: 2021-6-23
tags: [Linux, Archlinux]
---

# *本條目已不再適用,不僅停止維護,原先的script也被移除了*

<!--more-->

## 使用前

* 這個script預設是自動帶出來跑的,但是也是可以手動自己跑

* 這個script是用在有兩張網路卡的條件下的

* Script的直接位置如下：

  [https://Kiwi0093.github.io/script/Arch/arch_v2ray_gate_k.sh](https://Kiwi0093.github.io/script/Arch/arch_v2ray_gate_k.sh)

## Script內容

### Script內容說明

#### 基本定義

```bash
#!/bin/zsh
#Parmeter Pre-Define
COLOR1='\e[94m'
COLOR2='\e[32m'
NC='\e[0m'
```

定義Script中字串的顏色

#### 設定時區與硬體時間

```bash
#change Timezone to CTS(Taipei)
echo -e "${COLOR1}Please select your time zone\n1)Taipei\n2)Shanghai\n*)Whatever..I don't care\n${NC}"
while :
do
	read ZONE
	case $ZONE in
		1)
			echo -e "${COLOR1}Set Time Zone to Asia/Taipei${NC}"
			ln -sf /usr/share/zoneinfo/Asia/Taipei /etc/localtime
			hwclock --systohc --utc
			break
			;;
		2)
			echo -e "${COLOR1}Set Time Zone to Asia/Shanghai${NC}"
			ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
			hwclock --systohc --utc
			break
			;;
		*)
			echo -e "${COLOR1}Nobody cares the local time!!${NC}"
			hwclock --systohc --utc
			break
			;;
	esac
done
echo -e "${COLOR2}Completed${NC}"
```

#### 網路設定

##### Hostname設定

```bash
#Hostname
echo -e "${COLOR1}Please input your hostname\n${NC}"
read HOSTNAME
echo ${HOSTNAME} > /etc/hostname
echo "127.0.0.1 localhost ${HOSTNAME}" >> /etc/hosts
echo -e "${COLOR2}Completed${NC}"
```

##### 輸入卡號定義NIC名稱

```bash
#Set Mac Address
echo -e "${COLOR1}Define your NIC by Mac address${NC}"
echo -e "${COLOR1}Please input your EXT Mac Address:\n${NC}"
read OUTSIDE
echo 'SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="'${OUTSIDE}'", NAME="EXT0"' > /etc/udev/rules.d/10-network.rules
echo -e "${COLOR1}Please input your INT Mac Address:\n${NC}"
read INSIDE
echo 'SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="'${INSIDE}'", NAME="INT0"' >> /etc/udev/rules.d/10-network.rules
echo -e "${COLOR2}Completed${NC}"
```

* 這個設定是利用固定的**_Mac Address_**來定義**_NIC Interface_**的名字為**_EXT0_**

##### 設定對內固定IP

```bash
# Set INT network
echo -e "${COLOR1}Setting your INT0${NC}"
echo "Description='INT0 IP SETTING'" > /etc/netctl/INT0.service
echo "Interface=INT0" >> /etc/netctl/INT0.service
echo "Connection=ethernet" >> /etc/netctl/INT0.service
echo "IP=static" >> /etc/netctl/INT0.service
echo -e "${COLOR1}Please input your INT IP:\n${NC}"
read INT_IP
echo "Address=('${INT_IP}/24')" >> /etc/netctl/INT0.service
echo -e "${COLOR2}Enable INT0${NC}"
netctl enable INT0.service
echo -e "${COLOR2}Finished.${NC}"
```

* IP請設定在192.168/16的範圍內

##### 設定對外網路,可選固定IP或是PPPOE方式連線

```bash
# Set EXT network
echo -e "${COLOR1}Please select your connection\n1)PPPOE\n2)Static IP\n"
while
do
	read CONNECT
	case $CONNECT in
		1)
			echo -e "${COLOR1}Setting your PPPOE${NC}"
			echo "Description='EXT0 PPPOE SETTING'" > /etc/netctl/EXT0.service
			echo "Interface=EXT0" >> /etc/netctl/EXT0.service
			echo "Connection=pppoe" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Please input your PPPOE Account:\n:${NC}"
			read ISP
			echo "User='${ISP}'" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Please input your PPPOE password:\n${NC}"
			read ISPPW
			echo "Password='${ISPPW}'" >> /etc/netctl/EXT0.service
			echo "ConnectionMode='persist'" >> /etc/netctl/EXT0.service
			echo "UsePeerDNS=false" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Enable EXT0{NC}"
			netctl enable EXT0.service
			break
			;;
		2)
			echo -e "${COLOR1}Setting your Static IP${NC}"
			echo "Description='EXT0 IP SETTING'" > /etc/netctl/EXT0.service
			echo "Interface=EXT0" >> /etc/netctl/EXT0.service
			echo "Connection=ethernet" >> /etc/netctl/EXT0.service
			echo "IP=static" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Please input your IP address:\n${NC}"
			read EXT_IP
			echo "Address=('${EXT_IP}/24')" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Please input Gateway IP address:\n${NC}"
			read GATE_IP
			echo "Gateway='${GATE_IP}'" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Please input DNS IP address:\n${NC}"
			read DNS_IP
			echo "DNS=('${DNS_IP}')" >> /etc/netctl/EXT0.service
			echo -e "${COLOR2}Enable EXT0${NC}"
			netctl enable EXT0.service
			break
			;;
	esac
done
echo -e "${COLOR2}EXT set completed.${NC}"
```

##### 設定Gateway Routing

```bash
#Set Nat
echo -e "${COLOR1}Open package fowrading${NC}"
echo "net.ipv4.ip_forward=1" > /etc/sysctl/30-ipforward.conf
echo -e "${COLOR2}Finished.${NC}"
```

##### iptable設定與systemd

```bash
# iptable script
echo -e "${COLOR1}Create Iptable start script${NC}"
echo "#Natd" > /etc/iptables/iptable.sh
echo "iptables -t nat -A POSTROUTING -s 192.168/16 -j MASQUERADE" >> /etc/iptables/iptable.sh
chmod 750 /etc/iptables/iptable.sh
echo -e "${COLOR2}Finished.${NC}"

# systemd
echo -e "${COLOR1}Create Systemd Service${NC}"
echo "[Unit] > /etc/systemd/system/iptables.service
echo "Description=iptables rules >> /etc/systemd/system/iptables.service
echo " " >> /etc/systemd/system/iptables.service
echo "[Service]" >> /etc/systemd/system/iptables.service
echo "ExecStart=/bin/sh /etc/iptables/iptable.sh" >> /etc/systemd/system/iptables.service
echo " " >> /etc/systemd/system/iptables.service
echo "[Install]" >> /etc/systemd/system/iptables.service
echo "WantedBy=multi-user.target" >> /etc/systemd/system/iptables.service
systemctl enable iptables.service
echo -e "${COLOR2}Finished.${NC}"
```

#### 變更root密碼與建立其他帳號

```bash
#Root Password
echo -e "${COLOR1}設定你的root密碼${NC}"
passwd
chsh -s /bin/zsh
echo -e "${COLOR2}Completed${NC}"

#add User
echo -e "${COLOR1}Add user account:${NC}"
echo -n "${COLOR1}What ID you want:${NC}"
read YOURID
useradd -m -g root -s /bin/zsh ${YOURID}
passwd ${YOURID}
echo -e "${COLOR2}Completed${NC}"

echo -e "${COLOR1}Add $YOURID into sudo list${NC}"
pacman -Syu sudo
echo "${YOURID} ALL=(ALL) ALL" >> /etc/sudoers
echo -e "${COLOR2}Completed${NC}"
```

#### 安裝程式

```bash
#install Tools
echo -e "${COLOR1}Install Packages${NC}"
echo -e "${COLOR1}screen${NC}"
pacman -Sy screen v2ray
echo -e "${COLOR2}Completed${NC}"
```

#### 安裝Bootloader

```bash
#install Bootloader
echo -e "${COLOR1}Install grub Boot Loader into /dev/sda${NC}"
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
echo -e "${COLOR2}Completed${NC}"
```

#### 同步後離開Arch-chroot

```bash
#Finished install
sync
sync
sync
exit
```

### 完整版的script

```bash
#------------------------------------------------------------------------------
#(所有動作都是在change root內完成的)
#------------------------------------------------------------------------------
#!/bin/zsh
#Parmeter Pre-Define
COLOR1='\e[94m'
COLOR2='\e[32m'
NC='\e[0m'

#change Timezone to CTS(Taipei)
echo -e "${COLOR1}Please select your time zone\n1)Taipei\n2)Shanghai\n*)Whatever..I don't care\n${NC}"
while :
do
	read ZONE
	case $ZONE in
		1)
			echo -e "${COLOR1}Set Time Zone to Asia/Taipei${NC}"
			ln -sf /usr/share/zoneinfo/Asia/Taipei /etc/localtime
			hwclock --systohc --utc
			break
			;;
		2)
			echo -e "${COLOR1}Set Time Zone to Asia/Shanghai${NC}"
			ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
			hwclock --systohc --utc
			break
			;;
		*)
			echo -e "${COLOR1}Nobody cares the local time!!${NC}"
			hwclock --systohc --utc
			break
			;;
	esac
done
echo -e "${COLOR2}Completed${NC}"

#Set Mac Address
echo -e "${COLOR1}Define your NIC by Mac address${NC}"
echo -e "${COLOR1}Please input your EXT Mac Address:\n${NC}"
read OUTSIDE
echo 'SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="'${OUTSIDE}'", NAME="EXT0"' > /etc/udev/rules.d/10-network.rules
echo -e "${COLOR1}Please input your INT Mac Address:\n${NC}"
read INSIDE
echo 'SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="'${INSIDE}'", NAME="INT0"' >> /etc/udev/rules.d/10-network.rules
echo -e "${COLOR2}Completed${NC}"

# Set INT network
echo -e "${COLOR1}Setting your INT0${NC}"
echo "Description='INT0 IP SETTING'" > /etc/netctl/INT0.service
echo "Interface=INT0" >> /etc/netctl/INT0.service
echo "Connection=ethernet" >> /etc/netctl/INT0.service
echo "IP=static" >> /etc/netctl/INT0.service
echo -e "${COLOR1}Please input your INT IP:\n${NC}"
read INT_IP
echo "Address=('${INT_IP}/24')" >> /etc/netctl/INT0.service
echo -e "${COLOR2}Enable INT0${NC}"
netctl enable INT0.service
echo -e "${COLOR2}Finished.${NC}"

# Set EXT network
echo -e "${COLOR1}Please select your connection\n1)PPPOE\n2)Static IP\n"
while
do
	read CONNECT
	case $CONNECT in
		1)
			echo -e "${COLOR1}Setting your PPPOE${NC}"
			echo "Description='EXT0 PPPOE SETTING'" > /etc/netctl/EXT0.service
			echo "Interface=EXT0" >> /etc/netctl/EXT0.service
			echo "Connection=pppoe" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Please input your PPPOE Account:\n:${NC}"
			read ISP
			echo "User='${ISP}'" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Please input your PPPOE password:\n${NC}"
			read ISPPW
			echo "Password='${ISPPW}'" >> /etc/netctl/EXT0.service
			echo "ConnectionMode='persist'" >> /etc/netctl/EXT0.service
			echo "UsePeerDNS=false" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Enable EXT0{NC}"
			netctl enable EXT0.service
			break
			;;
		2)
			echo -e "${COLOR1}Setting your Static IP${NC}"
			echo "Description='EXT0 IP SETTING'" > /etc/netctl/EXT0.service
			echo "Interface=EXT0" >> /etc/netctl/EXT0.service
			echo "Connection=ethernet" >> /etc/netctl/EXT0.service
			echo "IP=static" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Please input your IP address:\n${NC}"
			read EXT_IP
			echo "Address=('${EXT_IP}/24')" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Please input Gateway IP address:\n${NC}"
			read GATE_IP
			echo "Gateway='${GATE_IP}'" >> /etc/netctl/EXT0.service
			echo -e "${COLOR1}Please input DNS IP address:\n${NC}"
			read DNS_IP
			echo "DNS=('${DNS_IP}')" >> /etc/netctl/EXT0.service
			echo -e "${COLOR2}Enable EXT0${NC}"
			netctl enable EXT0.service
			break
			;;
	esac
done
echo -e "${COLOR2}EXT set completed.${NC}"

#Set Nat
echo -e "${COLOR1}Open package fowrading${NC}"
echo "net.ipv4.ip_forward=1" > /etc/sysctl/30-ipforward.conf
echo -e "${COLOR2}Finished.${NC}"

# iptable script
echo -e "${COLOR1}Create Iptable start script${NC}"
echo "#Natd" > /etc/iptables/iptable.sh
echo "iptables -t nat -A POSTROUTING -s 192.168/16 -j MASQUERADE" >> /etc/iptables/iptable.sh
chmod 750 /etc/iptables/iptable.sh
echo -e "${COLOR2}Finished.${NC}"

# systemd
echo -e "${COLOR1}Create Systemd Service${NC}"
echo "[Unit] > /etc/systemd/system/iptables.service
echo "Description=iptables rules >> /etc/systemd/system/iptables.service
echo " " >> /etc/systemd/system/iptables.service
echo "[Service]" >> /etc/systemd/system/iptables.service
echo "ExecStart=/bin/sh /etc/iptables/iptable.sh" >> /etc/systemd/system/iptables.service
echo " " >> /etc/systemd/system/iptables.service
echo "[Install]" >> /etc/systemd/system/iptables.service
echo "WantedBy=multi-user.target" >> /etc/systemd/system/iptables.service
systemctl enable iptables.service
echo -e "${COLOR2}Finished.${NC}"

#Root Password
echo -e "${COLOR1}設定你的root密碼${NC}"
passwd
chsh -s /bin/zsh
echo -e "${COLOR2}Completed${NC}"

#add User
echo -e "${COLOR1}Add user account:${NC}"
echo -n "${COLOR1}What ID you want:${NC}"
read YOURID
useradd -m -g root -s /bin/zsh ${YOURID}
passwd ${YOURID}
echo -e "${COLOR2}Completed${NC}"

echo -e "${COLOR1}Add $YOURID into sudo list${NC}"
pacman -Syu sudo
echo "${YOURID} ALL=(ALL) ALL" >> /etc/sudoers
echo -e "${COLOR2}Completed${NC}"

#install Tools
echo -e "${COLOR1}Install Packages${NC}"
echo -e "${COLOR1}screen${NC}"
pacman -Sy screen v2ray
echo -e "${COLOR2}Completed${NC}"

#install Bootloader
echo -e "${COLOR1}Install grub Boot Loader into /dev/sda${NC}"
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
echo -e "${COLOR2}Completed${NC}"

#Finished install
sync
sync
sync
exit
```

