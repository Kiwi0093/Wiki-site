---
title: V2Ray With VLESS安裝
date: 2020-11-26
tags: [Network, V2Ray, VPN]
---

## 前提

因為VMESS被發現有漏洞很容易被偵測,所以V2Ray團隊開發了另一個更輕量的通訊協定 - *VLESS*

另外這個新版的*VLESS*協議因為沒有自身的加密,所以目前都是強制要加掛TLS(其實就是希望把加密都改成依靠TLS達到加密的安全性)

<!--more-->

## 參考資料

[v2fly在Github上的安裝說明](https://github.com/v2fly/fhs-install-v2ray)

[v2fly對於使用VLESS的SOP](https://github.com/v2fly/fhs-install-v2ray/wiki/To-use-the-VLESS-protocol)

[解析 Certbot（Let's encrypt） 使用方式](https://andyyou.github.io/2019/04/13/how-to-use-certbot/)

## 安裝

#### 新版V2Ray安裝

##### Software Installation

因為VLESS已經被包括到新版的V2Ray之中(4.27以後),所以只要安裝新版的V2Ray就可以進行相關設定,一般都會改用變更後的安裝script(for Debian/Ubuntu)

Archlinux可以用pacman直接安裝與更新

```bash
sudo pacman -S v2ray
```

其他的請用下述的script

```bash
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
```

##### Setup與啟動

因為需要證書的關係,所以會建議創建**v2ray**帳號

```bash
sudo useradd -s /usr/sbin/nologin v2ray
```

並且修改**systemd**裡面的Service檔

```bash
sudo systemctl edit v2ray.service
```

內容改為

```c
[Service]
#將nobody改成v2ray
user=v2ray
```

若是使用**systemctl edit**指令無法修改（會變成一片白之類的),請直接使用**vi**指令進行修改如下

```bash
sudo vi /etc/systemd/system/v2ray.service
```

用這個修改的話必須update Service不然不會做數

```bash
#先disable
sudo systemctl disable v2ray.service
#再重新enable
sudo systemctl enable v2ray.service
```

#### Certbot與相關組件安裝

因為VLESS需要跟TLS綁在一起,所以設定檔內需要fullchain.pem & private.pem的ssl key,~~所以以往很方便的caddy自動https作法瞬間變成沒辦法使用~~(應該還是可以只是得要先確定caddy的SSL Key放哪)

一般的教程會採用Certbot+Nginx或是Certbot+Apache的方式進行

##### Certbot安裝

Archlinux

```bash
sudo pacman -S certbot
```

Debian/Ubuntu

```bash
sudo apt install certbot
```

##### python-nginx

這個部份安裝的時候就會連同Nginx/Apache一起安裝了

Archlinux

```bash
sudo pacman -S python3-certbot-nginx
```

Debian/Ubuntu

```bash
sudo apt install python3-certbot-nginx
```

##### Certbot證書簽發與Key

這裡假設都以經將Domain與IP做好連結,所以才能簽發,這也代表VLESS的方案中Domain成為必要的前提條件之一

```bash
# 自動簽發模式for nginx
sudo certbot --nginx

# 自動簽發模式for Apache
sudo cerbot --apache
```

過程中會需要輸入

* Email address
* Domain

沒有異常自動模式會自動啟動Nginx或Apache進行認證,順著跑完就好了

##### 部屬V2Ray用的SSL Key

###### 確認Certbot建立的證書有以下

* `/etc/letsencrypt/live/example.com/fullchain.pem`

* `/etc/letsencrypt/live/example.com/privkey.pem`

example.com是你的Domain

###### Renew證書設定

* Archlinux

  利用`systemd timer`進行renewal

  先建立一個service for renewal

  ```bash
  sudo vi /lib/systemd/system/certbot.service
  ```

  ```c
  [Unit]
  Description=Certbot
  Documentation=file:///usr/share/doc/python-certbot-doc/html/index.html
  Documentation=https://letsencrypt.readthedocs.io/en/latest/
  [Service]
  Type=oneshot
  ExecStart=/usr/bin/certbot -q renew
  PrivateTmp=true
  ```

  之後在做一個timer檔讓systemd定時執行certbot.service

  ```bash
  sudo vi /etc/systemd/system/timers.target.wants/certbot.timer
  ```

  ```c
  [Unit]
  Description=Run certbot twice daily
  
  [Timer]
  OnCalendar=*-*-* 00,12:00:00
  RandomizedDelaySec=43200
  Persistent=true
  
  [Install]
  WantedBy=timers.target
  ```

  然後執行

  ```bash
  sudo systemctl enable certbot.timer
  ```

  ```bash
  sudo systemctl start certbot.timer
  ```

* Debian

  安裝Certbot後已經自動建好了上面的service/timer,所以不用自己來

###### 建立V2Ray專用目錄

```bash
sudo install -d -o v2ray -g v2ray /etc/ssl/v2ray/
```

###### 部屬到專用目錄

```bash
sudo install -m 644 -o v2ray -g v2ray /etc/letsencrypt/live/example.com/fullchain.pem -t /etc/ssl/v2ray/
sudo install -m 600 -o v2ray -g v2ray /etc/letsencrypt/live/example.com/privkey.pem -t /etc/ssl/v2ray/
```

###### 建立Renew後的自動script

```bash
sudo vi /etc/letsencrypt/renewal-hooks/deploy/v2ray.sh
```

其內容如下

```bash
#!/bin/bash

V2RAY_DOMAIN='example.com'

if [[ "$RENEWED_LINEAGE" == "/etc/letsencrypt/live/$V2RAY_DOMAIN" ]]; then
    install -m 644 -o v2ray -g v2ray "/etc/letsencrypt/live/$V2RAY_DOMAIN/fullchain.pem" -t /etc/ssl/v2ray/
    install -m 600 -o v2ray -g v2ray "/etc/letsencrypt/live/$V2RAY_DOMAIN/privkey.pem" -t /etc/ssl/v2ray/

    sleep "$((RANDOM % 2048))"
    systemctl restart v2ray.service
fi
```

不要忘記修改example.com為自己的DN

之後給予權限

```bash
sudo chmod +x /etc/letsencrypt/renewal-hooks/deploy/v2ray.sh
```

### BBR

* Archlinux

  [Archlinux開啟BBR](https://marskid.net/2017/12/03/arch-linux-open-google-bbr/)

  ```bash
  echo "tcp_bbr" > /etc/modules-load.d/80-bbr.conf
  echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.d/80-bbr.conf
  echo "net.core.default_qdisc=fq" >> /etc/sysctl.d/80-bbr.conf
  ```

  接著

  ```bash
  sysctl -p
  ```

* Debian

  Debian9/10有內建BBR只要打開就可以了

  ```bash
  echo "net.core.default_qdisc=fq" >> /etc/sysctl.conf
  echo "net.ipv4.tcp_congestion_control=bbr" >> /etc/sysctl.conf
  ```

  接著

  ```bash
  sysctl -p
  ```

  