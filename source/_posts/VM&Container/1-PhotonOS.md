---
title: Photon OS 4.0 GA
tags: [VM, Container]
---

# Installation

1. [下載Image ISO](https://github.com/vmware/photon/wiki/Downloading-Photon-OS)
2. 然後在VMware workstation或VMware Esxi上建立新的VM(選擇VMware PhotonOS 64 bits或是Linux Kernel最新的64bits都可以)
3. 用光碟開機後依照步驟做就好了

基本上這個系統需要的RAM跟空間很小,但是你會需要RAM跟空間來跑Docker.所以在建立的時候請考慮你要使用的服務數量來設定你的RAM跟空間

# Basic Setup

## 確認基本服務

```bash
#看一下基本的系統服務是否有開啟,例如open-vm-tools
systemctl status vmtoolsd.service
```

## 帳號設定

安裝的時候就會設定root密碼了,可以利用useradd再增加一般使用者(為了安全性)

```bash
#增加使用者
useradd -m -g root $(your_id)
#替你的帳號設定密碼
passwd $(your_id)
```

因為使用上還是很常會需要root權限,所以可以依照個人喜好決定是否要建立額外帳號然後登入後用`su`或是`sudo`或是直接修改/etc/ssh/sshd_config改成允許root ssh登入都可以

## 網路設定

### Systemd-Networkd

#### 基本規則

參考網頁[systemd-networkd@ArchWiKi](https://wiki.archlinux.org/title/systemd-networkd)

安裝後的系統預設是dhcp開啟的狀態,若是只要單ip並且有另外的dhcpd加上Mac Address控制的話是可以不用動,有其他需求的需要去修改以下檔案

#### 關閉DHCP

```bash
#/etc/systemd/network/99-dhcp-en.network
--------------------------------------------------------------------------------------------------------------------------------
[match]
Name=e*

[Network]
DHCP=yes	#這個改成no來關閉dhcp
IPv6AcceptRA=no
```

#### 固定IP設定方法

```bash
#/etc/systemd/network/10-static-en.network
--------------------------------------------------------------------------------------------------------------------------------
[Match]
Name=ens160								#網卡名稱

[Network]
Address=10.1.10.9/24					#IP位置,可重複多次定義多個IP
Gateway=10.1.10.1						#Gateway IP
DNS=10.1.10.1							#DNS IP
# NTP設定,用空白隔開不同Server
NTP=tock.stdtime.gov.tw watch.stdtime.gov.tw time.stdtime.gov.tw clock.stdtime.gov.tw tick.stdtime.gov.tw
# 關閉IPV6
LinkLocalAddressing=no
IPv6AcceptRA=no
```

#### 設定網路介面名稱

```bash
#/etc/systemd/network/10-ethusb0.link
--------------------------------------------------------------------------------------------------------------------------------
[Match]
MACAddress=00:00:00:00:00:00			#卡號

[Link]
Description=USB to Ethernet Adapter		#這張網路卡的說明
Name=ethusb0							#介面名字
```

想建立多張網卡的話就建立三個檔案分開定義卡號就好了

## 升級系統packages

```bash
yum update
```

## 安裝程式package

```bash
yum install -y vim #安裝vim
```

## 尋找package

```bash
yum search python #尋找所有名字裡有python的package
```

## 掛載SMB分享的檔案

基本上會把docker的volume檔案夾先mount上NAS上的備份用資料夾,這樣可以

1. 簡單備份
2. 減少PhotonOS的HDD空間需求
3. 針對某些需要NAS上檔案的docker服務可以直接access

[參考資料](https://wiki.centos.org/zh-tw/TipsAndTricks/WindowsShares)

### Cifs-utils

基本上我的音樂檔案都是放在NAS上面,雖然可以透過NFS掛在機器上,但是同理也可以用SMB/Cifs掛載

在PhotonOS上可以利用yum安裝

```bash
yum install -y cifs-utils
```

然後修改加入

```bash
#/etc/fstab
--------------------------------------------------------------------------------------------------------------------------------
\\winbox\getme /mnt/win cifs user,uid=500,rw,noauto,suid,credentials=/root/secret 0 0
```

以及

```bash
/root/secert
--------------------------------------------------------------------------------------------------------------------------------
username=sushi	#SMB ID
password=yummy	#SMB ID的Password
```

記得`/root/secert`的權限改成0400

### Cifs-utils + autofs

這個可以自動加卸載

```bash
yum install -y autofs
```

增加到

```bash
#/etc/auto.master
--------------------------------------------------------------------------------------------------------------------------------
/mymount /etc/auto.mymount
```

然後編輯

```bash
#/etc/auto.mymount
--------------------------------------------------------------------------------------------------------------------------------
winbox  -fstype=cifs,rw,noperm,credentials=/root/secret.txt ://winbox/getme
```

有任何使用到/mymount的動作就會自動掛上
# Basic Tools

### Docker-compose

```bash
#安裝python-pip
yum install python3-pip
#安裝docke-compose
pip3 install docker-compose
```

### Docker

在開始之前,最好先規畫一下各docker的外部儲存資料要怎麼規劃,看是使用docker volume管理

還是自己建立位置手動管理都可以,因為我後面會用portainer來管理docker,所以使用volume管理的方式應該比較簡單(在GUI看起來的話)

#### Portainer-ce

這是一個web-GUI,用來管理docker的,雖然說可以直接用cli管理就好了,但是有Web-GUI也是很方便的所以就裝了

他有單機用也可以管理docker-swarm跟 k8s,我沒那麼多node(那堆VPS for v2ray....)所以簡單的單機就可以了

參考資料[Portainer Deployment@Docker Hub](https://documentation.portainer.io/v2.0/deploy/ceinstalldocker/)

```bash
#建立Volume
docker volume create portainer_data
#部屬portainer-ce
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

這裡可以注意

* `-d` 是在背景跑

* `-p 8000:8000 -p 9000:9000`

  這個-p定義可以是 -p `主機ip` : `主機port` : `container port`

* `--name=` 定義你的container要叫什麼名字

* `--restart=` 定義重新啟動的規則,有

  - `no`不重新啟動
  - `on-failure[:最多次數]`只有沒有fail才重新啟動,後面的最多次數是最多重試次數
  - `always`總是重啟
  - `unless-stopped`  除非停止不然都會重啟

* `-v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce`

  這是兩個指令

  - `-v /var/run/docker.sock:/var/run/docker.sock`
  
    這個是利用-v把系統的/var/run/docker.sock對應到container裡的/var/run/docker.sock,這樣裡面的service就可以抓到外面的docker正在跑的狀態
  
  - `-v portainer_data:/data portainer/portainer-ce`
  
  ​       這裡是利用建立好的volume直接使用,若要自己定義位置,也可以就是把前面的部分定義成絕對位置的目錄也可以,然後就是寫入權限要滿足不然寫不進去
  

#### Certbot

##### Install

參考[Certbot的Doc](https://certbot.eff.org/docs/install.html#running-with-docker)

```bash
sudo docker run -it --rm --name certbot -v "/etc/letsencrypt:/etc/letsencrypt" -v "/var/lib/letsencrypt:/var/lib/letsencrypt" \
            certbot/certbot certonly
```

想要對於指定IP有不同Domain的話可以再加上`-p 你要的ip:80:80 -p 你要的ip:443:443`這一個參數這樣就可以對應了

##### Auto Renew

在crontab裡面加上

```bash
0 0 * * * docker run --rm -v /etc/letsencrypt:/etc/letsencrypt -ti certbot/certbot renew
```

就好了

