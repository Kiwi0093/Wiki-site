---
title: Ansible Installation
date: 2020-12-18
tag: [Linux, VPS, Network]
---

## Ansible

這是一個Redhat支持開發的**IT Automation**工具, 基礎是python3+ssh

### Push mode

這是Ansible的基本功用由一台安裝Ansible的機器push指令到Inventory的機器上一次性進行所有的同步指令進行管理

#### Ad-Hoc指令

這是一次性的指令基本用法是

```bash
ansible [pattern] -m [module] -a "[module options]"
```

#### Playbook

這個可以視為是事先寫好的script可以讓Ansible直接依照該Playbook去跑複雜的動作進行管理後續會把自己寫的一部分整理起來

### Pull Mode

這個是使用`ansible-pull`指令,將某地的Playbook拉到本機上套用,很適合用來進行單機的自動環境設定,像我這種會日益增加Linux機台的人很適合寫好playbook然後採用**_ansible-pull+Github_**的組合去儲存我的各種機器設定,缺點就是變成每一台機器都得要安裝ansible不然的話無法套用(push mode不用每台都安裝也可以跑)

## 安裝

安裝非常的簡單就我自己常用的Arch系與Debian/Ubuntu系的機器都是一行指令安裝完成

Arch/Manjaro

```bash
sudo pacman -S ansible
```

Debian/Ubuntu

```bash
apt install ansible
```

### 基本連線

```bash
ansible <IP or name> --ask-pass -m [module]
```

若是使用ssh key的話就不需要`--ask-pass`參數來要求手動輸入ssh password

有些Server會check host ssh key,所以可以去ansible.cfg裡面加上`host_key_checking = False`