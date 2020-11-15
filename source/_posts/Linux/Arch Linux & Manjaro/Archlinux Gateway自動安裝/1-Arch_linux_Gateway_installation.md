---
title: Archlinux Gateway自動安裝
date: 2020-11-15
tags: [Linux,Arch,Server]
---

## 參考資料

* [Arch Wiki](https://wiki.archlinux.org/) - 非常詳細的Arch Wiki幾乎所有Archlinux的設定都有相關說明,而且很多都有中文翻譯非常好用
* [Archlinux Install Guide](https://wiki.archlinux.org/index.php/Installation_guide_(正體中文)) - 這是中文的官方安裝說明文件,算是安裝的最基礎,實際上就是Arch Wiki的一部分
* [Miro's Blog](https://mirokaku.github.io/Blog/2016/ArchLinux-install-notes/) - 這份Archlinux的安裝筆記寫得很不錯

## Pre-installation Preparation

+ VMware host based on [VMware ESXi](https://www.vmware.com/products/esxi-and-esx.html) 

  - Memory: 1 or 2 GB
  - Processor: 不限制
  - Hard Disk: 20~30 GB
  - NIC: 2 NIC with fixed MAC Address for EXT(pppoe)/INT(static IP)
+ [Archlinux install Image](https://www.archlinux.org/) 

## Installation

採用script自動安裝,安裝前須先確認網路是好的,若是零開始,則需考慮先架設另外的gateway確認網路是好的才有用

#### 開機到chroot前

```bash
zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/arch-install-before-chroot.sh)
```

#### chroot之後

```bash
zsh <(curl -L -s https://raw.githubusercontent.com/Kiwi0093/script/master/arch-install--after-chroot.sh)
```

重開後即完成了