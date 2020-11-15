---
title: Mirrorlist選用
tags: [Linux, Arch,Manjaro]
---

##### 參考網頁

[FOSS Linux](https://www.fosslinux.com/4252/how-to-find-mirror-list-and-set-fastest-download-server-on-manjaro.htm)

##### By Country選擇

```Bash
sudo pacman-mirrors --country <County Name>
```

這個指令會依照選定的Country的server列表進行速度確認建立mirror-list

##### By 速度選擇

```Bash
sudo pacman-mirrors --fasttrack <數量>
```

