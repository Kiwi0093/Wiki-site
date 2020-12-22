---
title: INventory - hosts
date: 2020-12-18
tags: [VPS, Linux, Network]
---

## ansible inventory

ansible利用`/etc/ansible/hosts`這個檔案來控制他的連線inventory,這個檔案的格式可以是`[INI]`或是`yml`格式

我個人比較喜歡用[INI]的方式來寫

#### 基本語法

```ini
[tag1]
server1.example.com
[tag2]
server2.example.com
[tag3]
server[a:z].example.com
[tag4]
192.168.0.101
```

上述的意思為有四個`tag`,一個是`tag1`有一台`server1`, `tag2`有一台`server2`,`tag3`有`servera.example.com,serverb.example.com...serverz.example.com`等26台server.`tag4`是ip表示的server

#### 群組 :children

```ini
[group1:children]
tag1
tag2

[tag1]
server1.example.com
[tag2]
server2.example.com
[tag3]
server[a:z].example.com
[tag4]
192.168.0.101
```

有一個群組裡面包括了`tag1`&`tag2`,這個指令可以群組裡面包含群組弄成很多層

#### 變數 :vars

```ini
[group1:children]
tag1
tag2

[tag1]
server1.example.com ansible_user=myuser
[tag2]
server2.example.com
[tag3]
server[a:z].example.com
[tag4]
192.168.0.101

[tag3:vars]
ansible_passwd=mypassword
```

這裡設定了單項變數`ansible_user=myuser`在`server1`以及整體變數`ansible_passwd=mypassword`給`tag3`所有的機器上

#### 參考資料

[Ansible official document - How to build Inventory](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html)

