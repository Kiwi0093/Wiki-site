---
title: Manjaro21 for VMware Guest issue
tags: [Linux, Manjaro]
---

# 前言

Manjaro 21 KDE,不知道發了什麼瘋,安裝到VMWare Workstation裡面的時候會有下面兩個問題

* ~~用Free driver & none-Free driver無法開機~~(21.0.7修好了)
* 安裝後沒有VM用的Display driver顯示有問題

<!--more-->

# Boot from USB/ISO

雖然問題解決了,但是補充一下對策

```bash
# 開機的時候進入grub editor把
driver=free 改成 driver=mesa
```

就可以了

# Dispaly issue fix

基本上就是缺安裝`video-virtualmachine`這個Vmware跟virtualbox通吃的driver

```
#先安裝上需要的package
sudo pacman -S virtualbox-guest-utils open-vm-tools
sudo mhwd -i pci video-virtualmachine
sudo systemctl enable vmtoolsd.service
sudo systemctl start vmtoolsd.service
```

理論上這樣就好了