---
title: V2Ray With VLESS安裝與設定
date: 2020-11-26
tags: [Network, V2Ray, VPN]
---

## 前提

因為VMESS被發現有漏洞很容易被偵測,所以V2Ray團隊開發了另一個更輕量的通訊協定 - *VLESS*

另外這個新版的*VLESS*協議因為沒有自身的加密,所以目前都是強制要加掛TLS(其實就是希望把加密都改成依靠TLS達到加密的安全性)

## 參考資料



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
bash <(curl -L -s )
```

##### Setup與啟動

因為需要證書的關係,所以會建議創建**v2ray**帳號

```bash

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

```

Debian/Ubuntu

```

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





## Server設定檔



## Client設定檔

