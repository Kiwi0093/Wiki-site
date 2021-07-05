---
title: Calibre-Web
tags: [VM, Container]
---

# 前言

在蘇州的歲月我極度需要電子書（因為不像在台北以前可以買一堆實體書堆在房間）,久了也開始習慣就拿個平板把書都扔進去看,所以一直以來都想弄個電子書管理系統

<!--more-->

# 前置準備

## 目錄結構

## 建立書本資料庫

先使用Calibre在NAS上建立書庫

### /books

使用cifs-utils把NAS上的 Calibre資料庫掛載在PhotonOS的某個位置

```bash
#/etc/fstab
--------------------------------------------------------------------------------------------------------------------------------
#加上
\\$(NAS_IP)\$(Your_dir) /$(where_you_mount) cifs user,uid=$(your_None_root_id),rw,noauto,suid,credentials=/root/secret 0 0
```

### 目錄擁有者與權限

```bash
chmod 755 $(your_Mount_dir)
chown $(your_none_root_id) $(your_mount_dir)
```

## Volume

可以把設定檔掛在NAS上面,同上面的方式

```bash
#/etc/fstab
--------------------------------------------------------------------------------------------------------------------------------
#加上
\\$(NAS_IP)\$(Your_dir) /var/lib/docker/volumes cifs user,uid=$(your_None_root_id),rw,noauto,suid,credentials=/root/secret 0 0
```

然後就不用管了

# Docker-Compose file

```bash
#calibre-web.yml
--------------------------------------------------------------------------------------------------------------------------------
version: '3'

services:

  calibre-web:
    image: technosoft2000/calibre-web
    container_name: calibre-web
    environment:
      - PUID=1000
      - USE_CONFIG_DIR=true
    ports:
      - ${Your_Calibre-Web-IP}:${PORT}:8083
    volumes:
      - /home/docker/books:/books
      - /var/lib/docker/volumes/calibre-app:/calibre-web/app/
      - /var/lib/docker/volumes/calibre-kindle-gen:/calibre-web/kindlegen
      - /var/lib/docker/volumes/calibre-config:/calibre-web/config
    restart: unless-stopped

```

# Nginx相關設定

```bash
location /book {
 proxy_bind $server_addr;
 proxy_pass http://${Your_Calibre-Web-IP}:${PORT};
 proxy_set_header Host $http_host;
 proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
 proxy_set_header X-Scheme $scheme;
 proxy_set_header X-Script-Name /book;  # IMPORTANT: path has NO trailing slash
        }
```

用上述設定放在location這段

# 注意事項

* 加掛的Books最好是已經用calibre建立好的書庫,管理的時候也可以透過桌面板的calibre來增加書籍
* 平時整理書可以用calibre會比Calibre-Web好用
* 沒辦法一個帳號對應多個kindle信箱,有多device的人需要建立多個帳號

