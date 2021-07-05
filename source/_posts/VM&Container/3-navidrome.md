---
title: Navidrome
tags: [VM, Container]
---

# 前言

音樂,是很重要的,本來是打算用NAS+Foobar2000來管理與播放我的音樂,但是後面發現其實音樂串流server+Web播放界面似乎更實用所以就挑了一個比較漂亮又輕量級的navidrome來使用

<!--more-->

# 前置準備

## 目錄結構

### /music

使用cifs-utils把NAS上的音樂資料庫掛載在PhotonOS的某個位置

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
#navidrome.yml
--------------------------------------------------------------------------------------------------------------------------------
version: "3"
services:
  navidrome:
    image: deluan/navidrome:latest
    container_name: navidrome
    ports:
      - ${Your_navidorme-IP}:${PORT}:4533
    environment:
      ND_SCANSCHEDULE: "1h"
      ND_LOGLEVEL: "info"
      ND_SESSIONTIMEOUT: "24h"
      ND_BASEURL: "/music"
      ND_ENABLETRANSCODINGCONFIG: "true"
      ND_REVERSEPROXYWHITELIST: "0.0.0.0/0"
    volumes:
      - /var/lib/docker/volumes/navidrome:/data
      - /home/docker/music:/music:ro
    restart: unless-stopped
```

設定的部分,若是想要安全性高就不要加上`ND_ENABLETRANSCODINGCONFIG: "true"`這個是可以允許使用者直接定義轉碼內容的

# Nginx相關設定

```bash
location /music {
proxy_pass http://${Your_navidorme-IP}:${PORT};
proxy_set_header Host $host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
proxy_set_header X-Forwarded-Protocol $scheme;
proxy_set_header X-Forwarded-Host $http_host;
proxy_buffering off;
}
```

相關設定很簡單照抄就好了

# 注意事項

* 雖然支援Ultrasonic,但是我自己怎麼試都有問題,一換回subsonic就好了