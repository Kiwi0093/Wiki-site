---
title: Script After Chroot - Kiwi Private Gateway installation
date: 2020-11-15
tags: [Linux,Arch,Server]
---

## 前提

+ 自己要用的連結為: https://github.com/Kiwi0093/script/blob/master/
+ 相較於Normal的版本,這個script是我自己本人使用,所以會把V2ray設定檔這邊改成下載加密的tar檔案後進行解密解壓複製到該去的地方
+ 預安裝程式部分加上openssl

##### 使用openssl加解密

###### 加密

```bash
# 將目錄下的file文件打包壓縮，密碼設定為password
tar -czvf - /path/files | openssl enc -e -aes256 -salt -out /path/files.tar.gz
```

###### 解密

```bash
# 將目錄下的file.tar.gz進行解密解壓
openssl enc -d -aes256 -salt -in /path/files.tar.gz | tar xzvf -
```

* tar的部分

  跟一般的指令一樣,`zcvf`是創建`zxvf`是解開,然後利用`|`做pipe跟openssl一起作用

* openssl的部分
  - `enc` 是指加密編碼
  - `-e` 是加密
  - `-d ` 是解密
  - `-aes256` 是指加密的編碼,這裡採用的是AES256也可以挑其他Openssl支援的編碼使用
  - `-in` 輸入的檔案
  - `-out` 輸出的檔案
  - `-salt` 加入一段字串進行加密

## Script差異內容

```bash
#V2ray config.json get
echo -e "${COLOR1}Fetch V2ray files${NC}"
curl -o v2ray.tar.gz https://raw.githubusercontent.com/Kiwi0093/script/master/v2ray.tar.gz
echo -n "${COLOR1}Please Enter your password:${NC}"
openssl enc -d -aes256 -salt -in v2ray.tar.gz | tar xzvf -
echo -e "${COLOR1}Move config files to /etc/v2ray${NC}"
mv -f ./config.json* /etc/v2ray/
echo -e "${COLOR1}Clean tmp files${NC}"
rm -f ./*.tar.gz
echo -e "${COLOR2}Completed${NC}"
```

