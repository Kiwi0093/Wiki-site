---
title: 整合型Archlinux安裝Script - arch.sh說明
date: 2020-12-04
tags: [Linux, Archlinux]
---

## 使用前

* 先確認用LiveCD開起來的系統有網路

* 用`host raw.githubusercontent.com`確認是否可以連上script,不行的話請設定`/etc/hosts`或DNS設定

* Script的直接位置如下：

  [https://raw.gitubusercontent.com/Kiwi0093/script/master/arch.sh](https://raw.gitubusercontent.com/Kiwi0093/script/master/arch.sh)

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

這個script default只有顯示顏色是寫死的,可以參考Script語法說明裡面的說明修改顏色,或是自己修改的時候增加變數設定

#### 設定時區

```bash
#start ntp
echo -e "${COLOR1}Starting NTP Service${NC}"
timedatectl set-ntp true
echo -e "${COLOR2}NTP Setup Completed${NC}"
```

#### 設定Mirror list

```bash
#Modify Mirrorlist to setting country
echo -e "${COLOR1}Starting Modify mirrorlist to China servers${NC}"
echo -n "${COLOR1}Please Select the country you want to set for mirror list\nC)China\nT)Taiwan\n*)whatever..I don't care\n${NC}"
while :
do
	read COUNTRY
	case $ COUNTRY in
		C)
			echo -e "${COLOR2}Set China${NC}"
			sed -i '/Score/{/China/!{n;s/^/#/}}' /etc/pacman.d/mirrorlist
			break
			;;
		T)
			echo -e "${COLOR2}SetTaiwan${NC}"
			sed -i '/Score/{/Taiwan/!{n;s/^/#/}}' /etc/pacman.d/mirrorlist
			break
			;;
		*)
			echo -e "${COLOR2}Keep original Setting${NC}"
			break
			;;
	esac
done
echo -e "${COLOR2}Completed${NC}"
```

由於Archlinux內建沒有`pacman-mirrors`指令可以使用,所以只能採用官方的方式用`sed`去處理`/etc/pacman.d/mirrorlist`達到設定*mirror list*的內容

後面加上的`pacman -Syyu`主要是用來更新mirror list的使用所下的指令

#### Fdisk & Mount

```bash
#Fdisk
echo -e "${COLOR1}Partition your HDD please create 1 data as sda1 and 1 swap as sda2${NC}"
fdisk /dev/sda
echo -e "${COLOR2}Partition Setup Completed${NC}"

#Format
echo -e "${COLOR1}Format /dev/sda1 as EXT4 format${NC}"
mkfs.ext4 /dev/sda1
echo -e "${COLOR2}EXT4 formatting Completed${NC}"
echo -e "${COLOR1}Format /dev/sda2 as Linux Swap${NC}"
mkswap /dev/sda2
echo -e "${COLOR2}Swap Formatting Completed${NC}"
echo -e "${COLOR1}Mount /dev/sda1 to /mnt${NC}"
mount /dev/sda1 /mnt
echo -e "${COLOR2}Completed${NC}"
echo -e "${COLOR1}Mount Swap${NC}"
swapon /dev/sda2
echo -e "${COLOR2}Completed${NC}"
```

這裡是為什麼這個script無法成為萬用script的主要原因之一,因為這裡的**_format_**部份寫死了HDD分割後的定義與mount,使其失去彈性

加以因為所有使用這個script的場合都是在**_VMware ESXi_**下安裝*Archlinux guest*的關係,所以採用簡單的`fdisk`取代其他類型分割軟體,也是在這裡等於限死了後面的`grub`得安裝在*MBR*內而非現在流行的*UFEI*

script中採用最常見但是效率不是最好的ext4格式作為這個archlinux的系統格式,喜歡的話可以自己改成自己喜歡的方式進行

#### 安裝

```bash
#Install
echo -e "${COLOR1}Starting Install Archlinux into /mnt${NC}"
echo -e "${COLOR1}Please select your CPU vendor and Linux Kernel you want:\n1)Intel+Linux\n2)Intel+Linux-LTS\n3)Amd+Linux\n4)Amd+Linux-LTS\n5)Linux-LTS Kernel\n*)Whatever, Normal Linux Kernel is good enough to me\n${NC}"
while :
do
	read CPU
	case $CPU in
	1)
		echo -e "${COLOR2}Linux Kernel＋Intel${NC}"
		pacman -Syyu
		pacstrap /mnt base linux linux-firmware vim zsh curl netctl intel-ucode grub dnsutils open-vm-tools vim
		exit
		;;
	2)
		echo -e "${COLOR2}Linux-LTS Kernel＋Intel${NC}"
		pacman -Syyu
		pacstrap /mnt base linux-lts linux-firmware vim zsh curl netctl intel-ucode grub dnsutils open-vm-tools vim
		exit
		;;
	3)
		echo -e "${COLOR2}Linux Kernel＋Amd${NC}"
		pacman -Syyu
		pacstrap /mnt base linux linux-firmware vim zsh curl netctl amd-ucode grub dnsutils open-vm-tools vim
		exit
		;;
	4)
		echo -e "${COLOR2}Linux-LTS Kernel＋Amd${NC}"
		pacman -Syyu
		pacstrap /mnt base linux-lts linux-firmware vim zsh curl netctl amd-ucode grub dnsutils open-vm-tools vim
		exit
		;;
	5)
		echo -e "${COLOR2}Linux-LTS Kernel${NC}"
		pacman -Syyu
		pacstrap /mnt base linux-lts linux-firmware vim zsh curl netctl grub dnsutils open-vm-tools vim
		exit
		;;
	*)
		echo -e "${COLOR2}Linux Kernel${NC}"
		pacman -Syyu
		pacstrap /mnt base linux linux-firmware vim zsh curl netctl grub dnsutils open-vm-tools vim
		exit
		;;
	esac
done
echo -e "${COLOR2}Completed${NC}"
```

因為這個安裝動作是在Live CD的系統內替HDD安裝package,所以不能用**_pacman_**指令只能用**_pacstrap_**指令package安裝到指定的位置去

`pacstrap /<location> package1 package2 ....`

我們可以利用這個指令把本來要到archchroot內才安裝的程式中的共用部份直接放在這裡直接跑就好了,目前還沒實驗過全部的程式都放在這個地方跑,但是應該部會影響systemd的運作

#### 替新系統建立fstab

```bash
#fstab
echo -e "${COLOR1}Starting Gernerate fstab${NC}"
genfstab -U -p /mnt >> /mnt/etc/fstab
echo -e "${COLOR2}Completed${NC}"
```

因為Archlinux沒有一個所謂的安裝程式會在HDD分割後自動建立fstab到新系統內,所以會使用`genfstab`指令建立後直接送到HDD內的*/etc/*內

#### Copy zsh的設定到新系統

```bash
#Copy Zsh
echo -e "${COLOR1}Starting Copy ZSH setting file to new Archlinux${NC}"
cp -Rv /etc/zsh /mnt/etc/
echo -e "${COLOR2}Completed${NC}"
```

因為我個人蠻喜歡Arch linux Live CD裡面預設的zshrc設定風格,所以就乾脆在安裝的時候會自動copy設定檔,以後所有透過這個安裝的Arch linux就可以有統一個shell sytle.

#### 選擇fetch哪一個安裝script並且進入arch-root

```bash
echo -n "${COLOR1}Please select which type you want\na)Simple Server\nb)Nextcloud Server\nc)V2Ray Server\nd)V2Ray Gateway\ne)Kiwi's private router\n*)I'm the best! let me do by my own!!\n${NC}"
while :
do
	read SCRIPT
	case $SCRIPT in
		a)
			echo -e "${COLOR2}Simple Arch Linux${NC}"
			arch-chroot /mnt /bin/zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/simple_arch.sh)
			exit
			;;
		b)
			echo -e "${COLOR2}Nextcloud Server${NC}"
			arch-chroot /mnt /bin/zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/nextc_arch.sh)
			exit
			;;
		c)
			echo -e "${COLOR2}V2Ray Server${NC}"
			arch-chroot /mnt /bin/zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/arch_v2ray.sh)
			exit
			;;
		d)
			echo -e "${COLOR2}V2Ray Gateway${NC}"
			arch-chroot /mnt /bin/zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/arch_v2ray_gate.sh)
			exit
			;;
		e)
			echo -e "${COLOR2}Kiwi's Private Router${NC}"
			arch-chroot /mnt /bin/zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/arch_v2ray_gate_k.sh)
			exit
			;;
		*)
			echo -e "${COLOR2}Yes! you're the chosen one!${NC}"
			arch-root /mnt /bin/zsh
			exit
			;;
	esac
done
```

由於Arch linux的安裝邏輯是

> 先在Live CD的OS內對HDD進行處理,然後把透過網路把新的archlinux的系統裝進HDD內,然後chroot到新的系統內,利用新的系統的東西,進行初步的設定與啟動設定等動作,最後再完全退出重開改以HDD進行開機這樣算是完成了一個新的系統的安裝

所以做成選項自動在進入arch-chroot後執行該跑得script,或是空的進入arch-chroot後讓人自己處理

#### 進入Arch-chroot與離開後自動重開機

```bash
echo -e "${COLOR2}Finished Installation, will reboot, Good luck${NC}"

# Reboot
echo -e "${COLOR2}Rebooting...${NC}"
reboot
```

### 完整內容

```bash
#------------------------------------------------------------------------------
#(從Iso boot後直到完成change root內所有安裝/調整動作)
#------------------------------------------------------------------------------
#!/bin/zsh
#Parmeter Pre-Define
COLOR1='\e[94m'
COLOR2='\e[32m'
NC='\e[0m'

#start ntp
echo -e "${COLOR1}Starting NTP Service${NC}"
timedatectl set-ntp true
echo -e "${COLOR2}NTP Setup Completed${NC}"

#Modify Mirrorlist to setting country
echo -e "${COLOR1}Starting Modify mirrorlist to China servers${NC}"
echo -n "${COLOR1}Please Select the country you want to set for mirror list\nC)China\nT)Taiwan\n*)whatever..I don't care\n${NC}"
while :
do
	read COUNTRY
	case $COUNTRY in
		C)
			echo -e "${COLOR2}Set China${NC}"
			sed -i '/Score/{/China/!{n;s/^/#/}}' /etc/pacman.d/mirrorlist
			break
			;;
		T)
			echo -e "${COLOR2}SetTaiwan${NC}"
			sed -i '/Score/{/Taiwan/!{n;s/^/#/}}' /etc/pacman.d/mirrorlist
			break
			;;
		*)
			echo -e "${COLOR2}Keep original Setting${NC}"
			break
			;;
	esac
done
echo -e "${COLOR2}Completed${NC}"

#Fdisk
echo -e "${COLOR1}Partition your HDD please create 1 data as sda1 and 1 swap as sda2${NC}"
fdisk /dev/sda
echo -e "${COLOR2}Partition Setup Completed${NC}"

#Format
echo -e "${COLOR1}Format /dev/sda1 as EXT4 format${NC}"
mkfs.ext4 /dev/sda1
echo -e "${COLOR2}EXT4 formatting Completed${NC}"
echo -e "${COLOR1}Format /dev/sda2 as Linux Swap${NC}"
mkswap /dev/sda2
echo -e "${COLOR2}Swap Formatting Completed${NC}"
echo -e "${COLOR1}Mount /dev/sda1 to /mnt${NC}"
mount /dev/sda1 /mnt
echo -e "${COLOR2}Completed${NC}"
echo -e "${COLOR1}Mount Swap${NC}"
swapon /dev/sda2
echo -e "${COLOR2}Completed${NC}"

#Install
echo -e "${COLOR1}Starting Install Archlinux into /mnt${NC}"
echo -e "${COLOR1}Please select your CPU vendor and Linux Kernel you want:\n1)Intel+Linux\n2)Intel+Linux-LTS\n3)Amd+Linux\n4)Amd+Linux-LTS\n5)Linux-LTS Kernel\n*)Whatever, Normal Linux Kernel is good enough to me\n${NC}"
while :
do
	read CPU
	case $CPU in
	1)
		echo -e "${COLOR2}Linux Kernel＋Intel${NC}"
		pacman -Syyu
		pacstrap /mnt base linux linux-firmware vim zsh curl netctl intel-ucode grub dnsutils open-vm-tools vim
		break
		;;
	2)
		echo -e "${COLOR2}Linux-LTS Kernel＋Intel${NC}"
		pacman -Syyu
		pacstrap /mnt base linux-lts linux-firmware vim zsh curl netctl intel-ucode grub dnsutils open-vm-tools vim
		break
		;;
	3)
		echo -e "${COLOR2}Linux Kernel＋Amd${NC}"
		pacman -Syyu
		pacstrap /mnt base linux linux-firmware vim zsh curl netctl amd-ucode grub dnsutils open-vm-tools vim
		break
		;;
	4)
		echo -e "${COLOR2}Linux-LTS Kernel＋Amd${NC}"
		pacman -Syyu
		pacstrap /mnt base linux-lts linux-firmware vim zsh curl netctl amd-ucode grub dnsutils open-vm-tools vim
		break
		;;
	5)
		echo -e "${COLOR2}Linux-LTS Kernel${NC}"
		pacman -Syyu
		pacstrap /mnt base linux-lts linux-firmware vim zsh curl netctl grub dnsutils open-vm-tools vim
		break
		;;
	*)
		echo -e "${COLOR2}Linux Kernel${NC}"
		pacman -Syyu
		pacstrap /mnt base linux linux-firmware vim zsh curl netctl grub dnsutils open-vm-tools vim
		break
		;;
	esac
done
echo -e "${COLOR2}Completed${NC}"

#fstab
echo "${COLOR1}Starting Gernerate fstab${NC}"
genfstab -U -p /mnt >> /mnt/etc/fstab
echo -e "${COLOR2}Completed${NC}"

#Copy Zsh
echo "${COLOR1}Starting Copy ZSH setting file to new Archlinux${NC}"
cp -Rv /etc/zsh /mnt/etc/
echo -e "${COLOR2}Completed${NC}"

echo -n "${COLOR1}Please select which type you want\na)Simple Server\nb)Nextcloud Server\nc)V2Ray Server\nd)V2Ray Gateway\ne)Kiwi's private router\n*)I'm the best! let me do by my own!!\n${NC}"
while :
do
	read SCRIPT
	case $SCRIPT in
		a)
			echo -e "${COLOR2}Simple Arch Linux${NC}"
			arch-chroot /mnt /bin/zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/simple_arch.sh)
			break
			;;
		b)
			echo -e "${COLOR2}Nextcloud Server${NC}"
			arch-chroot /mnt /bin/zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/nextc_arch.sh)
			break
			;;
		c)
			echo -e "${COLOR2}V2Ray Server${NC}"
			arch-chroot /mnt /bin/zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/arch_v2ray.sh)
			break
			;;
		d)
			echo -e "${COLOR2}V2Ray Gateway${NC}"
			arch-chroot /mnt /bin/zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/arch_v2ray_gate.sh)
			break
			;;
		e)
			echo -e "${COLOR2}Kiwi's Private Router${NC}"
			arch-chroot /mnt /bin/zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/arch_v2ray_gate_k.sh)
			break
			;;
		*)
			echo -e "${COLOR2}Yes! you're the chosen one!${NC}"
			arch-root /mnt /bin/zsh
			break
			;;
	esac
done
echo -e "${COLOR2}Finished Installation, will reboot, Good luck${NC}"

# Reboot
echo -e "${COLOR2}Rebooting...${NC}"
reboot
```

