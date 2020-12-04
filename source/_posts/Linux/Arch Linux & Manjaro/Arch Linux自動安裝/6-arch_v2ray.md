---
title: 整合型Archlinux安裝Script - arch_v2ray.sh說明
date: 2020-12-04
tags: [Linux, Archlinux]
---

## 使用前

* 這個script預設是自動帶出來跑的,但是也是可以手動自己跑

* Script的直接位置如下：

  [https://raw.gitubusercontent.com/Kiwi0093/script/master/arch_v2ray.sh](https://raw.gitubusercontent.com/Kiwi0093/script/master/arch_v2ray.sh)

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
echo -e "${COLOR1}請選擇你想要設定的時區\n1)台北\n2)上海\n*)不用管時間隨便\n${NC}"
while:
do
	read ZONE
	case $ZONE in
		1)
			ln -sf /usr/share/zoneinfo/Asia/Taipei /etc/localtime
			hwclock --systohc --utc
			exit
			;;
		2)
			ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
			hwclock --systohc --utc
			exit
			;;
		*)
			hwclock --systohc --utc
			exit
			;;
	esac
done
echo -e "${COLOR2}Completed${NC}"
```

#### 網路設定

```bash
#Network
echo -n "${COLOR1}請輸入你的hostname${NC}"
read HOSTNAME
echo ${HOSTNAME} > /etc/hostname
echo "127.0.0.1 localhost ${HOSTNAME}" >> /etc/hosts
echo -e "${COLOR2}Completed${NC}"

echo -e "${COLOR1}Define your NIC by Mac address${NC}"
echo -n "${COLOR1}請輸入你的MAC Address:\n${NC}"
read OUTSIDE
echo 'SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="'${OUTSIDE}'", NAME="EXT0"' > /etc/udev/rules.d/10-network.rules
echo -e "${COLOR2}Completed${NC}"

echo -e "${COLOR1}Define your IP for EXT0:${NC}"
echo "Description='EXT0 IP SETTING'" > /etc/netctl/EXT0.service
echo "Interface=EXT0" >> /etc/netctl/EXT0.service
echo "Connection=ethernet" >> /etc/netctl/EXT0.service
echo "IP=static" >> /etc/netctl/EXT0.service
echo -n "${COLOR1}請輸入你的IP地址:\n${NC}"
read EXT_IP
echo "Address=('${EXT_IP}/24')" >> /etc/netctl/EXT0.service
echo -n "${COLOR1}請輸入你的Gateway的IP地址:\n${NC}"
read GATE_IP
echo "Gateway='${GATE_IP}'"
echo -n "${COLOR1}請輸入你的DNS的IP:\n${NC}"
read DNS_IP
echo "DNS=('${DNS_IP}')"
echo -e "${COLOR2}Enable EXT0${NC}"
netctl enable EXT0.service
echo -e "${COLOR2}Finished.${NC}"
```

* 這個設定是利用固定的**_Mac Address_**來定義**_NIC Interface_**的名字為**_EXT0_**
* 這個設定是使用`netctl`進行的

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
pacman -Sy screen v2ray certbot python3-certbot-nginx
echo -e "${COLOR2}Completed${NC}"
```

裝上`screen`,`v2ray`, `certbot`,`python3-certbot-nginx`,因為不想直接放`config.json`也不想盜用別人的*一鍵安裝*script所以只有把基本的裝上,其他的設定還是參考其他資料自己手工來吧

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
echo -e "${COLOR1}請選擇你想要設定的時區\n1)台北\n2)上海\n*)不用管時間隨便\n${NC}"
while:
do
	read ZONE
	case $ZONE in
		1)
			ln -sf /usr/share/zoneinfo/Asia/Taipei /etc/localtime
			hwclock --systohc --utc
			exit
			;;
		2)
			ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
			hwclock --systohc --utc
			exit
			;;
		*)
			hwclock --systohc --utc
			exit
			;;
	esac
done
echo -e "${COLOR2}Completed${NC}"

#Network
echo -n "${COLOR1}請輸入你的hostname${NC}"
read HOSTNAME
echo ${HOSTNAME} > /etc/hostname
echo "127.0.0.1 localhost ${HOSTNAME}" >> /etc/hosts
echo -e "${COLOR2}Completed${NC}"

echo -e "${COLOR1}Define your NIC by Mac address${NC}"
echo -n "${COLOR1}請輸入你的MAC Address:\n${NC}"
read OUTSIDE
echo 'SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="'${OUTSIDE}'", NAME="EXT0"' > /etc/udev/rules.d/10-network.rules
echo -e "${COLOR2}Completed${NC}"

echo -e "${COLOR1}Define your IP for EXT0:${NC}"
echo "Description='EXT0 IP SETTING'" > /etc/netctl/EXT0.service
echo "Interface=EXT0" >> /etc/netctl/EXT0.service
echo "Connection=ethernet" >> /etc/netctl/EXT0.service
echo "IP=static" >> /etc/netctl/EXT0.service
echo -n "${COLOR1}請輸入你的IP地址:\n${NC}"
read EXT_IP
echo "Address=('${EXT_IP}/24')" >> /etc/netctl/EXT0.service
echo -n "${COLOR1}請輸入你的Gateway的IP地址:\n${NC}"
read GATE_IP
echo "Gateway='${GATE_IP}'"
echo -n "${COLOR1}請輸入你的DNS的IP:\n${NC}"
read DNS_IP
echo "DNS=('${DNS_IP}')"
echo -e "${COLOR2}Enable EXT0${NC}"
netctl enable EXT0.service
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
pacman -Sy screen v2ray certbot python3-certbot-nginx
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

